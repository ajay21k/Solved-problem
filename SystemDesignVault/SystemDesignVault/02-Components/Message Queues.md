---
tags: [system-design, components, messaging, kafka, async]
links: [[Microservices vs Monolith]], [[High Availability]], [[Scalability]]
---

# 📨 Message Queues & Event-Driven Architecture

> [!abstract] Summary
> Message queues decouple producers and consumers, enabling async processing, traffic buffering, and resilient architectures.

---

## 🤔 Why Message Queues?

```
Without Queue (Tight Coupling):
[Order Service] ──synchronous call──► [Email Service]
                                       If email service is down → order fails!

With Queue (Loose Coupling):
[Order Service] ──► [Queue] ──► [Email Service]
                    (buffer)     (processes when ready)
```

**Benefits**:
- ✅ **Decoupling** — services don't need to know about each other
- ✅ **Buffering** — absorb traffic spikes
- ✅ **Async processing** — respond to user immediately
- ✅ **Durability** — messages persist even if consumer is down
- ✅ **Retry** — failed messages can be retried

---

## 🛠️ Popular Message Queue Tools

| Tool | Type | Key Strength |
|------|------|-------------|
| **Apache Kafka** | Distributed log | High throughput, event streaming, replay |
| **RabbitMQ** | Message broker | Flexible routing, task queues |
| **AWS SQS** | Managed queue | Cloud-native, fully managed |
| **AWS SNS** | Managed pub-sub | Fan-out notifications |
| **Redis Streams** | In-memory stream | Lightweight, low latency |
| **Google Pub/Sub** | Managed pub-sub | GCP-native |
| **NATS** | Lightweight broker | IoT, edge computing |

---

## 🔴 Apache Kafka Deep Dive

### Core Concepts
```
Producer → [Topic: orders] → Consumer Group
                ├── Partition 0: msg1, msg2, msg3...
                ├── Partition 1: msg4, msg5, msg6...
                └── Partition 2: msg7, msg8, msg9...
```

| Concept | Description |
|---------|-------------|
| **Topic** | Named stream of messages |
| **Partition** | Topic split into ordered, immutable log segments |
| **Offset** | Message position within a partition |
| **Producer** | Publishes messages to topics |
| **Consumer** | Reads messages from topics |
| **Consumer Group** | Multiple consumers sharing partitions (parallelism) |
| **Broker** | Kafka server node |
| **Zookeeper/KRaft** | Cluster coordination |

### Key Kafka Properties
- **Retention**: Messages kept for configurable duration (not deleted after consumption)
- **Replayability**: Consumers can re-read old messages by resetting offset
- **Ordering**: Guaranteed within a partition (not across partitions)
- **Throughput**: Millions of messages/sec

### Kafka vs Traditional Queue

| Feature | Kafka | RabbitMQ/SQS |
|---------|-------|-------------|
| Message deleted after read | ❌ No (retained) | ✅ Yes |
| Multiple consumers of same message | ✅ Yes | ❌ No (or fan-out needed) |
| Ordering guarantee | Per-partition | Per-queue |
| Throughput | Extremely high | Moderate-high |
| Replay messages | ✅ Yes | ❌ No |
| Use case | Event streaming, analytics | Task queues, work distribution |

---

## 📡 Messaging Patterns

### Point-to-Point (Competing Consumers)
```
Producer → [Queue] → Consumer A (gets some)
                  → Consumer B (gets rest)
```
- One message processed by **one** consumer
- **Use case**: Task distribution, job queues

### Publish-Subscribe (Fan-Out)
```
Producer → [Topic] → Consumer A (all messages)
                  → Consumer B (all messages)
                  → Consumer C (all messages)
```
- One message delivered to **all** subscribers
- **Use case**: Event notifications, broadcasting

### Fan-Out Pattern
```
SNS Topic → SQS Queue 1 → Email Service
          → SQS Queue 2 → Push Notification Service
          → SQS Queue 3 → Analytics Service
```
- One event triggers multiple independent workflows

### Dead Letter Queue (DLQ)
```
[Queue] → Consumer fails 3 times → [Dead Letter Queue]
                                    ↑ Alert + manual review
```
- Captures messages that failed processing
- Prevents poison pill messages from blocking the queue

---

## ⚡ Event-Driven Architecture (EDA)

### What Is It?
Services communicate through **events** rather than direct API calls.

```
User registers → [UserCreated event] → Email Service (sends welcome)
                                    → Analytics Service (tracks signup)
                                    → Recommendation Service (initializes preferences)
```

### Benefits
- ✅ Loose coupling — services don't call each other
- ✅ Easy to add new consumers without modifying producer
- ✅ Scales independently per service
- ✅ Resilient — if one service is down, events queue up

### Challenges
- ❌ Eventual consistency — hard to trace what happened
- ❌ Debugging is harder (distributed traces needed)
- ❌ Message ordering across services is complex
- ❌ At-least-once delivery → idempotent consumers needed

### Event Sourcing
- Store every state change as an **event** (not just current state)
- Can replay events to reconstruct any point-in-time state
- **Use case**: Audit logs, financial transactions, CQRS

### CQRS (Command Query Responsibility Segregation)
```
Write side: Commands → Event Store → Write DB (optimized for writes)
Read side:  Events  → Projections → Read DB (optimized for reads)
```

---

## 🔄 Message Delivery Guarantees

| Guarantee | Description | Risk |
|-----------|-------------|------|
| **At-most-once** | Message may be lost, never duplicated | Data loss |
| **At-least-once** | Message delivered at least once, may duplicate | Duplicates |
| **Exactly-once** | Delivered precisely once (hardest) | Complex, slow |

> [!important] At-Least-Once + Idempotency
> Most systems use at-least-once delivery. Make your consumers **idempotent** — processing the same message twice has no side effect (e.g., use message ID to deduplicate).

---

## 🔗 Related Notes
- [[Microservices vs Monolith]] — Async messaging enables microservice decoupling
- [[High Availability]] — Queues absorb failures and smooth traffic
- [[Scalability]] — Async processing helps scale write-heavy systems
- [[Design Twitter]] — Kafka used for event streaming in feed
- [[Observability]] — Trace messages across services

