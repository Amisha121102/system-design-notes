# 9. Monitoring & Logging

---

## Logging

Recording **events** that happen in your system for debugging and auditing.

### What to log
- Errors and exceptions
- API requests and responses
- User actions (login, signup, purchase)
- State changes (order created → shipped → delivered)

### Log Levels

| Level | When to use | Example |
|-------|-------------|---------|
| **DEBUG** | Detailed info for development | `"Fetching user 123 from cache"` |
| **INFO** | Normal operations | `"Order #456 placed successfully"` |
| **WARN** | Something unexpected but not broken | `"Cache miss, falling back to DB"` |
| **ERROR** | Something failed | `"Payment failed for order #456"` |
| **FATAL** | System is crashing | `"Database connection pool exhausted"` |

### Structured Logging
Log in a **parseable format** (JSON) instead of plain text.

```json
{
  "timestamp": "2026-03-27T10:30:00Z",
  "level": "ERROR",
  "service": "payment-service",
  "message": "Payment failed",
  "orderId": "456",
  "userId": "123",
  "error": "Card declined"
}
```

### Tools
- **ELK Stack:** Elasticsearch (search) + Logstash (collect) + Kibana (visualize)
- **Splunk:** Enterprise log management and analysis
- **Fluentd:** Open-source log collector
- **Loki:** Lightweight log aggregation by Grafana

### Centralized Logging
In microservices, each service generates its own logs. **Aggregate all logs** into one place for easy searching.

```
Service A → logs → [Log Collector (Fluentd)] → [Elasticsearch] → [Kibana Dashboard]
Service B → logs →
Service C → logs →
```

---

## Monitoring Metrics

Track **system health** using quantitative measurements.

### Key Metrics

**1. Infrastructure Metrics**
- CPU usage, memory usage, disk I/O
- Network throughput, packet loss

**2. Application Metrics**
- Request rate (requests/sec)
- Error rate (% of failed requests)
- Latency (p50, p95, p99 response times)
- Throughput (successful operations/sec)

**3. Business Metrics**
- Active users, orders/minute, revenue/hour

### The Four Golden Signals (Google SRE)
1. **Latency** — how long requests take
2. **Traffic** — how many requests are coming in
3. **Errors** — how many requests are failing
4. **Saturation** — how full your resources are

### Tools
- **Prometheus:** Open-source metrics collection and alerting
- **Grafana:** Visualization dashboards (pairs with Prometheus)
- **Datadog:** Full observability platform (metrics + logs + traces)
- **New Relic:** Application performance monitoring

### Example Dashboard
```
┌──────────────────────────────────────────┐
│  Request Rate: 1,200/sec    CPU: 45%     │
│  Error Rate: 0.3%           Memory: 72%  │
│  p99 Latency: 230ms        Disk: 55%    │
│  Active Users: 8,400                     │
└──────────────────────────────────────────┘
```

---

## Anomaly Detection

Automatically detect **unusual patterns** that may indicate problems.

### What to detect
- Sudden traffic spikes or drops
- Error rate surges
- Latency increases
- Unusual resource consumption

### Approaches

**1. Static Thresholds**
- Set fixed rules: "Alert if error rate > 5%"
- Simple but doesn't adapt to changing patterns
```
Normal error rate: 0.5%
Threshold: 5%
Current: 8% → ALERT!
```

**2. Statistical Methods**
- Use **standard deviation** — alert if a metric goes beyond 2-3 standard deviations from the mean
- Adapts to normal fluctuations

**3. ML-based Detection**
- Train models on historical data to learn "normal" patterns
- Automatically flags deviations
- Better at catching subtle anomalies

### Alerting Best Practices
- **Don't alert on everything** — leads to alert fatigue
- **Set severity levels:** Critical (page someone) vs Warning (check tomorrow)
- **Include context** in alerts: what happened, which service, link to dashboard
- **Use escalation policies:** If not acknowledged in 10 min → escalate to next person