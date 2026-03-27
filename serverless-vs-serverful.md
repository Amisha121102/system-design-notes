# Serverless vs Serverful

## Serverful (Traditional)

You **rent and manage servers yourself**. They run 24/7, whether traffic comes or not.

- **You handle:** Server setup, scaling, patching, monitoring
- **Billing:** Pay for the server even when idle
- **Examples:** AWS EC2, Azure VMs, self-hosted Kubernetes

### Example

You deploy an API on an EC2 instance. The server runs all day. At 3 AM with zero users, you're still paying.

```
User → Load Balancer → [Server 1 - always running]
                      → [Server 2 - always running]
```

---

## Serverless

The **cloud provider manages everything**. You just upload your code. It runs on demand and shuts down when not needed.

- **Provider handles:** Scaling, availability, infrastructure
- **Billing:** Pay only when your code runs (per request/execution time)
- **Examples:** AWS Lambda, Azure Functions, Google Cloud Functions

### Example

You write a `getProducts()` function and deploy it to AWS Lambda. When a user calls the API, Lambda spins up, runs your function, responds, and shuts down. At 3 AM — nothing runs, $0 cost.

```
User → API Gateway → Lambda(getProducts) → Response → Lambda shuts down
```

---

## Quick Comparison

| Aspect        | Serverful                    | Serverless                     |
|---------------|------------------------------|--------------------------------|
| Scaling       | You configure it             | Automatic                      |
| Idle cost     | You pay even when idle       | No cost when idle              |
| Cold starts   | None (always running)        | Possible slight delay          |
| Time limits   | No limit                     | Has execution limits (e.g. 15 min) |
| State         | Stateful (disk, memory)      | Stateless (ephemeral)          |
| Control       | Full (OS, runtime)           | Limited (provider's sandbox)   |

---

## Real-World Analogy

- **Serverful** = Owning a restaurant — you pay rent and salaries 24/7, even during slow hours
- **Serverless** = Cloud kitchen — kitchen activates only when an order comes in, you pay per order

---

## Practical Scenario

**Task:** User uploads a profile picture → generate a thumbnail

| Approach    | How it works                                                        |
|-------------|---------------------------------------------------------------------|
| Serverful   | A server runs 24/7 waiting for uploads. Idle 99% of the time.      |
| Serverless  | Upload triggers a Lambda → resizes image → saves thumbnail → done. Zero cost when no uploads. |

---

## When to Use What

- **Serverless** → APIs, event-driven tasks, variable/spiky traffic, background jobs
- **Serverful** → Long-running processes, WebSockets, game servers, GPU workloads
