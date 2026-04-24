---
tags: [system-design, components, databases, sql, nosql]
links: [[CAP Theorem]], [[Replication and Sharding]], [[Caching]]
---

# 🗄️ Databases

> [!abstract] Summary
> Choosing the right database is one of the most critical system design decisions. Learn SQL vs NoSQL, ACID properties, indexing, and optimization.

---

## 🆚 SQL vs NoSQL

| Factor | SQL (Relational) | NoSQL |
|--------|-----------------|-------|
| Schema | Fixed, predefined | Flexible, dynamic |
| Scaling | Vertical (primarily) | Horizontal |
| Transactions | Strong ACID | Eventual consistency (BASE) |
| Joins | ✅ Yes | ❌ Limited |
| Query language | SQL (standardized) | API-specific |
| Best For | Financial, ERP, reporting | Social, IoT, real-time, Big Data |
| Examples | PostgreSQL, MySQL, Oracle | MongoDB, Cassandra, DynamoDB |

---

## 🔷 SQL Databases

### ACID Properties

| Property | Description | Example |
|----------|-------------|---------|
| **Atomicity** | All or nothing — full transaction or full rollback | Bank transfer: debit + credit |
| **Consistency** | DB moves from one valid state to another | FK constraints enforced |
| **Isolation** | Concurrent transactions don't interfere | Two users updating same row |
| **Durability** | Committed data survives crashes | Written to disk/WAL |

### When to Use SQL
- Complex relationships and joins
- Strong consistency requirements (finance, healthcare)
- Complex queries and aggregations
- Well-defined, stable schema

### Popular SQL DBs
- **PostgreSQL** — Most feature-rich, open source ⭐
- **MySQL** — Widely used, good read performance
- **SQLite** — Embedded, file-based
- **AWS Aurora** — Cloud-native, MySQL/Postgres compatible

---

## 🔶 NoSQL Databases

### Types of NoSQL

#### Key-Value Stores
```
"user:123" → { name: "Alice", age: 30 }
```
- **Examples**: Redis, DynamoDB, etcd
- **Use Case**: Sessions, caching, feature flags
- **Strengths**: O(1) reads/writes, simple, fast

#### Document Stores
```json
{
  "_id": "123",
  "name": "Alice",
  "address": { "city": "Chennai", "zip": "600001" },
  "orders": [{"id": 1}, {"id": 2}]
}
```
- **Examples**: MongoDB, CouchDB, Firestore
- **Use Case**: Flexible schemas, JSON data, catalogs
- **Strengths**: Rich queries, nested documents, flexible schema

#### Wide-Column Stores
```
Row Key: user_123
  ├── column_family:profile  → { name, email }
  └── column_family:activity → { login_ts, page_views }
```
- **Examples**: Cassandra, HBase, Bigtable
- **Use Case**: Time-series, write-heavy, analytics
- **Strengths**: Massive write throughput, horizontal scale, tunable consistency

#### Graph Databases
```
(Alice) -[FOLLOWS]-> (Bob) -[LIKES]-> (Post#42)
```
- **Examples**: Neo4j, Amazon Neptune
- **Use Case**: Social networks, recommendations, fraud detection
- **Strengths**: Efficient graph traversals, relationship queries

---

## 🔢 BASE Properties (NoSQL)

| Property | Description |
|----------|-------------|
| **B**asically Available | System responds even during partial failures |
| **S**oft State | State may change over time without input |
| **E**ventually Consistent | System will become consistent in the future |

---

## 📇 Database Indexing

> [!important] 
> Indexes speed up reads but slow down writes. Index selectively!

### Types of Indexes

| Index Type | Description | Use Case |
|-----------|-------------|----------|
| **Primary** | On primary key (auto) | Every table |
| **Secondary** | On non-PK columns | WHERE clause columns |
| **Composite** | Multiple columns together | `(user_id, created_at)` |
| **Unique** | Enforces uniqueness | Email, username |
| **Full-Text** | Text search | Blog post search |
| **Partial** | Index subset of rows | `WHERE status = 'active'` |

### How B-Tree Index Works
```
Query: WHERE age > 25

Without index: Scan ALL rows (O(n))
With index:    Binary search on B-tree (O(log n))
```

### Index Guidelines
- ✅ Index columns in WHERE, JOIN ON, ORDER BY, GROUP BY
- ✅ Composite index: put equality columns first, range columns last
- ❌ Don't index low-cardinality columns (e.g., boolean flags)
- ❌ Avoid over-indexing — each index = slower writes, more storage

---

## 🚀 Query Optimization

```sql
-- ❌ Bad: SELECT * fetches all columns
SELECT * FROM users WHERE email = 'alice@example.com';

-- ✅ Good: Select only needed columns
SELECT id, name FROM users WHERE email = 'alice@example.com';
```

### Tips
1. Use `EXPLAIN` / `EXPLAIN ANALYZE` to inspect query plans
2. Avoid `N+1 queries` — use JOINs or batch fetching
3. Use **connection pooling** (PgBouncer, HikariCP)
4. **Denormalize** for read-heavy workloads
5. Use **read replicas** for read-heavy traffic → [[Replication and Sharding]]
6. Paginate large result sets: `LIMIT 20 OFFSET 0`

---

## 🏗️ Database Normalization

| Form | Rule |
|------|------|
| **1NF** | Atomic values, no repeating groups |
| **2NF** | 1NF + no partial dependencies |
| **3NF** | 2NF + no transitive dependencies |
| **Denormalized** | Intentionally duplicate data for performance |

> [!tip] Normalization vs Denormalization
> - **OLTP** (transactional): Normalize → reduce duplication, ensure integrity
> - **OLAP** (analytics): Denormalize → faster reads, avoid expensive joins

---

## ☁️ Cloud Database Options

| Type | AWS | GCP | Azure |
|------|-----|-----|-------|
| Managed SQL | RDS, Aurora | Cloud SQL | Azure SQL |
| NoSQL Key-Value | DynamoDB | Firestore | Cosmos DB |
| Data Warehouse | Redshift | BigQuery | Synapse |
| In-Memory | ElastiCache | Memorystore | Azure Cache |
| Time-Series | Timestream | — | — |

---

## 🔗 Related Notes
- [[CAP Theorem]] — Consistency vs Availability trade-offs in DBs
- [[Replication and Sharding]] — Scaling the data layer
- [[Caching]] — Reduce DB load with caching layer
- [[High Availability]] — DB failover, backups, DR

