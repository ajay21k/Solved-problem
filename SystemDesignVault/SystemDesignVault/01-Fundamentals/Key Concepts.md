---
tags: [system-design, fundamentals]
links: [[Scalability]], [[CAP Theorem]], [[High Availability]]
---

# 📌 Key Concepts & Terminologies

> [!abstract] Summary
> Core vocabulary every system designer must know — latency, throughput, availability, and reliability.

---

## ⚡ Latency vs Throughput

| Metric | Definition | Example |
|--------|-----------|---------|
| **Latency** | Time for ONE request to complete | 200ms response time |
| **Throughput** | Requests handled per second | 10,000 RPS |

> [!important] Goal
> Low Latency + High Throughput. They often trade off against each other.

### Latency Numbers to Remember

| Operation | Latency |
|-----------|---------|
| L1 Cache | ~1 ns |
| L2 Cache | ~4 ns |
| RAM Read | ~100 ns |
| SSD Read | ~0.1 ms |
| Network (same DC) | ~0.5 ms |
| HDD Seek | ~10 ms |
| Network (cross-region) | ~100 ms |

---

## 📈 Availability

Percentage of time a system is **operational and responding correctly**.

| SLA | Downtime/year | Downtime/month |
|-----|--------------|----------------|
| 99% | ~3.65 days | ~7.2 hours |
| 99.9% | ~8.7 hours | ~43 min |
| 99.99% | ~52 min | ~4.3 min |
| 99.999% | ~5 min | ~26 sec |

> [!tip] Five Nines
> 99.999% availability = ~5 minutes downtime/year. Very expensive to achieve.

### Availability in Series vs Parallel

- **Series**: `Availability = A1 × A2` → lower overall
- **Parallel**: `Availability = 1 - (1-A1)(1-A2)` → higher overall

---

## 🔒 Reliability

System consistently performs its **intended function** without failure, even under adverse conditions.

> [!note] Availability vs Reliability
> - A system can be **available** (responding) but **unreliable** (returning wrong data).
> - Reliable systems are also available, but not always vice versa.

---

## 📋 SLA / SLO / SLI

| Term | Full Name | Meaning |
|------|-----------|---------|
| **SLI** | Service Level Indicator | Actual measured metric (e.g., p99 latency = 180ms) |
| **SLO** | Service Level Objective | Internal target (e.g., p99 latency < 200ms) |
| **SLA** | Service Level Agreement | Contract with users (e.g., 99.9% uptime or credits issued) |

> [!warning] SLA vs SLO
> SLO should be **stricter** than SLA to give you a buffer before breaching the contract.

---

## 🔄 Consistency Models

| Model | Description | Example |
|-------|-------------|---------|
| **Strong** | Always reads latest write | Traditional RDBMS |
| **Eventual** | Reads may be stale; eventually syncs | DNS, S3 |
| **Read-Your-Writes** | You see your own writes | User profile update |
| **Monotonic Reads** | Never see older data than before | Session consistency |
| **Causal** | Causally related ops in order | Chat threads |

---

## 🔗 Related Notes
- [[Scalability]] — How to grow a system
- [[CAP Theorem]] — Fundamental consistency/availability trade-off
- [[High Availability]] — Designing for fault tolerance
- [[Interview Framework]] — Use these numbers in estimation

