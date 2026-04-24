---
tags: [system-design, components, cdn]
links: [[Caching]], [[Networking Basics]], [[Load Balancing]]
---

# 🌍 CDN (Content Delivery Network)

> [!abstract] Summary
> A CDN is a geographically distributed network of edge servers that cache and serve content close to users.

---

## 🔄 How CDN Works

```
Without CDN:  User (Chennai) ──────────────────► Origin Server (US) — 200ms!
With CDN:     User (Chennai) ──► Edge (Mumbai) ──► Origin (US) — 20ms! ✅
                                      ↑ cached after first request
```

1. User requests content (image, JS, video)
2. DNS resolves to **nearest edge server**
3. **Cache HIT** → served immediately from edge
4. **Cache MISS** → fetched from origin, cached at edge, served to user

---

## ✅ Benefits

| Benefit | How |
|---------|-----|
| ⚡ Reduced Latency | Content served from nearby server |
| 📉 Reduced Origin Load | Edge handles most requests |
| 🛡️ DDoS Protection | Absorbs traffic at the edge |
| 📈 High Availability | If origin is down, CDN serves cached content |
| 💰 Cost Savings | Less bandwidth from origin |

---

## 🔑 Cache-Control Headers

```http
Cache-Control: max-age=86400        # Cache for 1 day
Cache-Control: no-cache             # Revalidate with origin before serving
Cache-Control: no-store             # Never cache (sensitive data)
Cache-Control: public, max-age=3600 # CDN can cache for 1 hour
Cache-Control: private              # Browser only, not CDN
ETag: "abc123"                      # Version fingerprint for revalidation
```

---

## 📦 What to Put on a CDN?

| ✅ Good for CDN | ❌ Not for CDN |
|----------------|---------------|
| Images, videos | Dynamic API responses |
| JS, CSS bundles | User-specific data |
| HTML (static sites) | Real-time data |
| Fonts | Authenticated content |
| Large file downloads | Payment pages |

---

## 🏢 CDN Providers

- **Cloudflare** — Free tier, DDoS protection, global
- **AWS CloudFront** — Tight AWS integration
- **Akamai** — Enterprise, largest network
- **Fastly** — Real-time purging, developer-friendly
- **Azure CDN** — Azure-native

---

## 🔗 Related Notes
- [[Caching]] — CDN is caching at the network edge
- [[Design YouTube]] — Videos served via CDN
- [[Networking Basics]] — DNS-based geographic routing

