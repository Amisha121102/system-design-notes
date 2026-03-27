# 4. Cache

---

## What is Caching?

Storing frequently accessed data in a **fast storage layer** (RAM) to avoid hitting the slow source (database/disk) repeatedly.

```
Client → Cache (fast, RAM) → hit? return data
                            → miss? fetch from DB → store in cache → return
```

---

## Redis

In-memory **key-value store** with support for rich data structures.

- Supports: Strings, Lists, Sets, Sorted Sets, Hashes, Streams
- Supports **persistence** (RDB snapshots, AOF logs) — data survives restarts
- Single-threaded but extremely fast (~100K ops/sec)
- Use cases: Caching, sessions, leaderboards, rate limiting, pub-sub

**Example:** Cache a DB query result
```
SET user:123 '{"name":"Amisha","role":"dev"}' EX 3600   // expires in 1 hour
GET user:123  // returns cached data instead of hitting DB
```

---

## Memcached

Simple in-memory **key-value cache**.

- Only supports strings (no data structures)
- **Multi-threaded** — better for simple caching at high concurrency
- No persistence — data lost on restart
- Use cases: Simple DB query caching, session storage

### Redis vs Memcached

| Aspect | Redis | Memcached |
|--------|-------|-----------|
| Data types | Rich (lists, sets, hashes) | Strings only |
| Persistence | Yes | No |
| Threading | Single-threaded | Multi-threaded |
| Features | Pub-sub, Lua scripts, transactions | Simple get/set |
| Best for | Complex use cases | Simple, high-throughput caching |

---

## Cache Eviction Policies

When cache is full, which item to remove?

### LRU (Least Recently Used)
- Evicts the item that **hasn't been accessed for the longest time**
- Most commonly used policy
- Example: Items A, B, C in cache. A accessed last 1 hour ago → A gets evicted

### LFU (Least Frequently Used)
- Evicts the item with the **fewest total accesses**
- Better for identifying truly popular items
- Example: A accessed 2 times, B accessed 100 times → A gets evicted

### Segmented LRU (SLRU)
- Divides cache into **two segments**: Probation + Protected
- New items enter **probation**. If accessed again, promoted to **protected**.
- Eviction happens from probation first
- Prevents one-time reads from polluting the cache

```
New item → [Probation Segment] → accessed again → [Protected Segment]
                ↓ evicted first                      ↓ evicted last
```

---

## CDN (Content Delivery Network)

A distributed network of **edge servers** that cache static content **close to users**.

```
User in India → CDN Edge (Mumbai) → cached? return instantly
                                   → not cached? fetch from Origin Server (US) → cache → return
```

### What CDN caches
- Images, videos, CSS, JavaScript
- Static HTML pages
- API responses (sometimes)

### Benefits
- **Lower latency** — content served from nearby server
- **Reduced server load** — origin handles fewer requests
- **Better availability** — if origin is down, CDN serves cached content

### Examples
- Cloudflare, Akamai, AWS CloudFront, Fastly

### Real-world example
Netflix stores movies on CDN edge servers worldwide. When you stream, the video comes from a server near you, not from Netflix's main data center.