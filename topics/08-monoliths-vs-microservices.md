# 8. Monoliths vs Microservices

---

## Monolith

A **single deployable unit** where all features live in one codebase and run as one process.

```
[Single Application]
├── User Module
├── Order Module
├── Payment Module
├── Notification Module
└── All share one database
```

### Pros
- Simple to develop, test, and deploy initially
- Easy debugging — everything is in one place
- No network latency between modules (in-process calls)

### Cons
- **Hard to scale** — must scale the entire app even if only one module needs it
- **Slow deployments** — small change requires redeploying everything
- **Tight coupling** — one bug can crash the entire system
- **Tech lock-in** — entire app must use the same language/framework

---

## Why Microservices?

Break the application into **small, independent services**, each responsible for one business capability.

```
[API Gateway]
├── User Service        (own DB)
├── Order Service       (own DB)
├── Payment Service     (own DB)
├── Notification Service (own DB)
```

### Benefits
- **Independent deployment** — deploy only what changed
- **Independent scaling** — scale only the service under load
- **Tech flexibility** — each service can use different language/DB
- **Smaller teams** — each team owns a service end-to-end
- **Fault isolation** — one service failing doesn't crash everything

### Tradeoffs
- More complex infrastructure (networking, monitoring, deployment)
- Distributed system challenges (latency, data consistency)
- Harder debugging (tracing across services)

---

## Single Point of Failure (SPOF)

A component whose failure **takes down the entire system**.

### Examples
```
❌ Single database → DB goes down → entire app is down
❌ Single load balancer → LB crashes → no traffic reaches servers
❌ Single auth service → auth dies → nobody can log in
```

### How to fix
- **Redundancy:** Run multiple instances of every critical component
- **Replication:** Database replicas (master-slave)
- **Failover:** Automatic switch to backup when primary fails
- **Multi-region:** Deploy across regions so one datacenter outage doesn't kill you

```
✅ [Primary DB] ←replication→ [Replica DB]
   If primary dies → replica promoted to primary automatically
```

---

## Avoiding Cascading Failures

One service fails → overloads dependent services → **chain reaction** brings everything down.

```
Order Service fails → Payment Service keeps retrying → Payment overloaded
→ Notification Service waiting on Payment → also overloaded → everything down
```

### Solutions

**1. Circuit Breaker**
- Track failures to a service. If failures exceed a threshold, **stop calling** it temporarily.
```
Closed (normal) → failures pile up → Open (reject calls, return fallback)
→ after timeout → Half-Open (try one call) → success? Close : stay Open
```

**2. Timeouts**
- Never wait forever. Set a timeout for every external call.
- If service doesn't respond in 2 seconds → fail fast, don't block.

**3. Bulkhead Pattern**
- Isolate resources per service. One slow service shouldn't consume all threads.
```
Thread Pool A (50 threads) → Order Service
Thread Pool B (50 threads) → Payment Service
Payment is slow? Only Pool B is affected.
```

**4. Retries with Exponential Backoff**
- Retry failed requests but **wait longer** between each retry.
```
Retry 1: wait 1s → Retry 2: wait 2s → Retry 3: wait 4s → give up
```
- Prevents flooding a struggling service with retries.

---

## Containerization (Docker)

Package an application + all its dependencies into a **container** that runs the same everywhere.

### Problem Docker solves
```
"It works on my machine!" → but breaks in production
```

### How it works
```
Your Code + Dependencies + Runtime → Docker Image → Docker Container (running instance)
```

- **Image:** A blueprint (like a class)
- **Container:** A running instance (like an object)
- **Dockerfile:** Instructions to build an image

### Example Dockerfile
```dockerfile
FROM node:18
WORKDIR /app
COPY package.json .
RUN npm install
COPY . .
CMD ["node", "server.js"]
```

### Docker vs VM

| Aspect | Docker Container | Virtual Machine |
|--------|-----------------|-----------------|
| Size | MBs | GBs |
| Startup | Seconds | Minutes |
| OS | Shares host OS kernel | Full guest OS |
| Isolation | Process-level | Hardware-level |
| Performance | Near-native | Overhead from hypervisor |

### Orchestration
- **Docker Compose:** Run multi-container apps locally
- **Kubernetes (K8s):** Orchestrate containers at scale — auto-scaling, self-healing, rolling deployments

---

## Migrating to Microservices

### Strangler Fig Pattern (recommended)
Gradually **replace parts** of the monolith with microservices, instead of a risky full rewrite.

```
Phase 1: [Monolith handles everything]

Phase 2: [Monolith] ← still handles most things
         [User Service] ← extracted out

Phase 3: [Monolith] ← shrinking
         [User Service]
         [Order Service]
         [Payment Service]

Phase 4: [Monolith is gone]
         All services are independent
```

### Steps
1. **Identify boundaries** — find loosely coupled modules in the monolith
2. **Extract one service** at a time — start with the least risky one
3. **Route traffic** — use API gateway to route between monolith and new service
4. **Repeat** until monolith is fully decomposed

### Anti-patterns to avoid
- **Big bang rewrite** — rewriting everything at once is risky and slow
- **Distributed monolith** — microservices that are tightly coupled and must deploy together (worst of both worlds)
- **Shared database** — services should own their data, not share one DB