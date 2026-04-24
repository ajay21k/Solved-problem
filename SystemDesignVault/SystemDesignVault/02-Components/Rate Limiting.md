---
tags: [system-design, components, rate-limiting, security]
links: [[Security]], [[Load Balancing]], [[Caching]]
---

# 🚦 Rate Limiting & Throttling

> [!abstract] Summary
> Rate limiting controls how many requests a user/service can make in a given time window.

---

## 🤔 Why Rate Limit?

- 🛡️ Prevent DDoS / abuse
- ⚖️ Fair usage across users
- 💰 Protect infrastructure costs
- 🔐 Security (brute-force protection)

---

## 📐 Algorithms

### Token Bucket ⭐ (Most Common)
```
Bucket capacity: 10 tokens
Refill rate: 2 tokens/sec

Request → consume 1 token → allow ✅
No tokens → reject ❌
```
- **Pros**: Allows burst traffic up to bucket size
- **Cons**: Slightly complex implementation
- **Used by**: AWS, Stripe

### Leaky Bucket
```
Requests enter bucket → process at fixed rate → overflow = rejected
```
- **Pros**: Smooth, consistent output rate
- **Cons**: Bursts are dropped (no accumulation)

### Fixed Window Counter
```
Window: 00:00 - 01:00
Count: ||||||||||  (10 requests — limit reached)
01:00 - 02:00: counter resets
```
- **Pros**: Simple
- **Cons**: Spike at window boundary (edge case allows 2x requests)

### Sliding Window Log
- Store timestamp of each request in a log
- Count requests in rolling window
- **Pros**: Accurate
- **Cons**: High memory usage

### Sliding Window Counter
- Hybrid: weighted count from current + previous window
- **Pros**: Memory efficient + accurate
- **Best**: Production systems ⭐

---

## 📍 Where to Implement

| Location | Tool | Scope |
|----------|------|-------|
| **API Gateway** | Kong, AWS API Gateway | Per API key/route |
| **Load Balancer** | Nginx, HAProxy | Per IP |
| **Application** | Middleware, Interceptor | Per user/endpoint |
| **Distributed** | Redis counters | Multi-instance |

### Distributed Rate Limiting with Redis
```
key: "rate_limit:user_123:minute:2026032114"
INCR key → returns new count
EXPIRE key 60 → auto-reset after 1 minute

If count > limit → reject with HTTP 429
```

---

## 📤 HTTP Response for Rate Limited Requests

```http
HTTP/1.1 429 Too Many Requests
Retry-After: 30
X-RateLimit-Limit: 100
X-RateLimit-Remaining: 0
X-RateLimit-Reset: 1711029600
```

---

## 🔗 Related Notes
- [[Security]] — Rate limiting prevents brute force
- [[Load Balancing]] — LB can enforce IP-based limits
- [[Caching]] — Redis used for distributed counters
- [[API Design]] — Include rate limit headers in APIs

