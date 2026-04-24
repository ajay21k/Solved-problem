---
tags: [system-design, distributed-systems, cap-theorem, consistency]
links: [[Databases]], [[Replication and Sharding]], [[High Availability]]
---

# 📐 CAP Theorem

> [!abstract] Summary
> A distributed system can guarantee at most **2 of 3** properties: Consistency, Availability, Partition Tolerance.

---

## 🔺 The CAP Triangle

```
              Consistency
                  /\
                 /  \
                /    \
               / CP   \
              /    |    \
             /    ???    \
            /             \
           ────────────────
          AP               CA
    Availability    (not distributed)
    
    Network Partitions are INEVITABLE
    → You must choose: CP or AP
```

---

## 📖 The Three Properties

### C — Consistency
> Every read returns the **most recent write** (or an error)

- All nodes see the **same data** at the same time
- Not the same as ACID consistency!
- Strong consistency = linearizability

### A — Availability
> Every request gets a **response** (success or failure — never a timeout)

- System keeps responding even during partial failures
- Does NOT guarantee the data is the most recent

### P — Partition Tolerance
> System **continues operating** despite network partition (nodes can't communicate)

- Network partitions happen — dropped packets, node failures, network splits
- **You cannot sacrifice P** in a distributed system → choose CP or AP

---

## ⚡ CP vs AP Systems

### CP Systems (Consistency + Partition Tolerance)
> Sacrifice availability when a partition occurs

```
Partition happens → Node refuses to respond (to avoid stale data) → Unavailable but consistent
```

| System | Notes |
|--------|-------|
| **HBase** | Strong consistency |
| **Zookeeper** | Used for coordination/locks |
| **MongoDB** (strong mode) | Can be configured for CP |
| **Redis** (with quorum) | Depends on config |

**Use When**: Financial transactions, inventory management — correctness > availability

### AP Systems (Availability + Partition Tolerance)
> Sacrifice consistency when a partition occurs — return potentially stale data

```
Partition happens → Node returns best-known data → Available but may be stale
```

| System | Notes |
|--------|-------|
| **Cassandra** | Tunable consistency |
| **DynamoDB** | Eventually consistent by default |
| **CouchDB** | Multi-master, eventual consistency |
| **Riak** | Highly available KV store |

**Use When**: Social feeds, shopping carts, DNS — availability > strict consistency

---

## 🔧 PACELC Extension

CAP only covers partitions. **PACELC** adds the normal-operation trade-off:

```
If Partition: choose Availability vs Consistency
Else (normal): choose Latency vs Consistency
```

| System | Partition | Else |
|--------|-----------|------|
| DynamoDB | AP | EL (low latency) |
| Cassandra | AP | EL |
| HBase | CP | EC (strong consistency) |
| PostgreSQL | CP | EC |

---

## 🎚️ Tunable Consistency (Cassandra)

Cassandra lets you configure per-operation:

| Level | Behavior |
|-------|----------|
| `ONE` | Fastest — 1 replica responds |
| `QUORUM` | Majority of replicas respond |
| `ALL` | All replicas respond (slowest) |

> [!tip] Quorum Rule
> `Write QUORUM + Read QUORUM > Replication Factor` → Strong consistency

---

## 🔗 Related Notes
- [[Databases]] — Choose DB based on CP vs AP needs
- [[Replication and Sharding]] — How data is distributed across nodes
- [[High Availability]] — AP systems favor availability
- [[Key Concepts]] — Consistency models explained

