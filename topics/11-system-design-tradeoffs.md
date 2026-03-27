# 11. System Design Tradeoffs

Every design decision involves tradeoffs. There's no perfect solution — only the **right fit for your use case**.

---

## Push vs Pull Architecture

### Push
Server **sends updates to the client** as soon as they happen.

```
Server: "New message!" → pushes to Client
```

- Real-time, low latency
- Complex (needs persistent connections)
- Examples: WebSockets, push notifications, Kafka consumers
- Use case: Chat apps, live scores, stock tickers

### Pull
Client **periodically asks** the server for updates.

```
Client: "Any new messages?" → Server: "No"
Client: "Any new messages?" → Server: "No"
Client: "Any new messages?" → Server: "Yes, here's one"
```

- Simple to implement
- Wastes resources if nothing changed (empty polls)
- Adds latency (delay between polls)
- Examples: HTTP polling, RSS feeds, email clients
- Use case: Email, analytics dashboards

### Hybrid (Long Polling)
Client sends request → server **holds it open** until there's new data → responds → client immediately sends new request.

```
Client: "Any updates?" → Server holds... waits... "Yes!" → Client: "Any updates?" → ...
```

### When to use what

| Scenario | Best approach |
|----------|--------------|
| Chat app | Push (WebSocket) |
| Email inbox | Pull (check every few minutes) |
| News feed | Pull or Long Polling |
| Live sports score | Push |
| Analytics dashboard | Pull (refresh every 30s) |

---

## Consistency vs Availability

### Consistency first
- Every read returns the **latest data**
- May reject requests during network issues
- Use case: **Banking** — wrong balance is unacceptable

### Availability first
- Every request gets a **response** (even if stale)
- May return outdated data during network issues
- Use case: **Social media** — showing a slightly old feed is fine

### Example
```
User transfers $100: Account A → Account B

Consistency first: Both accounts updated atomically. If network fails → transaction rejected.
Availability first: Account A deducted, Account B not yet updated (eventual sync). User sees inconsistency briefly.
```

### Rule of thumb
- **Money, inventory, bookings** → Consistency
- **Feeds, likes, views, recommendations** → Availability

---

## SQL vs NoSQL

| Factor | Choose SQL | Choose NoSQL |
|--------|-----------|-------------|
| Data structure | Well-defined, relational | Flexible, evolving schema |
| Queries | Complex JOINs, aggregations | Simple lookups, key-value |
| Scaling | Vertical (mostly) | Horizontal (built for it) |
| Consistency | Strong (ACID) | Eventual (BASE) |
| Examples | User accounts, transactions | Logs, catalogs, real-time analytics |

### Example
- **E-commerce orders** (relationships between users, products, payments) → SQL
- **Product catalog** (each product has different attributes) → NoSQL (document store)

---

## Memory vs Latency

**More memory (caching) = lower latency, but higher cost.**

```
Without cache: Client → API → Database (50ms)
With cache:    Client → API → Redis (2ms) → cache miss? → Database (50ms)
```

### Tradeoff
- Cache everything? Fast but **expensive** (RAM costs)
- Cache nothing? Cheap but **slow** (every request hits DB)
- **Sweet spot:** Cache hot/frequently accessed data, let cold data hit DB

### Example
- Netflix caches **trending movies** in CDN → instant playback
- Rarely watched movies → fetched from origin server (slightly slower)

---

## Throughput vs Latency

- **Latency:** Time taken for a **single request** (how fast)
- **Throughput:** Number of requests handled **per second** (how many)

### The tradeoff
**Batching** increases throughput but adds latency per individual request.

```
Without batching:
  Request 1 → process → respond (5ms each, 200 req/sec)

With batching:
  Collect 50 requests → process together → respond all (100ms total, 500 req/sec)
  Individual latency: 100ms (slower), but throughput: 2.5x higher
```

### Examples

| Scenario | Priority | Why |
|----------|----------|-----|
| User-facing API | Latency | Users expect fast responses |
| Log ingestion | Throughput | Process millions of logs, speed per log doesn't matter |
| Payment processing | Latency | User is waiting for confirmation |
| Data pipeline (ETL) | Throughput | Process bulk data efficiently |

---

## Accuracy vs Speed

Sometimes you trade **exact results** for **faster approximate results**.

### Examples

| Scenario | Accurate but slow | Fast but approximate |
|----------|-------------------|---------------------|
| Search results | Scan entire DB | Use inverted index + ranking |
| Trending topics | Count every tweet exactly | HyperLogLog (probabilistic count) |
| Recommendation | Analyze all user history | Use pre-computed embeddings |
| Analytics | Query raw data | Pre-aggregated rollups |
| Fraud detection | Deep analysis (minutes) | Quick rule-based check (ms) |

### Real-world
- **YouTube view count:** Shows approximate count in real-time, reconciles exact count later
- **Google Search:** Returns "About 1,230,000 results" — it's an estimate, not an exact count

---

## Summary Cheat Sheet

| Tradeoff | Option A | Option B | Choose A when | Choose B when |
|----------|----------|----------|---------------|---------------|
| Push vs Pull | Push (real-time) | Pull (polling) | Live updates needed | Periodic checks are fine |
| Consistency vs Availability | Consistency | Availability | Financial data | Social feeds |
| SQL vs NoSQL | SQL (structured) | NoSQL (flexible) | Complex relations | Scale + flexibility |
| Memory vs Latency | More cache | Less cache | Speed is critical | Cost is critical |
| Throughput vs Latency | High throughput | Low latency | Bulk processing | User-facing requests |
| Accuracy vs Speed | Exact results | Approximate | Reports, billing | Real-time dashboards |