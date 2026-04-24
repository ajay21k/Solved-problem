---
tags: [system-design, architecture, api, rest, graphql, grpc]
links: [[Microservices vs Monolith]], [[Security]], [[Rate Limiting]], [[Networking Basics]]
---

# 🔌 API Design

> [!abstract] Summary
> APIs are contracts between services and clients. Choosing REST, GraphQL, or gRPC depends on your use case.

---

## 🌐 REST API

Stateless, HTTP-based, resource-oriented.

### HTTP Methods
| Method | Action | Idempotent? |
|--------|--------|-------------|
| **GET** | Read resource | ✅ Yes |
| **POST** | Create resource | ❌ No |
| **PUT** | Replace resource (full update) | ✅ Yes |
| **PATCH** | Partial update | ❌ No (usually) |
| **DELETE** | Delete resource | ✅ Yes |

### HTTP Status Codes
| Code | Meaning |
|------|---------|
| 200 OK | Success |
| 201 Created | Resource created |
| 204 No Content | Success, no body |
| 400 Bad Request | Client error (invalid input) |
| 401 Unauthorized | Not authenticated |
| 403 Forbidden | Authenticated but not authorized |
| 404 Not Found | Resource doesn't exist |
| 409 Conflict | State conflict (duplicate) |
| 429 Too Many Requests | Rate limited |
| 500 Internal Server Error | Server error |
| 503 Service Unavailable | Temporarily down |

### REST Best Practices
```http
✅ Good URL design:
GET    /users/123          — get user
POST   /users              — create user
PUT    /users/123          — replace user
PATCH  /users/123          — update user fields
DELETE /users/123          — delete user
GET    /users/123/orders   — get user's orders

❌ Bad URL design:
GET /getUser?id=123
POST /createUser
POST /deleteUser/123
```

- Version your API: `/v1/users`, `/v2/users`
- Pagination: `GET /posts?page=2&limit=20` or cursor-based
- Filter/sort: `GET /users?status=active&sort=created_at`
- Use nouns for resources, not verbs

---

## 📊 GraphQL

Query language for APIs — clients specify exactly what data they need.

```graphql
# Client specifies exactly what it wants
query {
  user(id: "123") {
    name
    email
    orders {
      id
      total
    }
  }
}

# Returns exactly that — no more, no less
```

| ✅ Pros | ❌ Cons |
|--------|--------|
| No over/under-fetching | Complex server implementation |
| Single endpoint | Caching harder |
| Strongly typed schema | N+1 query problem (use DataLoader) |
| Great for mobile (limited bandwidth) | Learning curve |
| Self-documenting | File uploads are awkward |

**Use when**: Multiple clients with different data needs (mobile vs web vs partner)

---

## ⚡ gRPC

Google's Remote Procedure Call framework using Protocol Buffers.

```protobuf
// Define service contract
service UserService {
  rpc GetUser (GetUserRequest) returns (User);
  rpc ListUsers (ListUsersRequest) returns (stream User);
}

message User {
  string id = 1;
  string name = 2;
  string email = 3;
}
```

| Feature | gRPC | REST |
|---------|------|------|
| Protocol | HTTP/2 | HTTP/1.1 or 2 |
| Format | Protobuf (binary) | JSON (text) |
| Performance | ~5-10x faster | Baseline |
| Typing | Strongly typed | None (or OpenAPI) |
| Browser support | Limited | ✅ Universal |
| Streaming | ✅ Built-in | Partial |

**Use when**: Internal microservice-to-microservice communication, low-latency needs

### gRPC Streaming Types
| Type | Pattern | Use Case |
|------|---------|----------|
| Unary | Request → Response | Regular RPC |
| Server Streaming | Request → Stream of responses | Live data feed |
| Client Streaming | Stream of requests → Response | File upload |
| Bidirectional | Stream ↔ Stream | Chat, gaming |

---

## 🔌 WebSockets

Persistent, full-duplex connection.

```
HTTP Upgrade:
Client → "GET /ws HTTP/1.1\nUpgrade: websocket"
Server → "101 Switching Protocols"
Client ←——————————— persistent connection ———————————→ Server
```

**Use when**: Real-time features — chat, live collaboration, gaming, stock tickers

---

## 🆚 REST vs GraphQL vs gRPC

| Factor | REST | GraphQL | gRPC |
|--------|------|---------|------|
| Protocol | HTTP/1.1+ | HTTP/1.1+ | HTTP/2 |
| Format | JSON/XML | JSON | Protobuf |
| Typing | ❌ No standard | ✅ Schema | ✅ Strong |
| Browser | ✅ Universal | ✅ Yes | ⚠️ Limited |
| Performance | Baseline | Similar | 5-10x faster |
| Best For | Public APIs | Complex clients | Internal services |
| Learning Curve | Low | Medium | Medium |

---

## 🔐 API Security Essentials
- Always use HTTPS
- Authenticate every request (JWT, API key, OAuth)
- Rate limit → [[Rate Limiting]]
- Validate all inputs
- Use CORS properly
- Never expose internal IDs in URLs (use UUIDs)

---

## 🔗 Related Notes
- [[Security]] — Auth, JWT, OAuth in APIs
- [[Rate Limiting]] — Protect APIs from abuse
- [[Microservices vs Monolith]] — APIs connect services
- [[Networking Basics]] — HTTP, WebSockets protocols

