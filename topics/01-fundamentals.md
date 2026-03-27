# 1. Fundamentals

---

## Serverless vs Serverful

### Serverful (Traditional)
You **rent and manage servers** yourself. They run 24/7 whether traffic comes or not.

- You handle: setup, scaling, patching, monitoring
- Pay for the server **even when idle**
- Examples: AWS EC2, Azure VMs, self-hosted Kubernetes

**Example:** You deploy an API on an EC2 instance. At 3 AM with zero users — server still running, still costing money.

### Serverless
Cloud provider manages everything. You just **upload your code**. It runs on demand.

- Provider handles: scaling, availability, infrastructure
- Pay **only when code runs**
- Examples: AWS Lambda, Azure Functions, Google Cloud Functions

**Example:** A `getProducts()` function on Lambda. User calls API → Lambda runs → responds → shuts down. No traffic = $0.

### Comparison

| Aspect       | Serverful              | Serverless                |
|--------------|------------------------|---------------------------|
| Scaling      | You configure it       | Automatic                 |
| Idle cost    | You pay                | No cost                   |
| Cold starts  | None                   | Possible delay            |
| Time limits  | No limit               | 15 min (Lambda)           |
| State        | Stateful               | Stateless                 |
| Control      | Full (OS, runtime)     | Limited (sandbox)         |

**Analogy:** Serverful = owning a restaurant (pay rent 24/7). Serverless = cloud kitchen (pay per order).

---

## Horizontal vs Vertical Scaling

### Vertical Scaling (Scale Up)
Make a **single machine more powerful** — add more CPU, RAM, storage.

```
[4GB RAM, 2 CPU] → [32GB RAM, 16 CPU]
```

- Simple, no code changes needed
- Has a hardware limit (can't scale infinitely)
- Single point of failure
- Example: Upgrading your database server

### Horizontal Scaling (Scale Out)
Add **more machines** to distribute the load.

```
[Server 1] → [Server 1] + [Server 2] + [Server 3]
```

- No hardware limit, scales infinitely
- Needs load balancer, distributed logic
- More complex but more resilient
- Example: Adding more web servers behind a load balancer

### Comparison

| Aspect          | Vertical              | Horizontal              |
|-----------------|-----------------------|-------------------------|
| Cost            | Expensive (bigger HW) | Cheaper (commodity HW)  |
| Complexity      | Simple                | Complex                 |
| Limit           | Hardware ceiling      | Virtually unlimited     |
| Downtime        | Often needs restart   | No downtime (add nodes) |
| Fault tolerance | SPOF                  | Resilient               |

**Real-world:** Most systems use **both** — vertical for databases (hard to distribute), horizontal for stateless services (web servers, APIs).

---

## Threads and Pages

### Threads
A **thread** is the smallest unit of execution within a process.

- A process can have **multiple threads** sharing the same memory
- Threads run tasks **concurrently** (or in parallel on multi-core CPUs)
- Lighter than processes — faster to create and switch

**Example:**
```
Web Server Process
├── Thread 1 → handles Request A
├── Thread 2 → handles Request B
└── Thread 3 → handles Request C
```

**Key concepts:**
- **Multithreading:** Running multiple threads in one process
- **Race condition:** Two threads modify shared data at the same time → unpredictable results
- **Deadlock:** Two threads wait for each other forever
- **Thread pool:** Pre-created set of threads to avoid creation overhead

### Pages (Memory Pages)
A **page** is a fixed-size block of memory (typically 4KB) used by the OS for virtual memory management.

- OS maps **virtual memory → physical memory** using pages
- **Page Table:** Mapping between virtual and physical pages
- **Page Fault:** When a requested page is not in RAM → OS loads it from disk (slow)
- **Thrashing:** Excessive page faults causing the system to spend more time swapping than working

**Why it matters:** Understanding pages helps in optimizing memory-intensive applications and database buffer pools.

---

## How Does the Internet Work

### Step-by-step flow

```
You type "google.com" in browser
        ↓
1. Browser checks cache for IP
        ↓
2. DNS Lookup → resolves "google.com" to 142.250.190.78
        ↓
3. TCP Handshake (SYN → SYN-ACK → ACK) establishes connection
        ↓
4. TLS Handshake (if HTTPS) for encryption
        ↓
5. Browser sends HTTP GET request
        ↓
6. Request travels through routers via IP routing
        ↓
7. Reaches Google's server (likely via load balancer)
        ↓
8. Server processes request, sends back HTML
        ↓
9. Browser renders the page
```

### Key components
- **DNS:** Translates domain names to IP addresses (like a phonebook)
- **IP Address:** Unique address of a device on the internet
- **TCP/IP:** Protocol stack for reliable data transmission
- **HTTP/HTTPS:** Protocol for web communication
- **Router:** Forwards packets between networks
- **ISP:** Internet Service Provider — connects you to the internet backbone
