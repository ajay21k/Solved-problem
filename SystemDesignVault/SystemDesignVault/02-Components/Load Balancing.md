---
tags: [system-design, components, load-balancing]
links: [[Scalability]], [[High Availability]], [[Proxies]]
---

# ⚖️ Load Balancing

> [!abstract] Summary
> A Load Balancer distributes incoming traffic across multiple servers to prevent overload and improve availability.

---

## 🎯 Why Load Balancers?

```
Without LB:          With LB:
                     
Client → Server A    Client ─┐
                     Client ─┤→ [Load Balancer] → Server A
                     Client ─┘                  → Server B
                                                → Server C
```

- **Prevents** single server overload
- **Increases** availability (removes unhealthy servers)
- **Enables** horizontal scaling
- **Hides** server infrastructure from clients

---

## 🔢 Types of Load Balancers

### L4 — Transport Layer (TCP/UDP)
- Routes based on **IP + Port** only
- Doesn't inspect content
- Very fast, low overhead
- Example: **AWS NLB** (Network Load Balancer)

### L7 — Application Layer (HTTP/HTTPS)
- Routes based on **content** (URL, headers, cookies, payload)
- Can do SSL termination, A/B testing, path-based routing
- Slightly more overhead but far more powerful
- Example: **AWS ALB**, **Nginx**, **HAProxy**

> [!tip] Which to Use?
> Use **L7** for web applications (almost always). Use **L4** for raw TCP (databases, game servers, IoT).

---

## 🔄 Load Balancing Algorithms

### Round Robin
```
Request 1 → Server A
Request 2 → Server B
Request 3 → Server C
Request 4 → Server A  ← back to start
```
- **Best for**: Identical server capacities, short requests.

### Weighted Round Robin
```
Server A (weight 3): gets 3x more requests
Server B (weight 1): gets baseline requests
```
- **Best for**: Servers with different capacities.

### Least Connections
- Send request to server with **fewest active connections**.
- **Best for**: Long-lived connections (database proxies, file uploads).

### Least Response Time
- Send to server with lowest **latency + fewest connections**.
- Most intelligent routing.

### IP Hash
- `server = hash(client_IP) % num_servers`
- Same client always hits same server.
- **Best for**: Session stickiness (stateful apps).

### Random
- Randomly pick a server.
- Surprisingly effective at scale with many servers.

---

## ❤️ Health Checks

Load balancers continuously verify server health:

```
LB → "GET /health" → Server A → 200 OK ✅ (keep in pool)
LB → "GET /health" → Server B → timeout ❌ (remove from pool)
```

- **Active checks**: LB pings servers periodically
- **Passive checks**: LB monitors real traffic for errors

---

## 🔐 SSL/TLS Termination

Load balancer handles SSL decryption so backend servers don't have to:

```
HTTPS Client → [LB decrypts SSL] → HTTP Backend Servers
```

- Reduces CPU load on app servers
- Centralizes certificate management

---

## 🔗 Load Balancer vs Reverse Proxy

| Feature | Load Balancer | Reverse Proxy |
|---------|--------------|---------------|
| Purpose | Distribute traffic | Forward requests |
| Backends | Multiple (required) | One or more |
| Examples | AWS ALB, HAProxy | Nginx, Traefik |
| Can do both? | Often yes | Often yes |

> [!note] In practice, most reverse proxies (Nginx) also do load balancing.

---

## 📦 Sticky Sessions

Force a client to always reach the **same server** (for stateful apps):
- Cookie-based: LB sets a cookie pointing to a server ID
- IP-based: Hash the client IP

> [!warning] Avoid Sticky Sessions
> Sticky sessions make horizontal scaling harder and create uneven load. Prefer stateless app design with shared session store (Redis).

---

## 🔗 Related Notes
- [[Scalability]] — Horizontal scaling needs a load balancer
- [[High Availability]] — Redundant load balancers for HA
- [[Proxies]] — Forward vs reverse proxy
- [[Rate Limiting]] — LBs can enforce rate limits
- [[Security]] — SSL termination at LB

