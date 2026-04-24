---
tags: [system-design, case-study, autocomplete, search, trie]
links: [[Caching]], [[Databases]], [[Scalability]]
---

# 🔍 Design Search Autocomplete

> [!abstract] Summary
> Design a real-time type-ahead/autocomplete system that suggests search queries as users type, like Google's search bar.

---

## 📋 Requirements

### Functional
- ✅ Return top 5 suggestions for a given prefix
- ✅ Suggestions ranked by popularity/frequency
- ✅ Update suggestions based on recent trends
- ✅ < 100ms latency end-to-end

### Non-Functional
- 10M DAU, 10 queries/user/day = 100M queries/day
- 5 keystrokes per query → 500M prefix lookups/day = ~6,000/sec
- Fresh data: update suggestions within 1 hour of trending

---

## 🌳 Data Structure: Trie (Prefix Tree)

```
Queries: "apple", "app", "apply", "application"

         root
          │
          a
          │
          p
          │
          p ──── (top suggestions: app, apple, apply)
         / \
        l   l
        │   │
        e   i
           / \
          c   e
          │   │
          a   d
          │
          t
          │
          i
         ...
```

### Enhanced Trie: Store Top-K at Each Node
```
Node "app" → top5: [(apple, 5000), (application, 4200), (apply, 3100), (app, 2800), (apps, 1900)]
```
- Pre-compute top-K suggestions at each prefix node
- Query: walk trie to node → return stored top-K instantly ✅
- Trade-off: more storage, but O(prefix_length) query time

---

## 🏗️ System Architecture

```
┌─────────────────────────────────────────────────────┐
│                    Data Collection                  │
│  User types → Log queries → Kafka → Batch Processor │
│              (raw query log)    (Spark/Hadoop)       │
└──────────────────────┬──────────────────────────────┘
                       │ (aggregated frequencies)
                       ▼
┌─────────────────────────────────────────────────────┐
│                 Trie Builder Service                │
│  Build trie from top queries → Serialize to file   │
│  Push updated trie to Trie Servers                 │
└──────────────────────┬──────────────────────────────┘
                       │
┌──────────────────────▼──────────────────────────────┐
│                  Trie Servers                       │
│  In-memory trie (read-only) → serve suggestions    │
│  Updated hourly via blue-green deployment           │
└──────────────────────┬──────────────────────────────┘
                       │
              [Redis Cache Layer]
                       │
              [API Gateway / LB]
                       │
                   Clients
```

---

## ⚡ Query Flow (Read Path)

```
User types "app"

1. Client sends: GET /autocomplete?prefix=app
2. Check Redis cache: key "autocomplete:app" → HIT? Return immediately ✅
3. MISS? → Query Trie Server
4. Trie Server traverses trie to "app" node
5. Returns stored top-5 suggestions
6. Store result in Redis: key "autocomplete:app" TTL 5 min
7. Return to client
```

> [!tip] Client-side optimization
> Cache suggestions in browser too! If user already typed "appl" and got results, use those to filter "apple" without another request.

---

## 🔄 Data Update Flow (Write Path)

```
Every search query → Kafka topic "search.queries"

Aggregation (every 1 hour):
→ Spark job reads Kafka
→ Count query frequency in last 24h
→ Identify top queries per prefix
→ Rebuild trie with updated frequencies
→ Serialize trie to blob storage (S3)
→ Trie servers reload via blue-green swap
```

### Handling Trending Queries
- Real-time trending: separate lighter-weight pipeline
- Recent queries weighted higher (time decay factor)
- Personalization: mix global popular + user's own history

---

## 🔤 Design Considerations

### Filtering Bad Content
- Blocklist: Filter adult/hate content from suggestions
- Store blocklist in memory, check before returning results

### Multilingual Support
- Build separate tries per language
- Detect user language and query appropriate trie

### Scaling the Trie

```
Full trie is large (~10GB for all prefixes)
→ Shard by first character: 'a*' → Server A, 'b*' → Server B
→ Each shard held fully in memory
→ For 'ab*' queries, route to server handling 'a'
```

---

## 📊 Storage Estimates

```
Unique queries tracked: top 100M queries
Average query length:   20 chars
Trie nodes:             ~100M × 20 = 2B nodes (with shared prefixes: ~500M)
Per node:               ~30 bytes (char + count + top5 pointers)
Total trie size:        ~15 GB → fits in RAM on a few servers
```

---

## 🔑 Key Design Decisions

| Decision | Choice | Reason |
|----------|--------|--------|
| Data structure | Trie with top-K per node | O(L) lookup where L = prefix length |
| Update strategy | Batch (hourly) + async | Real-time rebuild is too expensive |
| Caching | Redis + browser cache | Reduce trie server load |
| Personalization | Blend global + user history | Better UX |
| Serving | Read-only in-memory trie | Maximum speed |

---

## 🔗 Related Notes
- [[Caching]] — Redis cache for frequent prefix lookups
- [[Databases]] — Store raw query logs
- [[Scalability]] — Shard trie by prefix for scale

