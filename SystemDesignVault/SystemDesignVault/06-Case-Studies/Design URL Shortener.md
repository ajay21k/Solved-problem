---
tags: [system-design, case-study, url-shortener, tinyurl, bitly]
links: [[Databases]], [[Caching]], [[Replication and Sharding]]
---

# 🔗 Design URL Shortener (TinyURL / bit.ly)

> [!abstract] Summary
> Design a URL shortening service: convert a long URL to a short alias, then redirect users to the original URL on click.

---

## 📋 Requirements

### Functional
- ✅ Shorten a long URL → short URL (e.g., `tiny.ly/abc123`)
- ✅ Redirect short URL → original URL
- ✅ Custom aliases (optional)
- ✅ URL expiry (optional)
- ✅ Click analytics (optional)

### Non-Functional
- 100M URLs created/day → ~1200 writes/sec
- 10B redirects/day → ~115,000 reads/sec
- Read:Write = ~10,000:1 (extremely read-heavy)
- Availability > 99.99% (redirects must always work)
- Redirect latency < 10ms

---

## 📐 Capacity Estimation

```
Writes:      100M/day = ~1,200/sec
Reads:       10B/day  = ~115,000/sec

URL record:  ~500 bytes
Storage/yr:  100M × 365 × 500 bytes = ~18 TB/year
             (manageable in a single DB with sharding for 10 years)

Cache:       80/20 rule — 20% of URLs get 80% of traffic
             Cache top 20%: 100M × 0.2 × 500 bytes = ~10 GB/day
             → Fits in Redis easily!
```

---

## 🔑 Short URL Generation

### Option 1: MD5 Hash (❌ Collision-prone)
```
hash = MD5(long_url)[:7]  → 7-char code
Problem: collision if two URLs hash to same code
```

### Option 2: Base62 Encoding ⭐
```
Characters: [a-z, A-Z, 0-9] = 62 chars
Length 6:   62^6 = 56 billion combinations
Length 7:   62^7 = 3.5 trillion combinations

Flow:
1. Generate unique ID (auto-increment counter or Snowflake)
2. Encode ID in Base62 → short code
3. Store: {short_code → long_url}
```

### Option 3: Random String
```
Generate 6-7 random chars, check DB for collision
Retry if collision (rare but possible)
```

### Distributed ID Generation
- Multiple servers need unique IDs without coordination
- Options:
  - **Zookeeper**: Assign ID ranges to servers (Server 1: 1-1M, Server 2: 1M-2M)
  - **Snowflake IDs**: Timestamp + machine ID + sequence
  - **Redis INCR**: Atomic counter in Redis

---

## 🗄️ Database Schema

```sql
CREATE TABLE urls (
  id          BIGINT PRIMARY KEY,        -- auto-increment
  short_code  VARCHAR(10) UNIQUE,
  long_url    TEXT NOT NULL,
  user_id     BIGINT,
  created_at  TIMESTAMP DEFAULT NOW(),
  expires_at  TIMESTAMP,
  click_count BIGINT DEFAULT 0
);

-- Index for fast lookup
CREATE INDEX idx_short_code ON urls(short_code);
```

---

## 🔄 Redirect Flow

```
User clicks tiny.ly/abc123

1. App looks up "abc123" in Redis cache
   CACHE HIT → return 301/302 with Location: long_url
   
2. CACHE MISS → query DB
3. Found → store in Redis (TTL: 1 day)
4. Return HTTP redirect

5. Not found → 404
```

### 301 vs 302 Redirect

| | 301 Permanent | 302 Temporary |
|-|--------------|---------------|
| Browser caches | ✅ Yes | ❌ No |
| Server sees request | First time only | Every time |
| Analytics (server-side) | ❌ Miss repeat visits | ✅ Count every click |
| CDN cacheable | ✅ Yes | ❌ No |

> [!tip] Use 302 if you want accurate analytics. Use 301 to reduce server load.

---

## 📊 Analytics

```
Click event: {short_code, timestamp, referrer, user_agent, ip, country}
→ Kafka topic "url.clicked"
→ Analytics consumer → ClickHouse / BigQuery (for aggregation)
→ Dashboards: clicks/hour, top referrers, geographic distribution
```

---

## 🔑 Key Design Decisions

| Decision | Choice | Reason |
|----------|--------|--------|
| ID generation | Base62 + counter | Simple, no collisions |
| DB | MySQL/Postgres | Simple relational, fits well |
| Cache | Redis | 10GB fits all hot URLs in memory |
| Redirect type | 302 | Analytics accuracy |
| Analytics | Async via Kafka | Don't slow down redirect path |

---

## 🔗 Related Notes
- [[Databases]] — MySQL for URL storage
- [[Caching]] — Redis for hot URL cache
- [[Replication and Sharding]] — Shard by short_code hash at scale

