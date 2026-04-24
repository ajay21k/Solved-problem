---
tags: [system-design, MOC, index]
created: 2026-03-21
---

# 🗺️ System Design — Map of Content

> [!tip] How to Use This Vault
> - Click any `[[WikiLink]]` to jump to that note
> - Use **Cmd/Ctrl + Click** to open in a new pane
> - Enable **Graph View** to see connections between notes
> - Use `#tags` in the search bar to filter topics

---

## 📐 Fundamentals
- [[Key Concepts]] — Latency, Throughput, Availability, SLAs
- [[Scalability]] — Vertical vs Horizontal, Auto-scaling
- [[Networking Basics]] — DNS, TCP/UDP, HTTP versions, WebSockets

---

## ⚙️ Core Components
- [[Load Balancing]] — Algorithms, L4 vs L7, Health Checks
- [[Caching]] — Redis, Eviction Policies, Write Strategies
- [[Databases]] — SQL vs NoSQL, ACID, Indexing
- [[Message Queues]] — Kafka, RabbitMQ, Pub-Sub
- [[CDN]] — Edge Caching, Cache-Control Headers
- [[Proxies]] — Forward, Reverse, Service Mesh
- [[Rate Limiting]] — Token Bucket, Sliding Window
- [[Storage Systems]] — Block, File, Object Storage

---

## 🌐 Distributed Systems
- [[CAP Theorem]] — Consistency, Availability, Partition Tolerance
- [[Replication and Sharding]] — Consistent Hashing, Sharding Strategies
- [[High Availability]] — Circuit Breaker, Fault Tolerance, DR

---

## 🏗️ Architecture & Design
- [[Microservices vs Monolith]] — Patterns, API Gateway, Service Discovery
- [[API Design]] — REST, GraphQL, gRPC, WebSockets
- [[Security]] — Auth, JWT, OAuth, Encryption, Attack Vectors

---

## 📊 Monitoring
- [[Observability]] — Metrics, Logs, Traces, Alerting

---

## 🔬 Case Studies
- [[Design Twitter]] — Feed generation, fanout strategies
- [[Design YouTube]] — Video upload, transcoding, streaming
- [[Design WhatsApp]] — Real-time messaging, presence
- [[Design URL Shortener]] — Base62, Redirection
- [[Design Search Autocomplete]] — Trie, Ranking
- [[Design Amazon]] — Catalog, Cart, Orders, Payments

---

## 🎯 Interview Prep
- [[Interview Framework]] — RESHADED method, estimation cheat sheet

---

## 🔗 Quick Links by Topic

| Topic | Key Note | Related |
|-------|----------|---------|
| Scaling reads | [[Caching]] | [[Replication and Sharding]], [[CDN]] |
| Scaling writes | [[Replication and Sharding]] | [[Message Queues]], [[Databases]] |
| Real-time | [[Message Queues]] | [[API Design]], [[Networking Basics]] |
| Reliability | [[High Availability]] | [[CAP Theorem]], [[Observability]] |
| Security | [[Security]] | [[API Design]], [[Rate Limiting]] |

