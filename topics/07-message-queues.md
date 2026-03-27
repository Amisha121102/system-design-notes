# 7. Message Queues

---

## What is a Message Queue?

A **middleman** that sits between a producer (sender) and consumer (receiver), allowing them to communicate **asynchronously**.

```
Producer → [Message Queue] → Consumer
(sends message)  (stores it)  (processes when ready)
```

Instead of calling a service directly and waiting, you drop a message in the queue and move on.

---

## Why Async Processing?

### Without queue (synchronous)
```
User places order → wait for payment → wait for email → wait for inventory update → respond
Total: 5 seconds (user waits for everything)
```

### With queue (asynchronous)
```
User places order → respond immediately
                  → Queue: [send email, update inventory, process payment]
                  → Consumers handle these in background
Total: 200ms response to user
```

### Benefits
- **Decoupling:** Producer doesn't need to know about consumers
- **Resilience:** If consumer is down, messages wait in the queue
- **Traffic spikes:** Queue absorbs bursts, consumers process at their own pace
- **Scalability:** Add more consumers to process faster

---

## Kafka

A distributed **event streaming platform**. Built for high throughput and durability.

### How it works
```
Producers → [Topic: "orders"] → Partition 0 → Consumer Group A
                               → Partition 1 → Consumer Group B
                               → Partition 2
```

### Key concepts
- **Topic:** A named channel for messages (e.g., "orders", "user-events")
- **Partition:** Topic is split into partitions for parallelism
- **Offset:** Each message has a unique position (offset) in its partition
- **Consumer Group:** A group of consumers that share the load. Each partition is read by only one consumer in the group.
- **Retention:** Messages are **kept on disk** for a configured duration (e.g., 7 days). Can be replayed.

### Characteristics
- **Extremely high throughput** (millions of messages/sec)
- Messages are **persisted to disk** (log-based)
- Messages can be **replayed** (consumers can go back to any offset)
- Ordered within a partition, not across partitions

### Use cases
- Event sourcing, activity tracking
- Log aggregation
- Real-time data pipelines
- Stream processing

---

## RabbitMQ

A traditional **message broker** focused on routing and delivery guarantees.

### How it works
```
Producer → [Exchange] → routing rules → [Queue 1] → Consumer A
                                       → [Queue 2] → Consumer B
```

### Key concepts
- **Exchange:** Receives messages and routes them to queues based on rules
- **Queue:** Stores messages until consumed
- **Binding:** Rule connecting exchange to queue
- **Acknowledgment:** Consumer confirms message was processed. If not acked, message is redelivered.

### Exchange types
| Type | Routing |
|------|---------|
| **Direct** | Routes by exact routing key match |
| **Fanout** | Broadcasts to all bound queues |
| **Topic** | Routes by pattern matching (e.g., `order.*`) |
| **Headers** | Routes by message headers |

### Characteristics
- Messages are **deleted after consumption**
- Supports complex routing logic
- Lower throughput than Kafka but better for task queues
- Built-in retry, dead-letter queues

### Use cases
- Task queues (background jobs)
- Request-reply patterns
- Complex message routing

---

## Kafka vs RabbitMQ

| Aspect | Kafka | RabbitMQ |
|--------|-------|----------|
| Model | Log-based (append-only) | Queue-based (delete after consume) |
| Throughput | Very high (millions/sec) | Moderate (thousands/sec) |
| Message replay | Yes (replay from any offset) | No (deleted after ack) |
| Ordering | Per partition | Per queue |
| Routing | Simple (topic + partition key) | Complex (exchanges, bindings) |
| Best for | Event streaming, logs, pipelines | Task queues, request-reply |

---

## Publisher-Subscriber (Pub-Sub) Model

A messaging pattern where **publishers** send messages to a **topic**, and all **subscribers** to that topic receive a copy.

```
Publisher → [Topic: "new-order"] → Subscriber 1 (Email Service)
                                 → Subscriber 2 (SMS Service)
                                 → Subscriber 3 (Analytics Service)
```

### How it differs from a regular queue

| Aspect | Queue | Pub-Sub |
|--------|-------|---------|
| Delivery | One consumer gets the message | All subscribers get a copy |
| Use case | Task distribution | Event broadcasting |
| Example | Process one payment | Notify email + SMS + analytics |

### Real-world example
**User signs up on an app:**
```
SignupService publishes → "user-registered" event

Subscribers:
  → Email Service: sends welcome email
  → Analytics Service: logs new user
  → Notification Service: sends push notification
  → CRM Service: creates customer profile
```

Each service works **independently** — adding a new subscriber doesn't require changing the publisher.
