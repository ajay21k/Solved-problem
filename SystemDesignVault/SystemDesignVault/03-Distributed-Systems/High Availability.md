---
tags: [system-design, distributed-systems, high-availability, fault-tolerance]
links: [[Load Balancing]], [[Replication and Sharding]], [[Observability]]
---

# 🏥 High Availability & Fault Tolerance

> [!abstract] Summary
> HA systems remain operational despite component failures through redundancy, failover, and graceful degradation.

---

## 🎯 High Availability Principles

> [!important] Core Principle
> Eliminate every **Single Point of Failure (SPOF)**. If one component going down takes down the system, it's a SPOF.

```
SPOF Architecture:               HA Architecture:
Client → LB → Server → DB        Client → [LB1]  → Server A → [DB Primary]
                ↑ SPOF!                  → [LB2]  → Server B → [DB Replica]
                                  Any component fails → traffic reroutes ✅
```

---

## 🛠️ Fault Tolerance Techniques

### Redundancy
- Duplicate every critical component
- Active-Active: All components handle traffic
- Active-Passive: Standby takes over on failure

### Health Checks + Auto-Healing
```
Load Balancer pings /health every 30s
→ No response? Remove from pool
→ Recovery? Add back automatically
```

### Circuit Breaker Pattern ⭐
```
State Machine:
CLOSED ──(failure threshold reached)──► OPEN ──(timeout)──► HALF-OPEN
  ↑                                                               |
  └───────────────(success)──────────────────────────────────────┘
```

| State | Behavior |
|-------|---------|
| **Closed** | Requests pass through normally |
| **Open** | Requests fail immediately (no downstream call) |
| **Half-Open** | Limited test requests; if pass → Close, if fail → Open |

**Why?** Prevents cascading failures — one slow service taking down everything.

### Retry with Exponential Backoff
```
Attempt 1: fail → wait 1s
Attempt 2: fail → wait 2s
Attempt 3: fail → wait 4s
Attempt 4: fail → wait 8s + jitter
```

> [!warning] Add Jitter
> Random jitter prevents all clients from retrying simultaneously (thundering herd).

### Bulkhead Pattern
```
[Thread Pool A: Payment Service]  — isolated
[Thread Pool B: Email Service]    — isolated
[Thread Pool C: Inventory]        — isolated

Payment slowdown only affects Payment pool — doesn't drain all threads ✅
```

### Graceful Degradation
```
Normal:   [Search] + [Recommendations] + [Ads]
Degraded: [Search] + [Cached Recommendations] + (ads failed, hidden)
```
- Serve core functionality even when non-critical features fail
- Show cached/stale data instead of an error

### Timeout
- Always set timeouts on external calls
- Don't wait forever for a downstream service
- `connection_timeout` + `read_timeout`

---

## 🌍 Disaster Recovery

### Key Metrics

| Metric | Meaning |
|--------|---------|
| **RTO** (Recovery Time Objective) | Max acceptable **downtime** after a disaster |
| **RPO** (Recovery Point Objective) | Max acceptable **data loss** (in time) |

### DR Strategies

| Strategy | RTO | RPO | Cost |
|----------|-----|-----|------|
| **Backup & Restore** | Hours | Hours | 💰 Cheapest |
| **Pilot Light** | ~10 min | Minutes | 💰💰 |
| **Warm Standby** | Minutes | Seconds | 💰💰💰 |
| **Multi-Site Active-Active** | ~0 | ~0 | 💰💰💰💰 Most expensive |

### Backup & Restore
- Scheduled backups to S3 / cold storage
- Restore when disaster strikes
- **Best for**: Non-critical systems, low budget

### Pilot Light
- Minimal infrastructure always running (DB replicated)
- Scale up quickly when needed
- Like a pilot light on a gas stove — ready to ignite

### Warm Standby
- Scaled-down but **functional** copy always running
- Scale to full production on failover
- **Best for**: Most production systems

### Multi-Site Active-Active
- Full production deployment in 2+ regions
- Traffic load balanced across regions
- Near-zero RTO and RPO
- **Best for**: Mission-critical (banking, healthcare)

---

## 📋 Chaos Engineering

> Test failure scenarios in production deliberately to find weaknesses.

- **Netflix Chaos Monkey**: Randomly terminates instances in production
- **Principles**: Hypothesis → Experiment → Observe → Learn
- Tools: Chaos Monkey, Gremlin, Chaos Toolkit

---

## 🔗 Related Notes
- [[Load Balancing]] — Removes unhealthy servers automatically
- [[Replication and Sharding]] — Data redundancy
- [[Observability]] — Detect failures early
- [[Microservices vs Monolith]] — Circuit breaker between services
- [[Key Concepts]] — SLA, availability numbers

