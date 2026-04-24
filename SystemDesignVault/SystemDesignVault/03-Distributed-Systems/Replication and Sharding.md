---
tags: [system-design, distributed-systems, replication, sharding, scaling]
links: [[Databases]], [[CAP Theorem]], [[High Availability]]
---

# 🔁 Replication & Sharding

> [!abstract] Summary
> Replication copies data for redundancy and reads. Sharding splits data for writes. Both are essential for scaling data at millions of users.

---

## 📋 Replication

**Copying data to multiple nodes** for redundancy, availability, and read scaling.

### Primary-Replica (Master-Slave)
```
Writes ──► [Primary] ──► sync/async ──► [Replica 1]
                                    ──► [Replica 2]
Reads  ──────────────────────────────► [Replica 1 or 2]
```

- **Primary**: Handles all writes
- **Replicas**: Handle reads (offload primary)
- **Failover**: Replica promoted to primary if primary fails

**Use case**: Read-heavy applications (most web apps)

### Multi-Primary (Multi-Master)
```
[Primary A] ◄──── sync ────► [Primary B]
     ↑ writes                      ↑ writes
```

- Write to **any** node
- **Conflict resolution** is complex (same record updated on both)
- **Use case**: Geo-distributed writes, high write availability

### Synchronous vs Asynchronous

| Type | How | Trade-off |
|------|-----|-----------|
| **Synchronous** | Write confirmed only after ALL replicas ACK | No data loss, slower writes |
| **Asynchronous** | Write confirmed after primary only | Faster, potential data loss if primary crashes |
| **Semi-Sync** | Wait for at least 1 replica ACK | Balance of both |

---

## 🗂️ Sharding (Horizontal Partitioning)

**Splitting a large dataset across multiple nodes** (shards), each owning a subset.

```
User data (1 billion users):
Shard 1: user_id 1 – 250M
Shard 2: user_id 250M – 500M
Shard 3: user_id 500M – 750M
Shard 4: user_id 750M – 1B
```

> [!important] Why Shard?
> When a single DB server can't handle the write load or storage size — shard!

---

### Sharding Strategies

#### Range-Based Sharding
```
user_id 1-1000 → Shard 1
user_id 1001-2000 → Shard 2
```
- ✅ Simple, range queries easy
- ❌ Hotspots (e.g., new users always hit last shard)

#### Hash-Based Sharding
```
shard = hash(user_id) % num_shards
```
- ✅ Even distribution of data
- ❌ Range queries require all shards
- ❌ Resharding is painful (all keys remapped)

#### Directory-Based Sharding
```
Lookup Table: user_123 → Shard 3
```
- ✅ Maximum flexibility (can move data between shards)
- ❌ Lookup table = single point of failure / bottleneck

#### Geo-Based Sharding
```
India users → Shard Mumbai
US users    → Shard Virginia
EU users    → Shard Frankfurt
```
- ✅ Low latency for users
- ❌ Uneven load if traffic is geographically skewed

---

## 🔵 Consistent Hashing ⭐

> [!note] Solves the resharding problem in hash-based sharding

### How It Works
1. Arrange nodes in a **virtual ring** (0 to 2^32)
2. Hash each node → place on ring
3. Hash each key → place on ring
4. Key goes to the **first node clockwise**

```
Ring:  0 ──── Node A ──── Node B ──── Node C ──── 2^32
              ↑              ↑              ↑
           keys go here   keys go here  keys go here
```

### Adding/Removing Nodes
- **Adding Node D** between A and B:
  - Only keys between A and D move from B to D
  - All other keys unaffected ✅

- **Without consistent hashing**: All keys remapped (painful!)

### Virtual Nodes
- Each physical node maps to **multiple positions** on the ring
- More even distribution, especially with unequal server capacities
- Used in: **Cassandra**, **DynamoDB**, **Memcached**

---

## ⚠️ Sharding Challenges

| Challenge | Solution |
|-----------|---------|
| **Cross-shard JOINs** | Denormalize, avoid joins, or use scatter-gather |
| **Cross-shard transactions** | Two-phase commit (2PC) or avoid cross-shard txns |
| **Resharding** | Consistent hashing, gradual migration |
| **Hot shards** | Virtual nodes, better shard key selection |
| **Shard key selection** | Choose key that distributes load evenly |

---

## 🏗️ Replication vs Sharding

| | Replication | Sharding |
|-|-------------|---------|
| **Purpose** | Redundancy, read scaling | Write/storage scaling |
| **Data** | Same data on all replicas | Different data on each shard |
| **Read scaling** | ✅ Yes (read from replicas) | Partial |
| **Write scaling** | ❌ No (all writes to primary) | ✅ Yes |
| **Fault tolerance** | ✅ Yes | Partial (per-shard redundancy) |

> [!tip] Use Both Together
> Shard your database AND replicate each shard. This gives you both write scaling and high availability.

---

## 🔗 Related Notes
- [[Databases]] — When to choose which DB
- [[CAP Theorem]] — Replication choices affect consistency
- [[High Availability]] — Replication enables failover
- [[Design Twitter]] — Cassandra for tweets (sharded + replicated)

