---
tags: [system-design, monitoring, observability, metrics, logs, traces]
links: [[High Availability]], [[Microservices vs Monolith]], [[Key Concepts]]
---

# 📊 Monitoring & Observability

> [!abstract] Summary
> Observability lets you understand the internal state of a system from its external outputs — metrics, logs, and traces.

---

## 🔭 The 3 Pillars of Observability

```
┌─────────────┬──────────────────┬──────────────────┐
│   METRICS   │      LOGS        │     TRACES       │
│             │                  │                  │
│ Numbers     │ Text records     │ Request journey  │
│ over time   │ of events        │ across services  │
│             │                  │                  │
│ "CPU at 85%"│ "Error: DB conn  │ "Request took    │
│             │  failed at 14:03"│  200ms: 50ms DB  │
│             │                  │  + 150ms API"    │
└─────────────┴──────────────────┴──────────────────┘
```

---

## 📈 Metrics

Numerical, time-series data about system behavior.

### Key Metric Frameworks

#### RED Method (for Services)
| Metric | What to Measure |
|--------|----------------|
| **R**ate | Requests per second |
| **E**rrors | Error rate (%) |
| **D**uration | Latency (p50, p95, p99) |

#### USE Method (for Resources)
| Metric | What to Measure |
|--------|----------------|
| **U**tilization | CPU %, memory %, disk % |
| **S**aturation | Queue depth, wait time |
| **E**rrors | Hardware errors, dropped packets |

### Important Metrics to Monitor
| Category | Metrics |
|----------|---------|
| **Application** | RPS, error rate, p99 latency |
| **Database** | Query time, connection pool usage, replication lag |
| **Cache** | Hit rate, miss rate, eviction rate |
| **Queue** | Queue depth, consumer lag, publish rate |
| **Infrastructure** | CPU, memory, disk I/O, network I/O |

### Percentiles vs Averages
```
Response times: [1ms, 2ms, 3ms, 5ms, 500ms]
Average: 102ms  ← misleading!
p50: 3ms        ← 50% of requests are faster than this
p95: 500ms      ← 5% of users experience this
p99: 500ms      ← the "worst" 1% (important for SLOs!)
```

> [!important] Always use percentiles, not averages for latency!

### Metrics Tools
- **Prometheus** — Open source, pull-based, time-series DB
- **Grafana** — Visualization dashboards
- **Datadog** — Full-stack SaaS monitoring
- **AWS CloudWatch** — AWS-native metrics
- **StatsD** — Lightweight metrics aggregation

---

## 📝 Logs

Timestamped records of discrete events.

### Log Levels
| Level | When to Use |
|-------|-------------|
| **DEBUG** | Detailed info for debugging (dev only) |
| **INFO** | Normal operations, request completions |
| **WARN** | Unexpected but handled situations |
| **ERROR** | Failures that need attention |
| **FATAL** | System cannot continue |

### Structured Logging (JSON)
```json
{
  "timestamp": "2026-03-21T10:30:00Z",
  "level": "ERROR",
  "service": "payment-service",
  "trace_id": "abc123",
  "user_id": "user_456",
  "message": "Payment failed",
  "error": "Card declined",
  "duration_ms": 245
}
```

> [!tip] Always use structured logs (JSON) in production — they're queryable!

### Log Aggregation Stack

```
App Servers → [Logstash/Fluentd] → [Elasticsearch] → [Kibana]
               (collect/parse)        (store/index)    (visualize)
```

- **ELK Stack**: Elasticsearch + Logstash + Kibana
- **EFK Stack**: Elasticsearch + Fluentd + Kibana
- **Loki + Grafana**: Lightweight alternative (label-based)
- **Splunk**: Enterprise log management

---

## 🔍 Distributed Tracing

Track a **single request** as it flows through multiple services.

```
Request: POST /checkout
  │
  ├─ [API Gateway]      0ms - 5ms
  │
  ├─ [Order Service]    5ms - 80ms
  │    │
  │    ├─ [DB Query]    10ms - 40ms
  │    └─ [Redis]       50ms - 55ms
  │
  └─ [Payment Service]  80ms - 200ms
       │
       └─ [Stripe API]  85ms - 195ms  ← 🐢 bottleneck!
```

### Key Concepts
| Term | Description |
|------|-------------|
| **Trace** | Full journey of one request |
| **Span** | Single operation within a trace |
| **Trace ID** | Unique ID propagated through all services |
| **Parent Span** | The calling service |
| **Child Span** | The called service |

### Tracing Tools
- **Jaeger** — Open source, CNCF project
- **Zipkin** — Open source, originally from Twitter
- **AWS X-Ray** — AWS-native
- **Datadog APM** — Commercial, full-stack
- **OpenTelemetry** — Industry standard SDK (vendor-neutral)

> [!important] OpenTelemetry
> Use OpenTelemetry for instrumentation — it's the open standard that works with any backend (Jaeger, Datadog, etc.). Instrument once, switch backends freely.

---

## 🚨 Alerting

### Good Alert Principles
- ✅ Alert on **symptoms** (user impact), not causes
- ✅ Every alert should be **actionable**
- ✅ Include **runbook links** in alerts
- ✅ Set appropriate severity levels
- ❌ Avoid alert fatigue — too many alerts = ignored alerts

### Alert Severity Levels
| Level | Meaning | Response |
|-------|---------|----------|
| **P1 Critical** | Service down, all users affected | Immediate, wake up on-call |
| **P2 High** | Degraded, some users affected | Within 30 min |
| **P3 Medium** | Warning threshold crossed | Business hours |
| **P4 Low** | Informational | Ticket for review |

### On-Call Tools
- **PagerDuty** — Incident management, escalation policies
- **OpsGenie** — On-call scheduling, alert routing
- **Slack + Webhooks** — Simple alert notifications

---

## 🏠 Dashboards

### Golden Signals Dashboard (per service)
1. **Latency** — p50, p95, p99 response time
2. **Traffic** — Requests per second
3. **Errors** — Error rate %
4. **Saturation** — CPU, memory, queue depth

---

## 🔗 Related Notes
- [[High Availability]] — Monitoring detects failures
- [[Microservices vs Monolith]] — Distributed tracing essential for microservices
- [[Key Concepts]] — SLI/SLO/SLA definitions
- [[Interview Framework]] — Know RED/USE methods for interviews

