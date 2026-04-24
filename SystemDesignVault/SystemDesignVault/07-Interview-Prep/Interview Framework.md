---
tags: [system-design, interview, framework, estimation]
links: [[Key Concepts]], [[Scalability]], [[🗺️ MOC — System Design]]
---

# 🎯 System Design Interview Framework

> [!abstract] Summary
> A structured approach to ace any system design interview — RESHADED framework, estimation cheat sheet, and common pitfalls.

---

## 🗺️ The RESHADED Framework

| Step | What | Time (45 min) |
|------|------|----------------|
| **R** — Requirements | Functional + non-functional | 3-5 min |
| **E** — Estimation | Scale, storage, bandwidth | 3-5 min |
| **S** — Storage | DB choice, schema | 3 min |
| **H** — High-Level Design | Components diagram, data flow | 10 min |
| **A** — API Design | Key endpoints / contracts | 5 min |
| **D** — Data Design | Detailed schema, indexing | 5 min |
| **E** — Enhancements | Caching, sharding, failover | 8 min |
| **D** — Deep Dive | Hardest/most interesting part | 5 min |

---

## 📋 Step 1: Requirements (R)

### Functional Requirements
Ask clarifying questions before designing anything!

```
❓ Who uses this? (consumers, businesses, developers?)
❓ What are the core features? (MVP first)
❓ What does NOT need to be in scope?
❓ Read-heavy or write-heavy?
❓ Real-time or batch?
❓ Global or single region?
```

### Non-Functional Requirements
```
❓ How many users? DAU? Concurrent users?
❓ Latency requirements? (p99 < X ms)
❓ Availability target? (99.9%? 99.99%?)
❓ Consistency? (strong or eventual OK?)
❓ Data durability? (can we lose data?)
❓ Storage size? Growing at what rate?
```

> [!important] Don't Skip Requirements!
> Interviewers often plant ambiguity intentionally. Clarify before you design.

---

## 🔢 Step 2: Back-of-Envelope Estimation (E)

### Key Numbers to Memorize

| Metric | Value |
|--------|-------|
| Seconds/day | 86,400 (~100K) |
| 1M requests/day | ~12 requests/sec |
| 1B requests/day | ~12,000 requests/sec |
| 1 KB × 1M | = 1 GB |
| 1 KB × 1B | = 1 TB |
| Average web page | ~1 MB |
| Tweet (text) | ~280 bytes |
| Photo (compressed) | ~300 KB |
| Video (1 min, 720p) | ~50 MB |

### Latency Cheat Sheet

| Operation | Latency |
|-----------|---------|
| L1 Cache | ~1 ns |
| L2 Cache | ~10 ns |
| RAM | ~100 ns |
| SSD Read | ~100 µs (0.1 ms) |
| Network (same DC) | ~0.5 ms |
| Network (cross-region) | ~100 ms |
| HDD Seek | ~10 ms |

### Estimation Template
```
Users:    X million DAU
Actions:  Y actions/user/day
QPS:      X × Y / 86,400 ≈ Z requests/sec
Peak:     Z × 3 = peak QPS (assume 3x average)

Data per record: W bytes
Total storage:   X users × W bytes = ? GB/TB
Growth/year:     Z writes/sec × W bytes × 86,400 × 365 = ?
```

### Example: Instagram
```
100M DAU
1 photo upload every 2 days per user = 50M uploads/day
50M / 86,400 = ~580 writes/sec

Photo size: 3 MB (original), 300 KB (compressed)
Storage/day: 50M × 300 KB = 15 TB/day
Storage/5yr: ~27 PB (need distributed object storage)

Reads: 100M users × 20 photos/day = 2B reads/day = ~23,000 reads/sec
Read:Write = 23,000 : 580 = ~40:1 (read-heavy → use caching + CDN)
```

---

## 🏗️ Step 3-4: High-Level Design (S + H)

### Component Checklist
- [ ] Client (web/mobile)
- [ ] DNS + CDN
- [ ] Load Balancer
- [ ] API Gateway
- [ ] Core Services
- [ ] Message Queue (if async needed)
- [ ] Database(s)
- [ ] Cache Layer
- [ ] Storage (S3/blob)
- [ ] Monitoring

### DB Selection Cheat Sheet

