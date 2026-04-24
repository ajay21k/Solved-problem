---
tags: [system-design, components, proxy, nginx]
links: [[Load Balancing]], [[Security]], [[Microservices vs Monolith]]
---

# 🔀 Proxies (Forward & Reverse)

> [!abstract] Summary
> Proxies sit between clients and servers, adding a layer of control, security, and performance optimization.

---

## ➡️ Forward Proxy

Sits between **client and the internet**. Client → Forward Proxy → Internet.

```
[Client] ──► [Forward Proxy] ──► [Internet/Server]
```

**Client knows about the proxy. Server does NOT know who the real client is.**

### Use Cases
- 🕵️ **Anonymity**: Hide client's real IP
- 🔒 **Content filtering**: Block certain websites (corporate networks, schools)
- 🌍 **Bypass geo-restrictions**: Access content from another region
- 📊 **Traffic monitoring**: Log all outbound requests
- ⚡ **Caching**: Cache repeated requests to same resources

### Examples
- Corporate proxy servers
- VPN (conceptually similar)
- Tor network

---

## ⬅️ Reverse Proxy

Sits between **internet and backend servers**. Client → Reverse Proxy → Backend.

```
[Internet/Client] ──► [Reverse Proxy] ──► [Backend Server A]
                                       ──► [Backend Server B]
                                       ──► [Backend Server C]
```

**Server knows about the proxy. Client does NOT know which backend it's hitting.**

### Use Cases
- ⚖️ **Load balancing**: Distribute traffic across backends → [[Load Balancing]]
- 🔐 **SSL termination**: Decrypt HTTPS once at the proxy
- 🛡️ **Security**: Hide backend topology, DDoS protection
- ⚡ **Caching**: Cache responses to reduce backend load
- 🗜️ **Compression**: Gzip responses before sending to client
- 📝 **Logging**: Central request logging

### Examples
- **Nginx** — Most popular, high-performance
- **HAProxy** — Excellent for TCP/HTTP load balancing
- **Traefik** — Cloud-native, auto-discovers Docker/K8s
- **Envoy** — Used in service meshes

---

## ⚔️ Forward vs Reverse Proxy

| | Forward Proxy | Reverse Proxy |
|-|--------------|---------------|
| **Protects** | Clients | Servers |
| **Who knows about it** | Clients | Servers |
| **Who's hidden** | Client (from internet) | Backend (from client) |
| **Use case** | Content filtering, anonymity | Load balancing, SSL, caching |

---

## 🕸️ Service Mesh (Advanced)

Manages **service-to-service communication** in microservices without changing app code.

```
[Service A] ←→ [Sidecar Proxy] ←→ [Sidecar Proxy] ←→ [Service B]
                      ↕                      ↕
                 [Control Plane — Istio/Linkerd]
```

### What a Service Mesh Handles
| Concern | Without Mesh | With Mesh |
|---------|-------------|-----------|
| Load balancing | Manual in app | Automatic |
| Retries/timeouts | Manual in app | Config-driven |
| Circuit breaking | Manual in app | Automatic |
| mTLS (mutual TLS) | Complex setup | Automatic |
| Observability | Manual instrumentation | Automatic traces/metrics |

### Examples
- **Istio** — Feature-rich, Kubernetes-native
- **Linkerd** — Lightweight, easy to use
- **Consul Connect** — HashiCorp ecosystem

> [!note] When to use a Service Mesh
> When you have 10+ microservices and need consistent security, observability, and traffic management across all of them.

---

## 🔗 Related Notes
- [[Load Balancing]] — Reverse proxy often does load balancing
- [[Security]] — SSL termination, DDoS protection
- [[Microservices vs Monolith]] — Service mesh for microservices
- [[Observability]] — Proxies generate rich access logs

