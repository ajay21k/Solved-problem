---
tags: [system-design, fundamentals, scalability]
links: [[Load Balancing]], [[Databases]], [[Caching]]
---

# 📈 Scalability

> [!abstract] Summary
> Scalability is the ability to handle increased load without performance degradation.

---

## ↕️ Vertical Scaling (Scale Up)

Add more power (CPU, RAM, SSD) to a **single machine**.

```
Before:  [Server 4GB RAM]
After:   [Server 64GB RAM]
```

| ✅ Pros | ❌ Cons |
|--------|--------|
| Simple — no code changes | Physical hardware limits |
| No distributed system complexity | Single Point of Failure (SPOF) |
| Low latency (no network hops) | Very expensive at high end |
| Works for stateful apps | Requires downtime to upgrade |

---

## ↔️ Horizontal Scaling (Scale Out)

Add **more machines** to distribute load.

```
Before:  [Server A]
After:   [Server A] [Server B] [Server C]  ← behind a Load Balancer
```

| ✅ Pros | ❌ Cons |
|--------|--------|
| Near-infinite scale | Requires [[Load Balancing]] |
| High availability | Distributed coordination complexity |
| Commodity hardware (cheap) | Stateful services are harder |
| No single point of failure | Eventual consistency challenges |

> [!tip] Rule of Thumb
> Start with vertical scaling (simple). Switch to horizontal when you hit limits.

---

## 🔁 Stateless vs Stateful Services

### Stateless
- Each request is **self-contained** — server holds no session data.
- Any server can handle any request.
- **Easy to scale horizontally**.
- Examples: REST APIs, microservices.

### Stateful
- Server **remembers** state between requests (e.g., session, DB connection).
- Clients must connect to the **same** server.
- Harder to horizontally scale without sticky sessions or external state stores.
- Examples: WebSocket connections, DB servers.

> [!important] Best Practice
> Move state **out** of your application servers and into dedicated stores (Redis, DB) so your app layer stays stateless and horizontally scalable.

---

## ⚡ Auto-Scaling

Automatically add or remove instances based on real-time metrics.

```
Traffic spike → Auto Scaler detects high CPU/RPS → Launches new instances → Load balancer routes traffic
```

### Scaling Triggers
- CPU utilization > 70%
- Memory usage > 80%
- Request queue depth > threshold
- RPS (requests per second)

### Tools
- **AWS**: Auto Scaling Groups + CloudWatch
- **Kubernetes**: Horizontal Pod Autoscaler (HPA)
- **GCP**: Managed Instance Groups

> [!warning] Cold Start Problem
> New instances take time to warm up (load configs, fill caches). Pre-warm instances during expected traffic spikes (e.g., flash sales).

---

## 🧮 Scalability Patterns

### Read-Heavy Systems
- Add **read replicas** to the DB → [[Replication and Sharding]]
- Use **caching** → [[Caching]]
- Use a **CDN** for static content → [[CDN]]

### Write-Heavy Systems
- Use **sharding** to distribute writes → [[Replication and Sharding]]
- Use **message queues** to buffer writes → [[Message Queues]]
- Use **event sourcing / CQRS**

### Compute-Heavy Systems
- Use **horizontal scaling** with a load balancer
- Offload tasks to **async workers**

---

## 🔗 Related Notes
- [[Load Balancing]] — Distribute traffic across scaled servers
- [[Caching]] — Reduce DB load at scale
- [[Databases]] — DB scaling strategies
- [[Replication and Sharding]] — Data layer scaling

