---
tags: [system-design, case-study, twitter, social-feed]
links: [[Caching]], [[Databases]], [[Message Queues]], [[Replication and Sharding]], [[CDN]]
---

# 🐦 Design Twitter / Social Feed

> [!abstract] Summary
> Design a Twitter-like platform supporting tweet posting, following users, a home timeline feed, and notifications at massive scale.

---

## 📋 Requirements

### Functional Requirements
- ✅ Post tweets (text, images, videos)
- ✅ Follow / unfollow users
- ✅ Home timeline (tweets from followed users)
- ✅ User profile & timeline
- ✅ Like, retweet, reply
- ✅ Search tweets
- ✅ Push notifications

### Non-Functional Requirements
- 300M DAU, 500M tweets/day (~6000 tweets/sec)
- Home timeline load < 200ms
- 99.99% availability
- Eventual consistency acceptable for feed

---

## 📐 Capacity Estimation

```
Tweets/day:    500M
Tweets/sec:    ~6,000 writes/sec
Timeline reads/sec: 300M users × 5 reads/day = ~17,000 reads/sec

Tweet size:    ~280 chars × 4 bytes = 1.12 KB
Storage/day:   500M × 1.12 KB = 560 GB/day
Storage/5yr:   ~1 PB (text only)

Media:         ~10% tweets have images → ~50M images/day
               Avg image 200KB → 10 TB/day (use S3 + CDN)
```

---

## 🏗️ High-Level Architecture

```
           ┌──────────────────────────────────────────────────┐
           │                   Clients                         │
           └──────────────────────┬───────────────────────────┘
                                  │
                         [API Gateway / LB]
                                  │
         ┌────────────────────────┼──────────────────────────┐
         │                        │                          │
   [Tweet Service]      [Timeline Service]       [User Service]
         │                        │                          │
    [Cassandra]             [Redis Cache]              [MySQL]
    (tweets store)          (pre-built feeds)        (user data)
         │
    [Kafka Topic: tweets]
         │
    [Fan-out Workers]
```

---

## 📡 Core Services

### Tweet Service
- Store tweets in **Cassandra** (write-heavy, horizontal scale)
- Schema: `(tweet_id, user_id, text, media_urls, created_at, likes_count)`
- Tweet ID: Snowflake ID (timestamp + machine_id + sequence → sortable, globally unique)
- Media → **AWS S3** + **CDN**

### User Service
- User profiles, follow relationships in **MySQL**
- Follow graph also stored as adjacency list in **Redis** for fast lookup

### Timeline Service
- Pre-builds and caches the home feed
- Uses **Redis Sorted Set**: `timeline:{user_id}` → `{tweet_id: timestamp}`

---

## 🔀 Feed Generation: The Hard Problem

### Option 1: Fanout on Write (Push Model)
```
Alice posts tweet → Fan-out Workers → push to all Alice's followers' timeline caches
                                     (immediately pre-built)
```
- ✅ Read is instant (already computed)
- ❌ Expensive if Alice has 100M followers (100M writes per tweet!)

### Option 2: Fanout on Read (Pull Model)
```
Bob opens feed → Fetch tweets from all users Bob follows → Merge & sort → Return
```
- ✅ Write is cheap
- ❌ Read is expensive — must query N users and merge

### Option 3: Hybrid ⭐ (Twitter's actual approach)
```
Regular users (< 10K followers): → Push (fanout on write)
Celebrities (> 10K followers):   → Pull (fanout on read, merged at query time)
```
- Avoids massive fan-out for celebrities
- Most users get fast reads via pre-built feeds

---

## 💾 Storage Choices

| Data | Storage | Reason |
|------|---------|--------|
| Tweets | Cassandra | High write throughput, horizontal scale |
| User profiles | MySQL | Relational, ACID for user data |
| Social graph | MySQL + Redis | MySQL for persistence, Redis for fast lookup |
| Home timeline | Redis Sorted Set | O(log n) inserts, instant reads |
| Media (images/video) | S3 + CDN | Cheap, durable, globally distributed |
| Search index | Elasticsearch | Full-text search on tweet content |

---

## 📬 Notification System

```
Tweet/Like/Follow Event 
    → Kafka (notifications topic)
    → Notification Consumer
    → Check user preferences
    → Push: APNs (iOS) / FCM (Android)
         Email: SendGrid
```

---

## 🔍 Search

- Index tweets into **Elasticsearch** asynchronously (via Kafka consumer)
- Search: full-text on content, hashtags, user handles
- Trending topics: Count hashtag occurrences in sliding window (Redis)

---

## 🔑 Key Design Decisions

| Decision | Choice | Reason |
|----------|--------|--------|
| Tweet storage | Cassandra | Write-heavy, horizontal scale |
| Feed model | Hybrid push/pull | Balance for celebrity accounts |
| Tweet IDs | Snowflake | Sortable, globally unique, no central bottleneck |
| Real-time feed | Kafka + fan-out workers | Async, decoupled, scalable |
| Media | S3 + CDN | Cost-efficient at scale |

---

## 🔗 Related Notes
- [[Caching]] — Redis for timeline cache
- [[Databases]] — Cassandra for tweets, MySQL for users
- [[Message Queues]] — Kafka for fan-out events
- [[CDN]] — Serve media globally
- [[Replication and Sharding]] — Cassandra sharding strategy

