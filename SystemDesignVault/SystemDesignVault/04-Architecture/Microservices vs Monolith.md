---
tags: [system-design, architecture, microservices, monolith]
links: [[Message Queues]], [[API Design]], [[High Availability]], [[Load Balancing]]
---

# 🏗️ Microservices vs Monolith

> [!abstract] Summary
> Choosing between monolith and microservices is a fundamental architecture decision with major trade-offs.

---

## 🧱 Monolithic Architecture

All features bundled in a **single deployable unit**.

```
┌─────────────────────────────────────────┐
│              Monolith                   │
│  ┌──────────┐ ┌──────────┐ ┌─────────┐ │
│  │  Users   │ │  Orders  │ │ Products│ │
│  └──────────┘ └──────────┘ └─────────┘ │
│  ┌──────────────────────────────────┐   │
│  │           Single Database        │   │
│  └──────────────────────────────────┘   │
└─────────────────────────────────────────┘
```

| ✅ Pros | ❌ Cons |
|--------|--------|
| Simple to develop & test | Can't scale specific features |
| Easy debugging (single codebase) | Tight coupling → hard to change |
| Low latency (in-process calls) | Slow deployments (whole app redeploys) |
| Simple deployment | Language/tech lock-in |
| Good for small teams | Long build times at scale |

**Start here!** Premature microservices = distributed monolith.

---

## 🔬 Microservices Architecture

System split into **small, independent services**, each owning its data.

```
Client → [API Gateway]
              │
     ┌────────┼────────┬────────┐
     ▼        ▼        ▼        ▼
 [Users]  [Orders] [Products] [Payment]
   │DB       │DB      │DB       │DB
```

| ✅ Pros | ❌ Cons |
|--------|--------|
| Independent scaling per service | Distributed system complexity |
| Independent deployment | Network latency between services |
| Tech flexibility per service | Data consistency challenges |
| Fault isolation | More DevOps overhead |
| Small, focused teams | Distributed tracing needed |

---

## 🚪 API Gateway

Single entry point for all client requests to microservices.

```
Mobile App ─┐
Web App    ─┤─► [API Gateway] ─► User Service
Partner    ─┘                ─► Order Service
                              ─► Payment Service
```

**Responsibilities**:
- ✅ Request routing
- ✅ Authentication & authorization
- ✅ Rate limiting
- ✅ SSL termination
- ✅ Request/response transformation
- ✅ Logging & monitoring
- ✅ Caching

**Examples**: Kong, AWS API Gateway, Nginx, Traefik

---

## 🔍 Service Discovery

How do services find each other when IPs change dynamically?

### Client-Side Discovery
- Service queries a **registry** (Eureka, Consul) for the target service address
- Client does load balancing itself

### Server-Side Discovery
- Load balancer queries the registry
- Client doesn't know about other services
- Examples: AWS ALB + ECS, Kubernetes Services

---

## 🐌 Strangler Fig Pattern

Gradually migrate a monolith to microservices:

```
Start: All traffic → Monolith

Step 1: New Feature A → Microservice A
        Old Features → Monolith

Step 2: Migrate Feature B → Microservice B

Final: All traffic → Microservices
       Monolith decommissioned
```

---

## 🔗 Related Notes
- [[Message Queues]] — Async communication between services
- [[API Design]] — API contracts between services
- [[High Availability]] — Circuit breaker pattern
- [[Load Balancing]] — Route traffic to services
- [[Observability]] — Distributed tracing across services

