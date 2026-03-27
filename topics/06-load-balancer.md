# 6. Load Balancer

A load balancer **distributes incoming traffic** across multiple servers to ensure no single server gets overwhelmed.

```
Client requests → [Load Balancer] → Server 1
                                  → Server 2
                                  → Server 3
```

---

## Stateless Algorithms

Don't track previous requests. Each request is routed independently.

### Round Robin
- Sends requests to servers **one by one in order**
- Simple but doesn't consider server load
```
Request 1 → Server A
Request 2 → Server B
Request 3 → Server C
Request 4 → Server A  (cycles back)
```

### Weighted Round Robin
- Same as Round Robin but servers with **higher capacity get more requests**
```
Server A (weight 3): gets 3 out of every 5 requests
Server B (weight 2): gets 2 out of every 5 requests
```

### Random
- Picks a server **randomly** for each request
- Simple, works well with large number of servers

### Least Connections
- Sends to the server with the **fewest active connections**
- Better for requests with varying processing times
```
Server A: 5 active connections
Server B: 2 active connections  ← next request goes here
Server C: 8 active connections
```

---

## Stateful Algorithms

Track previous requests to ensure **same user goes to same server**.

### Sticky Sessions (Session Affinity)
- Routes a user to the **same server** for the entire session
- Uses cookies or IP address to identify users
```
User A (cookie=srv1) → always goes to Server 1
User B (cookie=srv2) → always goes to Server 2
```

- **Why needed:** If session data (cart, login state) is stored on a specific server
- **Problem:** If that server goes down, user loses their session
- **Better approach:** Store sessions externally (Redis) and use stateless algorithms

---

## Consistent Hashing

Distributes requests across servers using a **hash ring**. Adding/removing servers only affects **nearby keys**, not all.

### How it works
```
Hash Ring (0 to 360°):

        Server A (90°)
       /
  0°--+--------+--- Server B (180°)
                |
                +--- Server C (270°)

Request hash = 120° → goes to next server clockwise → Server B
Request hash = 300° → goes to Server A (wraps around)
```

### Why it's better than simple hashing
- **Simple hash:** `server = hash(key) % N` — if N changes, **all keys** get remapped
- **Consistent hash:** Only **K/N keys** get remapped (K = total keys, N = total servers)

### Virtual nodes
- Each server gets **multiple positions** on the ring for even distribution
- Prevents hotspots when servers are unevenly spaced

**Used in:** DynamoDB, Cassandra, CDN routing, distributed caches

---

## Proxy vs Reverse Proxy

### Forward Proxy
Sits between **client and internet**. Hides the client.

```
Client → [Forward Proxy] → Internet → Server
Server sees proxy's IP, not client's IP
```

- Use cases: VPN, bypass restrictions, anonymity, content filtering
- Example: Corporate proxy that blocks social media

### Reverse Proxy
Sits between **internet and servers**. Hides the servers.

```
Client → Internet → [Reverse Proxy] → Server 1
                                     → Server 2
Client doesn't know which server handled the request
```

- Use cases: Load balancing, SSL termination, caching, security
- Examples: Nginx, HAProxy, AWS ALB

### Comparison

| Aspect | Forward Proxy | Reverse Proxy |
|--------|--------------|---------------|
| Protects | Client | Server |
| Hides | Client identity | Server identity |
| Placed at | Client side | Server side |
| Example | VPN, corporate proxy | Nginx, CDN |

---

## Rate Limiting

Restricts the **number of requests** a user can make in a time window. Prevents abuse, DDoS, and resource exhaustion.

### Algorithms

**1. Token Bucket**
- Bucket holds tokens, refilled at a fixed rate
- Each request consumes one token. No tokens = request rejected.
- Allows **bursts** (bucket can be full)
```
Bucket capacity: 10 tokens, refill: 1 token/sec
10 requests at once → all pass (bucket empties)
11th request → rejected (wait for refill)
```

**2. Leaky Bucket**
- Requests enter a queue (bucket), processed at a **fixed rate**
- If bucket is full, new requests are dropped
- Smooths out bursts into a steady flow
```
Requests come in bursts → queue fills → processed one by one at fixed rate
```

**3. Fixed Window**
- Count requests in a **fixed time window** (e.g., 100 requests per minute)
- Resets at window boundary
- Problem: Burst at window edges (99 requests at 0:59 + 99 at 1:00 = 198 in 2 seconds)

**4. Sliding Window**
- Counts requests in a **rolling time window**
- Fixes the fixed window boundary problem
- More accurate but slightly more complex

### Where to apply
- API gateways
- Per user / per IP / per endpoint
- Example: GitHub API allows 5000 requests/hour per authenticated user
