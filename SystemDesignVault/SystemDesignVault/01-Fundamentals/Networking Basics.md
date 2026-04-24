---
tags: [system-design, fundamentals, networking]
links: [[API Design]], [[CDN]], [[Load Balancing]]
---

# 🌐 Networking Basics

> [!abstract] Summary
> Core networking concepts every system designer needs: DNS, TCP/UDP, HTTP, and real-time protocols.

---

## 🌍 DNS (Domain Name System)

Translates human-readable domain names → IP addresses.

### Resolution Flow
```
Browser → OS Cache → Local Resolver → Root DNS → TLD DNS (.com) → Authoritative DNS → IP Address
```

### DNS Record Types

| Record | Purpose | Example |
|--------|---------|---------|
| **A** | Domain → IPv4 | `api.example.com → 1.2.3.4` |
| **AAAA** | Domain → IPv6 | `api.example.com → 2001:db8::1` |
| **CNAME** | Alias to another domain | `www → example.com` |
| **MX** | Mail server | `mail.example.com` |
| **TXT** | Verification, SPF, DKIM | `v=spf1 include:...` |
| **NS** | Nameserver for domain | `ns1.example.com` |

> [!note] TTL (Time to Live)
> Controls how long DNS records are cached. Low TTL = faster propagation but more queries.

---

## 🔒 HTTP vs HTTPS

| | HTTP | HTTPS |
|-|------|-------|
| Encryption | ❌ Plaintext | ✅ TLS encrypted |
| Port | 80 | 443 |
| Certificate | None | SSL/TLS cert |
| Performance | Slightly faster | Minimal overhead (modern TLS) |

### TLS Handshake (Simplified)
```
1. Client Hello (supported cipher suites)
2. Server Hello + Certificate
3. Client verifies cert with CA
4. Key exchange (Diffie-Hellman)
5. Symmetric encryption begins
```

---

## 📡 TCP vs UDP

| Feature | TCP | UDP |
|---------|-----|-----|
| Connection | Connection-oriented (3-way handshake) | Connectionless |
| Reliability | Guaranteed delivery, retransmits | Best-effort, no retransmit |
| Order | In-order delivery | No ordering guarantee |
| Speed | Slower (overhead) | Faster |
| Header Size | 20 bytes | 8 bytes |
| **Use Case** | HTTP, SSH, FTP, email | DNS, video stream, gaming, VoIP |

> [!tip] When to use UDP
> Use UDP when **speed > reliability** — live video, online games. A dropped frame is better than a frozen one.

---

## 🔄 HTTP Versions

| Version | Key Features |
|---------|-------------|
| **HTTP/1.0** | New connection per request |
| **HTTP/1.1** | Persistent connections, pipelining (but HOL blocking) |
| **HTTP/2** | Multiplexing, header compression (HPACK), server push |
| **HTTP/3** | QUIC (UDP-based), 0-RTT, no HOL blocking, better mobile |

> [!important] HTTP/2 Multiplexing
> Multiple requests on a single TCP connection simultaneously. Eliminates head-of-line blocking at the HTTP layer.

---

## 🔌 Real-Time Communication Patterns

### Polling
```
Client → "Any updates?" → Server → "No"
Client → "Any updates?" → Server → "No"
Client → "Any updates?" → Server → "Yes! Here's data"
```
- Simple but **wasteful** — most requests return nothing.

### Long Polling
```
Client → Request → Server holds open → (event happens) → Server responds → Client immediately re-requests
```
- Better than polling, but still HTTP overhead per event.

### WebSockets
```
Client ←————————— Persistent Bidirectional Connection ——————————→ Server
```
- Full-duplex over a single TCP connection.
- Low latency, real-time.
- **Use cases**: Chat, live collaboration, gaming, stock prices.

### Server-Sent Events (SSE)
```
Client ← ← ← One-way stream from server
```
- One-directional (server → client only).
- Simple, built on HTTP.
- **Use cases**: Live feeds, dashboards, notifications.

### Comparison

| Method | Direction | Protocol | Overhead | Use Case |
|--------|-----------|----------|----------|----------|
| Polling | Client→Server | HTTP | High | Simple status checks |
| Long Polling | Both (simulated) | HTTP | Medium | Chat (legacy) |
| WebSocket | Bidirectional | WS | Low | Real-time apps |
| SSE | Server→Client | HTTP | Low | Live feeds |

---

## 🌐 IP Addressing

- **IPv4**: 32-bit, ~4 billion addresses (exhausted)
- **IPv6**: 128-bit, virtually unlimited
- **Private IPs**: `10.x.x.x`, `172.16.x.x`, `192.168.x.x` (not routable on internet)
- **Localhost**: `127.0.0.1`

---

## 🔗 Related Notes
- [[API Design]] — HTTP methods, REST, gRPC protocols
- [[CDN]] — DNS-based geographic routing
- [[Load Balancing]] — L4 (TCP) vs L7 (HTTP) load balancing
- [[Security]] — TLS, HTTPS, encryption in transit

