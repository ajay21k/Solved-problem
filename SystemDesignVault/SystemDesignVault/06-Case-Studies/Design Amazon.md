---
tags: [system-design, case-study, amazon, ecommerce]
links: [[Databases]], [[Caching]], [[Message Queues]], [[Security]], [[CDN]]
---

# 🛒 Design Amazon / E-Commerce Platform

> [!abstract] Summary
> Design a large-scale e-commerce platform with product catalog, shopping cart, checkout, inventory management, and order processing.

---

## 📋 Requirements

### Functional
- ✅ Product search and browsing
- ✅ Shopping cart
- ✅ Checkout & payment
- ✅ Order tracking
- ✅ Inventory management
- ✅ Product recommendations
- ✅ Reviews & ratings

### Non-Functional
- 300M users, 1.5M orders/day
- Product catalog: 350M+ products
- Search: < 200ms
- Checkout: ACID transactions (can't oversell)
- 99.99% availability for checkout

---

## 🏗️ Microservices Architecture

```
Client → [API Gateway]
              │
    ┌─────────┼──────────┬───────────┬──────────┐
    ▼         ▼          ▼           ▼          ▼
[Product]  [Search]  [Cart]     [Order]    [Payment]
[Service]  [Service] [Service]  [Service]  [Service]
    │          │         │           │          │
  [RDS]   [ElasticSearch] [Redis]  [RDS]   [Stripe/etc]
    │
   [S3+CDN]
  (images)
```

---

## 📦 Product Catalog Service

### Storage
- **Relational DB** (Aurora/PostgreSQL): product metadata, pricing, categories
- **Elasticsearch**: full-text search, faceted filtering
- **S3 + CDN**: product images

### Product Schema
```sql
CREATE TABLE products (
  id          UUID PRIMARY KEY,
  name        VARCHAR(500),
  description TEXT,
  price       DECIMAL(10,2),
  category_id INT,
  seller_id   UUID,
  images      JSONB,          -- array of S3 URLs
  attributes  JSONB,          -- flexible: color, size, etc.
  status      ENUM('active', 'inactive'),
  created_at  TIMESTAMP
);
```

### Search with Elasticsearch
```json
GET /products/_search
{
  "query": {
    "multi_match": {
      "query": "wireless headphones",
      "fields": ["name^3", "description", "tags"]
    }
  },
  "aggs": {
    "by_brand": { "terms": { "field": "brand.keyword" } },
    "price_range": { "histogram": { "field": "price", "interval": 50 } }
  }
}
```

---

## 🛒 Shopping Cart Service

### Why Redis?
- Temporary data (cart abandoned = don't need)
- Very fast reads/writes
- Natural TTL (cart expires after 30 days)

### Cart Schema (Redis Hash)
```
key:   "cart:user_123"
value: {
  "product_abc": { "qty": 2, "price": 29.99, "added_at": "2026-03-21" },
  "product_xyz": { "qty": 1, "price": 149.00, "added_at": "2026-03-21" }
}
TTL: 30 days
```

### Anonymous Cart → Login
```
Anonymous user builds cart → logs in
→ Merge anonymous cart into user cart
→ Delete anonymous cart
```

---

## 📋 Inventory Service

### The Overselling Problem
```
❌ Bad: Check stock → Show "In Stock" → Add to cart → Place order → 
        Meanwhile 100 other users did same → Oversold!

✅ Good: Use optimistic locking or pessimistic locking
```

### Optimistic Locking (Best for most cases)
```sql
-- Check and decrement atomically
UPDATE inventory
SET stock = stock - 1,
    version = version + 1
WHERE product_id = 'abc'
  AND stock >= 1
  AND version = :expected_version;

-- If 0 rows affected → someone else updated first → retry or fail
```

### Redis for Real-Time Stock
```
DECRBY product:abc:stock 1
If result < 0 → INCRBY back, return "Out of Stock"

For flash sales: pre-load stock count in Redis, decrement atomically
```

---

## 💳 Order & Payment Service

### Order State Machine
```
CREATED → PAYMENT_PENDING → PAYMENT_CONFIRMED → PROCESSING → SHIPPED → DELIVERED
                                                                            │
                                                                        RETURNED
```

### Checkout Flow
```
1. User clicks "Place Order"
2. Lock inventory (reserve items for 10 min)
3. Create order record (status: PENDING)
4. Process payment via payment gateway (Stripe/Razorpay)
5a. Payment SUCCESS:
    → Confirm order (status: CONFIRMED)
    → Decrement inventory permanently
    → Send confirmation email (async via Kafka)
    → Notify fulfillment service (async)
5b. Payment FAIL:
    → Cancel order
    → Release inventory reservation
    → Notify user
```

### Idempotent Payments
```
Each payment request has a unique idempotency_key
If user double-clicks → same key → payment processed only once
Gateway deduplicates based on key
```

---

## 🌟 Recommendations Engine

```
Data signals:
- Purchase history
- Browsing history (clickstream)
- Cart additions
- Ratings & reviews

Algorithms:
1. Collaborative filtering: "Users like you bought..."
2. Item-based: "Frequently bought together"
3. Content-based: Similar products

Pipeline:
User actions → Kafka → Spark ML jobs → Recommendation DB
             (real-time signals)  (batch training)    (serve via Redis)
```

---

## ⭐ Reviews System

```sql
CREATE TABLE reviews (
  id          UUID PRIMARY KEY,
  product_id  UUID,
  user_id     UUID,
  rating      SMALLINT CHECK (rating BETWEEN 1 AND 5),
  title       VARCHAR(200),
  body        TEXT,
  verified    BOOLEAN DEFAULT FALSE,  -- verified purchase
  helpful_votes INT DEFAULT 0,
  created_at  TIMESTAMP
);
```

- Aggregate ratings pre-computed (don't calculate on every load)
- Update aggregate when new review added (async via Kafka)
- Anti-fraud: only verified purchasers can review

---

## 🔑 Key Design Decisions

| Decision | Choice | Reason |
|----------|--------|--------|
| Product search | Elasticsearch | Full-text, faceted, scalable |
| Cart | Redis | Fast, TTL, temporary data |
| Inventory locking | Optimistic locking | Avoids deadlocks at scale |
| Order processing | Async via Kafka | Decouple services |
| Payment | Idempotent keys | Prevent double-charge |
| Images | S3 + CDN | Cheap, fast, globally distributed |

---

## 🔗 Related Notes
- [[Databases]] — Aurora for orders, Elasticsearch for search
- [[Caching]] — Redis for cart, inventory
- [[Message Queues]] — Kafka for order events
- [[Security]] — Payment security, user auth
- [[CDN]] — Product images globally

