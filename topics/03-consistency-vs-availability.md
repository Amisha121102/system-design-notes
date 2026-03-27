# 3. Consistency vs Availability

---

## Data Consistency

All nodes in a distributed system **see the same data at the same time**.

### Consistency Levels (Strong → Weak)

**1. Strong Consistency**
- Every read returns the **most recent write**
- Slowest but safest
- Example: Banking — balance must always be accurate

**2. Sequential Consistency**
- All nodes see operations in the **same order**, not necessarily real-time order

**3. Causal Consistency**
- Causally related operations are seen in order. Unrelated ones can be in any order.
- Example: A reply always appears after the original comment

**4. Eventual Consistency**
- All nodes will **eventually** converge, but reads might return stale data temporarily
- Example: Instagram likes — you see 99, someone else sees 100, but it converges

---

## Isolation Levels

Control how **concurrent transactions** interact.

| Level | Dirty Read | Non-Repeatable Read | Phantom Read |
|-------|-----------|-------------------|--------------|
| Read Uncommitted | Yes | Yes | Yes |
| Read Committed | No | Yes | Yes |
| Repeatable Read | No | No | Yes |
| Serializable | No | No | No |

- **Dirty Read:** Reading uncommitted data that may get rolled back
- **Non-Repeatable Read:** Same query returns different results within one transaction
- **Phantom Read:** New rows appear between two reads in the same transaction

---

## CAP Theorem

In a distributed system, you can only guarantee **2 out of 3**:

- **C (Consistency):** All nodes see the same data
- **A (Availability):** Every request gets a response
- **P (Partition Tolerance):** System works even if network splits

Since network partitions **will** happen, P is mandatory. You choose between:

| Type | Example | Use Case |
|------|---------|----------|
| **CP** (Consistency + Partition) | MongoDB, HBase | Banking — reject requests rather than risk wrong data |
| **AP** (Availability + Partition) | Cassandra, DynamoDB | Social media — show stale feed rather than nothing |
