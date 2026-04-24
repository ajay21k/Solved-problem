---
tags: [system-design, components, caching, redis]
links: [[Databases]], [[CDN]], [[Scalability]]
---

# ⚡ Caching

> [!abstract] Summary
> Caching stores frequently accessed data in fast memory to dramatically reduce latency and database load.

---

## 🏗️ Cache Hierarchy

```
CPU (L1/L2/L3)  →  App Server RAM  →  Distributed Cache  →  Database
   ~1-10 ns           ~100 ns            ~1-5 ms              ~10-100 ms
   (fastest)                                                   (slowest)
```

| Level | Location | Latency | Tool |
|-------|----------|---------|------|
| CPU Cache | On-chip | ~1-10 ns | Hardware |
| In-Process | App memory | ~100 ns | `HashMap`, Guava |
| Distributed | Network cache | ~1-5 ms | **Redis**, Memcached |
| CDN | Edge servers | Varies | Cloudflare, CloudFront |
| DB Query Cache | DB layer | Varies | MySQL Query Cache |

---

## 📖 Cache Reading Strategies

### Cache-Aside (Lazy Loading) ⭐ Most Common
```
App → Check Cache → HIT? → Return data ✅
                  → MISS? → Query DB → Populate Cache → Return data
```
- App controls cache population
- Only cache what's needed
- **Risk**: Cache stampede on cold start

### Read-Through
```
App → Cache → HIT? → Return ✅
           → MISS? → Cache queries DB automatically → Return
```
- Cache handles the miss automatically
- Simpler app code

---

## ✍️ Cache Write Strategies

### Write-Through
```
Write → Cache + DB (simultaneously)
```
| ✅ Pros | ❌ Cons |
|--------|--------|
| Cache always in sync | Slower writes (2 writes) |
| No data loss on crash | Writes unused data |

### Write-Back (Write-Behind)
```
Write → Cache (immediate) → DB (async, later)
```
| ✅ Pros | ❌ Cons |
|--------|--------|
| Very fast writes | Risk of data loss (crash before DB write) |
| Batches DB writes | More complex |

### Write-Around
```
Write → DB only (bypass cache)
Read → Cache MISS → DB → populate cache
```
| ✅ Pros | ❌ Cons |
|--------|--------|
| Avoids caching write-once data | First read always a miss |

---

## 🗑️ Cache Eviction Policies

| Policy | Description | Use Case |
|--------|-------------|----------|
| **LRU** (Least Recently Used) | Evict oldest-accessed item | General purpose ⭐ |
| **LFU** (Least Frequently Used) | Evict least-used item | Long-term access patterns |
| **FIFO** | Evict first-inserted item | Simple scenarios |
| **TTL** | Expire after set time | Time-sensitive data |
| **Random** | Evict random item | Simple, good enough at scale |

---

## 🔴 Redis Deep Dive

### Data Structures
| Structure | Use Case | Example |
|-----------|----------|---------|
| **String** | Simple key-value, counters | Session token, rate limit counter |
| **Hash** | Object fields | User profile `{name, email, age}` |
| **List** | Message queues, timelines | Recent activity feed |
| **Set** | Unique items | Unique visitors |
| **Sorted Set** | Leaderboards, rankings | Game scores |
| **Stream** | Event log | Message queue (like Kafka lite) |
| **Bitmap** | Feature flags, tracking | Daily active users |

### Persistence Options
| Mode | How | Trade-off |
|------|-----|-----------|
| **RDB** (Snapshot) | Periodic snapshots to disk | Fast recovery, potential data loss |
| **AOF** (Append-Only File) | Log every write operation | More durable, larger files |
| **Both** | RDB for backup + AOF for durability | Best of both |
| **None** | Pure in-memory | Fastest, data lost on restart |

---

## ⚔️ Redis vs Memcached

| Feature | Redis | Memcached |
|---------|-------|-----------|
| Data Structures | Rich (lists, sets, hashes, sorted sets) | Key-value only |
| Persistence | ✅ Yes (AOF, RDB) | ❌ No |
| Replication | ✅ Master-Replica | ❌ No |
| Pub/Sub | ✅ Yes | ❌ No |
| Clustering | ✅ Yes | ✅ Yes |
| Threads | Single-threaded (mostly) | Multi-threaded |
| Best For | Everything | Pure high-speed caching |

> [!tip] Choose Redis almost always. Memcached only if you need multi-threading for simple caching.

---

## ⚠️ Cache Problems & Solutions

### Cache Stampede (Thundering Herd)
- Many requests hit DB simultaneously on cache expiry
- **Fix**: Mutex/lock, probabilistic early expiry, background refresh

### Cache Penetration
- Requests for non-existent keys bypass cache every time
- **Fix**: Cache `null` results, Bloom filter to check existence

### Cache Avalanche
- Many cache keys expire at same time → DB overwhelmed
- **Fix**: Add random jitter to TTL values

### Hot Key Problem
- One key gets disproportionate traffic
- **Fix**: Local in-process cache, key replication, read replicas

---

## 🔑 Cache Invalidation

> [!quote] Phil Karlton
> "There are only two hard things in Computer Science: cache invalidation and naming things."

### Strategies
1. **TTL-based**: Expire automatically (simple but stale window)
2. **Event-driven**: Invalidate on write/update events
3. **Version-based keys**: `user:123:v5` — change version = new key
4. **Write-through**: Update cache on every write

---

## 🔗 Related Notes
- [[Databases]] — What you're caching in front of
- [[CDN]] — Caching at the network edge
- [[Rate Limiting]] — Redis used for distributed rate limit counters
- [[Scalability]] — Caching is #1 strategy for read scaling
- [[Design Twitter]] — Timeline caching with Redis sorted sets

