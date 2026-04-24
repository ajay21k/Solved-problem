---
tags: [system-design, case-study, whatsapp, messaging, real-time]
links: [[Networking Basics]], [[Databases]], [[High Availability]], [[Security]]
---

# 💬 Design WhatsApp / Chat Application

> [!abstract] Summary
> Design a real-time messaging app supporting 1:1 chats, group chats, online presence, message delivery receipts, and offline notifications.

---

## 📋 Requirements

### Functional Requirements
- ✅ 1:1 real-time messaging
- ✅ Group chats (up to 256 members)
- ✅ Online / offline presence (last seen)
- ✅ Message delivery & read receipts (✓✓)
- ✅ Media sharing (images, videos, documents)
- ✅ Push notifications for offline users
- ✅ End-to-end encryption

### Non-Functional Requirements
- 2B registered users, 500M DAU
- Send/receive messages in < 100ms
- Messages delivered with no loss (even if offline for days)
- 99.99% availability

---

## 📐 Capacity Estimation

```
DAU:              500M
Messages/user/day: 40
Total messages/day: 20B
Messages/sec:       ~230,000 msg/sec

Message size:       ~100 bytes (text)
Storage/day:        20B × 100 bytes = ~2 TB/day

Connections:        500M persistent WebSocket connections!
                    → Need many Chat Servers, ~10M conns/server = 50 servers minimum
```

---

## 🏗️ High-Level Architecture

```
Client ──WebSocket──► [Chat Server N]
                             │
                      [Session Service]  ← "user_123 → chat_server_7"
                             │
                      [Message Store]    ← Cassandra
                             │
                      [Notification]     ← If offline → APNs/FCM
```

---

## 🔌 Connection Management

### Why WebSockets?
- HTTP is request-response — server can't push messages proactively
- WebSocket = persistent, bidirectional, low-latency
- Each client maintains one WebSocket connection to a Chat Server

### Chat Server Assignment
```
Client connects → Load Balancer → Chat Server A
                                  Chat Server A registers: "user_123 → server_A" in Redis

Bob sends to Alice:
1. Bob's message → Chat Server B
2. Chat Server B looks up: "where is Alice?" → Redis → Server A
3. Server B sends to Server A via internal channel
4. Server A delivers to Alice's WebSocket
```

---

## 📨 Message Flow (1:1)

```
Alice sends "Hello" to Bob:

1. Alice → WebSocket → Chat Server A
2. Server A persists message to Cassandra (status: SENT ✓)
3. Server A looks up Bob's connection in Session Service
4a. Bob ONLINE → Server A routes to Bob's server → Bob's WebSocket
    → Update status: DELIVERED ✓✓
4b. Bob OFFLINE → Message stored, Push notification sent via FCM/APNs
5. Bob comes online → fetches unread messages from Cassandra
6. Bob reads message → status: READ ✓✓ (blue ticks)
```

---

## 👥 Group Chat Design

```
Alice sends to Group "Friends" (Bob, Charlie, Dave):

1. Alice → Group Message Service
2. Service retrieves group member list
3. Fan-out: push to each member's Chat Server
   (like Twitter fanout, but max 256 members so manageable)
4. Each member gets delivered/read receipts per-user
```

### Group Message Storage
```
Table: group_messages
  (group_id, message_id, sender_id, content, created_at)

Table: message_receipts
  (message_id, user_id, status: sent|delivered|read, timestamp)
```

---

## 👁️ Presence (Online/Offline)

```
User opens app → WebSocket connected → Heartbeat every 30s to Presence Service
User closes app → Connection drops → Presence Service marks offline

Presence stored in Redis:
  "presence:user_123" → {status: "online", last_seen: timestamp}
  TTL: 60 seconds (auto-expires if no heartbeat)
```

> [!note] "Last Seen" Privacy
> Allow users to hide last seen — only expose to contacts, or hide entirely. Check permission before returning presence data.

---

## 💾 Message Storage (Cassandra)

```sql
CREATE TABLE messages (
  conversation_id  UUID,
  message_id       TIMEUUID,  -- time-ordered UUID
  sender_id        UUID,
  content          TEXT,
  media_url        TEXT,
  status           TEXT,
  created_at       TIMESTAMP,
  PRIMARY KEY (conversation_id, message_id)
) WITH CLUSTERING ORDER BY (message_id DESC);
```

- **Partition key**: `conversation_id` → all messages for a chat on same shard
- **Clustering key**: `message_id` (TIMEUUID) → sorted by time automatically
- Fetch last 50 messages: `WHERE conversation_id = ? LIMIT 50`

---

## 🔐 End-to-End Encryption (E2EE)

```
Signal Protocol (used by WhatsApp):

1. Alice & Bob each have: Public Key + Private Key
2. Key Exchange: Alice gets Bob's public key from server
3. Encryption: Alice encrypts message with Bob's public key
4. Server stores encrypted blob — cannot read it
5. Bob decrypts with his private key
```

> [!important] Server cannot read E2EE messages — only metadata (who, when, how much)

---

## 📱 Push Notifications (Offline Users)

```
Message arrives for offline user:
→ Notification Service
→ Check user's device token
→ APNs (Apple Push Notification Service) for iOS
→ FCM (Firebase Cloud Messaging) for Android
→ Device receives push → user opens app → fetches messages
```

---

## 🔑 Key Design Decisions

| Decision | Choice | Reason |
|----------|--------|--------|
| Real-time protocol | WebSocket | Bidirectional, low latency |
| Message storage | Cassandra | High write throughput, time-ordered |
| Presence | Redis with TTL | Fast, auto-expire |
| Encryption | Signal Protocol (E2EE) | Privacy, can't read at server |
| Offline delivery | Persistent store + Push | Messages never lost |
| Group chat fanout | Fan-out per message | Max 256 members, manageable |

---

## 🔗 Related Notes
- [[Networking Basics]] — WebSocket protocol
- [[Databases]] — Cassandra for message storage
- [[Caching]] — Redis for presence, session lookup
- [[Security]] — End-to-end encryption
- [[High Availability]] — Message durability guarantees

