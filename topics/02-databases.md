# 2. Databases

---

## SQL vs NoSQL

### SQL (Relational)
Structured, table-based databases with fixed schemas and relationships.

- Uses **rows and columns** with predefined schema
- Supports **JOIN** operations across tables
- **ACID** compliant (Atomicity, Consistency, Isolation, Durability)
- Examples: MySQL, PostgreSQL, Oracle, SQL Server

**Best for:** Banking, e-commerce, any app needing complex queries and data integrity.

### NoSQL (Non-Relational)
Flexible schema databases designed for scale and speed.

**Types:**
| Type | Structure | Example | Use Case |
|------|-----------|---------|----------|
| Document | JSON-like docs | MongoDB, CouchDB | User profiles, catalogs |
| Key-Value | Simple key→value | Redis, DynamoDB | Caching, sessions |
| Column-family | Column-oriented | Cassandra, HBase | Analytics, time-series |
| Graph | Nodes + edges | Neo4j | Social networks, recommendations |

**Best for:** Large-scale apps, flexible data, high write throughput.

### Comparison

| Aspect | SQL | NoSQL |
|--------|-----|-------|
| Schema | Fixed (predefined) | Flexible (dynamic) |
| Scaling | Vertical (mostly) | Horizontal (designed for it) |
| Queries | Complex JOINs, aggregations | Simple lookups, limited JOINs |
| Consistency | Strong (ACID) | Eventual (BASE) |
| Speed | Slower writes at scale | Fast writes at scale |

---

## In-Memory Databases

Store data **in RAM** instead of disk. Extremely fast reads/writes.

- **Redis:** Key-value store + data structures (lists, sets, sorted sets, hashes). Supports persistence (RDB snapshots, AOF logs).
- **Memcached:** Pure key-value cache. Simpler, multi-threaded. No persistence.

### When to use
- Caching (DB query results, API responses)
- Session storage
- Leaderboards, counters
- Real-time analytics

### Tradeoff
- **Fast** but **expensive** (RAM costs more than disk)
- Data can be **lost on crash** unless persistence is configured

---

## Data Replication

Copying data across multiple nodes for **availability** and **fault tolerance**.

### Strategies

**1. Master-Slave (Primary-Replica)**
```
Writes → [Master] → replicates to → [Slave 1]
                                   → [Slave 2]
Reads  → [Slave 1] or [Slave 2]
```
- Master handles writes, slaves handle reads
- If master dies → promote a slave

**2. Master-Master (Multi-Master)**
```
[Master 1] ⟷ [Master 2]
(both accept reads + writes)
```
- Both accept writes → conflict resolution needed
- Better availability but more complex

### Sync vs Async Replication
- **Synchronous:** Write confirmed only after all replicas updated. Slower but consistent.
- **Asynchronous:** Write confirmed immediately, replicas update later. Faster but risk of data loss.

---

## Data Migration

Moving data between databases, formats, or systems.

### Common scenarios
- Moving from SQL to NoSQL (or vice versa)
- Upgrading database version
- Migrating from on-premise to cloud

### Best practices
- Migrate in **phases**, not all at once
- Run **old and new systems in parallel** during transition
- Validate data integrity after migration
- Have a **rollback plan**

---

## Data Partitioning

Splitting a large dataset into smaller **partitions** for performance and manageability.

### Types

**1. Horizontal Partitioning (Row-based)**
Split rows across partitions.
```
Users table:
  Partition 1: users with ID 1-1M
  Partition 2: users with ID 1M-2M
```

**2. Vertical Partitioning (Column-based)**
Split columns across partitions.
```
Users table:
  Partition 1: id, name, email (frequently accessed)
  Partition 2: id, bio, avatar (rarely accessed)
```

### Partitioning strategies
- **Range-based:** By value range (date, ID)
- **Hash-based:** Hash the key to determine partition
- **List-based:** By specific values (country, category)

---

## Sharding

A type of **horizontal partitioning** where each partition (shard) lives on a **separate database instance**.

```
[App Server] → Shard Key (user_id % 3)
    → Shard 0: users 0, 3, 6, 9...  (DB Instance 1)
    → Shard 1: users 1, 4, 7, 10... (DB Instance 2)
    → Shard 2: users 2, 5, 8, 11... (DB Instance 3)
```

### Challenges
- **Cross-shard queries:** JOINs across shards are expensive
- **Hotspots:** Uneven data distribution → one shard gets overloaded
- **Rebalancing:** Adding/removing shards requires data redistribution
- **Shard key selection:** Choosing the wrong key leads to unbalanced shards

### When to shard
- Single database can't handle the load
- Data is too large for one machine
- Need geographic distribution