| Scenario | Choose |
|----------|--------|
| Strong consistency, transactions | PostgreSQL, MySQL |
| High write throughput, time-series | Cassandra, HBase |
| Flexible schema, JSON | MongoDB |
| Key-value, caching, sessions | Redis, DynamoDB |
| Full-text search | Elasticsearch |
| Social graph | Neo4j |
| Analytics, OLAP | BigQuery, Redshift |

---

## 🔌 Step 5: API Design (A)

### Design 3-5 Key Endpoints
```
POST /tweets          → Create tweet
GET  /timeline/{user} → Get home feed
POST /users/{id}/follow → Follow user
GET  /search?q=query  → Search tweets
```

Include:
- Method + path
- Request body / query params
- Response schema
- Auth required?

---

## 🔧 Step 7: Enhancements (E)

### Always Cover These

#### Caching Strategy
```
- What to cache? (hot data, computed results)
- Where? (CDN, Redis, in-process)
- Cache invalidation strategy?
- TTL values?
```

#### Sharding / Partitioning
```
- What's the shard key?
- How to handle cross-shard queries?
- Hot shard mitigation?
```

#### Rate Limiting
```
- Per user? Per IP? Per API key?
- Algorithm? (Token bucket, sliding window)
- Where? (API Gateway, Redis)
```

#### Failover / HA
```
- What are the SPOFs?
- Failover strategy for each?
- Circuit breakers between services?
- Multi-region? Active-active or active-passive?
```

---

## ⚠️ Common Mistakes to Avoid

| Mistake | Better Approach |
|---------|----------------|
| Jump straight to design | **Clarify requirements first** |
| Single monolithic DB | Choose right DB for each use case |
| Ignore scale | Estimate QPS, storage upfront |
| Forget failures | Discuss SPOF and mitigation |
| Over-engineer from start | Start simple, add complexity for scale |
| Ignore monitoring | Mention metrics, alerts, tracing |
| Vague "database" | Name the specific DB and explain why |

---

## 💬 Things Interviewers Want to Hear

> [!tip] Demonstrate these skills

- **Trade-off thinking**: "We could do X, but that has Y downside. I'm choosing Z because..."
- **Scale awareness**: "At 1M users, this is fine. At 1B users, we'd need to shard."
- **Failure thinking**: "What happens when this service goes down?"
- **Back-of-envelope**: Do rough math to justify choices
- **Technology rationale**: "I'm using Cassandra because it's write-optimized and AP"
- **Iterative design**: Start simple, evolve toward scale

---

## 🎭 Interview Flow Script

```
"Before I start designing, let me clarify some requirements..."
  → [Ask 3-4 functional questions]
  → [Ask 3-4 non-functional questions]

"Let me do a quick capacity estimate..."
  → [Calculate QPS, storage]

"Here's my high-level design..."
  → [Draw components on whiteboard/paper]
  → [Walk through a key user flow]

"Let me deep dive into [hardest part]..."
  → [Discuss the most complex component in detail]

"A few things I'd add for production readiness..."
  → [Monitoring, alerting, DR, rate limiting]
```

---

## 📚 Final Study Checklist

### Core Concepts
- [ ] Latency, throughput, availability numbers
- [ ] CAP theorem → CP vs AP examples
- [ ] Consistent hashing
- [ ] SQL vs NoSQL trade-offs
- [ ] Caching strategies (aside, write-through, write-back)
- [ ] Circuit breaker pattern
- [ ] Kafka vs RabbitMQ

### System Designs to Practice
- [ ] [[Design Twitter]] — Feed fanout problem
- [ ] [[Design YouTube]] — Video upload + streaming
- [ ] [[Design WhatsApp]] — Real-time + presence
- [ ] [[Design URL Shortener]] — Base62, 302 vs 301
- [ ] [[Design Search Autocomplete]] — Trie structure
- [ ] [[Design Amazon]] — Inventory, checkout, search
- [ ] Design Uber (bonus) — Geo-spatial, matching
- [ ] Design Google Docs (bonus) — CRDTs, collaboration

---

## 🔗 Related Notes
- [[Key Concepts]] — Numbers to memorize
- [[Scalability]] — When to scale what
- [[🗺️ MOC — System Design]] — Full vault index

