---
tags: [system-design, architecture, security, auth, jwt, oauth]
links: [[API Design]], [[Rate Limiting]], [[Networking Basics]]
---

# 🔐 Security in System Design

> [!abstract] Summary
> Security must be designed in from the start — authentication, authorization, encryption, and protection against common attacks.

---

## 🪪 Authentication vs Authorization

| | Authentication (AuthN) | Authorization (AuthZ) |
|-|----------------------|----------------------|
| **Question** | Who are you? | What can you do? |
| **Mechanism** | Login, JWT, OAuth | Roles, permissions, ACL |
| **Example** | "You are Alice" | "Alice can read posts but not delete" |
| **When fails** | 401 Unauthorized | 403 Forbidden |

---

## 🎟️ JWT (JSON Web Token)

```
eyJhbGciOiJIUzI1NiJ9.eyJzdWIiOiJ1c2VyXzEyMyIsImV4cCI6MTcxMTAyOTYwMH0.signature
      HEADER                        PAYLOAD                                SIGNATURE
```

### Structure
| Part | Contains | Base64 encoded? |
|------|---------|-----------------|
| Header | Algorithm (`HS256`), type (`JWT`) | ✅ |
| Payload | Claims: `sub`, `exp`, `iat`, roles | ✅ |
| Signature | `HMAC(header.payload, secret)` | ✅ |

### JWT Flow
```
1. User logs in → Server validates credentials
2. Server creates JWT with payload + signs with secret
3. Client stores JWT (localStorage or HttpOnly cookie)
4. Client sends JWT in every request: Authorization: Bearer <token>
5. Server verifies signature — no DB lookup needed ✅
```

### JWT Best Practices
- ✅ Keep tokens short-lived (15 min - 1 hour)
- ✅ Use refresh tokens for long sessions
- ✅ Store in HttpOnly cookies (prevents XSS)
- ❌ Never store sensitive data in payload (it's just base64, not encrypted)
- ❌ Never put JWT in URL parameters

> [!warning] JWT Revocation Problem
> You can't invalidate individual JWTs before expiry (they're stateless). Solutions: short TTL + refresh tokens, or maintain a blacklist in Redis.

---

## 🔑 OAuth 2.0 & OpenID Connect

### OAuth 2.0 — Authorization Framework
> "I allow this app to access my Google Drive" without sharing my password

```
User → "Login with Google" → [Your App]
                                  ↓ redirect to Google
User authenticates with Google
Google → issues Authorization Code → [Your App]
[Your App] exchanges code for Access Token
[Your App] uses Access Token to call Google APIs
```

### OpenID Connect (OIDC)
- **OAuth 2.0 + Identity layer**
- OAuth tells you what the user can do; OIDC tells you **who they are**
- Returns an **ID Token** (JWT) with user info

### OAuth Grant Types
| Type | Use Case |
|------|----------|
| **Authorization Code** | Web apps with server backend (most secure) |
| **Authorization Code + PKCE** | Mobile/SPA apps |
| **Client Credentials** | Server-to-server (no user) |
| **Implicit** | Deprecated — don't use |

---

## 🛡️ Common Attacks & Defenses

| Attack | Description | Defense |
|--------|-------------|---------|
| **SQL Injection** | Malicious SQL in input | Parameterized queries, ORM |
| **XSS** (Cross-Site Scripting) | Inject JS into pages | Sanitize input, CSP headers, encode output |
| **CSRF** (Cross-Site Request Forgery) | Trick user's browser to make requests | CSRF tokens, SameSite cookies |
| **DDoS** | Flood with requests | Rate limiting, CDN, WAF |
| **MITM** (Man-in-the-Middle) | Intercept traffic | HTTPS everywhere, HSTS |
| **Brute Force** | Try all passwords | Rate limiting, lockout, CAPTCHA, MFA |
| **IDOR** | Access other users' data | Validate ownership server-side |
| **Injection** | Inject commands/scripts | Validate, sanitize, whitelist inputs |

### SQL Injection Example
```sql
-- ❌ VULNERABLE
query = "SELECT * FROM users WHERE email = '" + email + "'"
-- Input: ' OR '1'='1  → Returns ALL users!

-- ✅ SAFE
query = "SELECT * FROM users WHERE email = ?"
params = [email]
```

---

## 🔒 Encryption

### Encryption at Rest
- Encrypt data stored in DB, disks, backups
- AES-256 standard
- Key management: AWS KMS, HashiCorp Vault

### Encryption in Transit
- TLS/HTTPS for all communications
- Enforce HTTPS with HSTS header
- Never send sensitive data over HTTP

### End-to-End Encryption (E2EE)
- Only sender and recipient can decrypt
- Server can't read content
- Used in: WhatsApp, Signal
- Challenges: Can't search encrypted content, harder backups

---

## 🔑 Password Storage
```
❌ NEVER store plaintext passwords
❌ NEVER store MD5/SHA1 hashes (reversible with rainbow tables)

✅ Use bcrypt, Argon2, or scrypt:
   hash = bcrypt(password + salt, cost=12)
```

---

## 🔗 Related Notes
- [[API Design]] — Secure your API endpoints
- [[Rate Limiting]] — Brute force protection
- [[Networking Basics]] — TLS, HTTPS
- [[Design WhatsApp]] — End-to-end encryption

