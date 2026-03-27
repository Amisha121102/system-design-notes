# HLD Topics — Quick Summary

---

## 1. Fundamentals

- **Serverless vs Serverful:** Serverful = you manage servers 24/7 (EC2, VMs). Serverless = cloud runs your code on demand, you pay per execution (Lambda, Azure Functions).
- **Horizontal Scaling:** Add more machines to handle load (e.g., 3 servers → 10 servers). **Vertical Scaling:** Make one machine more powerful (e.g., 8GB RAM → 32GB RAM).
- **Threads:** Smallest unit of execution within a process. Multiple threads share memory and run tasks concurrently. **Pages:** Fixed-size blocks of memory used by the OS for virtual memory management.
- **How Internet Works:** Client sends a request → DNS resolves domain to IP → request travels via TCP/IP through routers → reaches the server → server responds back the same way.

---

## 2. Databases

- **SQL vs NoSQL:** SQL = structured, table-based, ACID compliant (MySQL, PostgreSQL). NoSQL = flexible schema, designed for scale (MongoDB, DynamoDB).
- **In-Memory DBs:** Store data in RAM instead of disk for ultra-fast reads/writes. Examples: Redis, Memcached. Tradeoff: data can be lost on crash unless persisted.
- **Data Replication:** Copy data across multiple nodes for availability and fault tolerance.
- **Data Migration:** Moving data between databases, formats, or systems.
- **Data Partitioning:** Splitting a large dataset into smaller chunks stored across nodes.
- **Sharding:** A type of partitioning where each shard is on a separate database instance. Distributes load but adds complexity (cross-shard queries, rebalancing).

---

## 3. Consistency vs Availability

- **Data Consistency:** All nodes see the same data at the same time. **Levels:** Strong → Sequential → Causal → Eventual.
- **Isolation Levels:** Control how concurrent transactions interact. Levels: Read Uncommitted → Read Committed → Repeatable Read → Serializable.
- **CAP Theorem:** A distributed system can guarantee only 2 of 3: **Consistency** (all nodes see same data), **Availability** (every request gets a response), **Partition Tolerance** (system works despite network splits). In practice, P is unavoidable, so you choose between CP or AP.

---

## 4. Cache

- **Redis:** In-memory key-value store, supports data structures (lists, sets, sorted sets). Used for caching, sessions, leaderboards.
- **Memcached:** Simple in-memory key-value cache. Faster for basic caching but fewer features than Redis.
- **LRU (Least Recently Used):** Evicts the item that hasn't been accessed for the longest time.
- **LFU (Least Frequently Used):** Evicts the item with the fewest accesses.
- **Segmented LRU:** Divides cache into segments (e.g., probation + protected). Items must "prove" their worth before getting promoted. Avoids one-time reads polluting the cache.
- **CDN (Content Delivery Network):** Distributed network of edge servers that cache static content (images, videos, CSS) close to users. Reduces latency and server load. Examples: Cloudflare, Akamai.

---

## 5. Networking

- **TCP vs UDP:** TCP = reliable, ordered delivery with handshake (HTTP, email). UDP = fast, no guarantee of delivery (video streaming, gaming).
- **HTTP vs HTTPS:** HTTP = plain text communication. HTTPS = encrypted via TLS/SSL. Always use HTTPS for security.
- **WebSockets:** Persistent, full-duplex connection between client and server. Used for chat apps, live updates, real-time dashboards.
- **WebRTC:** Peer-to-peer real-time communication for audio/video/data. No server needed for media transfer. Used by Google Meet, Discord.
- **Video Streaming:** Uses adaptive bitrate streaming (HLS, DASH). Video is split into chunks at different qualities. Client picks the best quality based on bandwidth.

---

## 6. Load Balancer

- **Stateless Algorithms:** Round Robin, Weighted Round Robin, Random, Least Connections — don't track previous requests.
- **Stateful Algorithms:** Sticky Sessions — route same user to same server using cookies/IP.
- **Consistent Hashing:** Distributes requests across servers using a hash ring. Adding/removing servers only affects nearby keys, not all. Used in distributed caches and databases.
- **Proxy:** Sits between client and internet, hides the client (e.g., VPN).
- **Reverse Proxy:** Sits between internet and servers, hides the servers (e.g., Nginx, HAProxy). Handles SSL, caching, load balancing.
- **Rate Limiting:** Restricts number of requests a user can make in a time window. Prevents abuse and DDoS. Algorithms: Token Bucket, Leaky Bucket, Fixed/Sliding Window.

---

## 7. Message Queues

- **Async Processing:** Decouple producers from consumers. Producer sends a message to a queue, consumer processes it later. Improves reliability and handles traffic spikes.
- **Kafka:** Distributed event streaming platform. High throughput, persistent log-based storage. Great for event sourcing, logs, real-time pipelines.
- **RabbitMQ:** Traditional message broker. Supports complex routing, acknowledgments. Better for task queues and request-reply patterns.
- **Pub-Sub Model:** Publishers send messages to a topic, all subscribers to that topic receive the message. Decouples senders from receivers. Example: Notifications — one event triggers email + push + SMS.

---

## 8. Monoliths vs Microservices

- **Monolith:** Single deployable unit. Simple to develop and deploy initially but hard to scale and maintain as it grows.
- **Why Microservices:** Independent deployment, independent scaling, tech flexibility, smaller teams own smaller services.
- **Single Point of Failure (SPOF):** A component whose failure takes down the entire system. Fix: redundancy, replication, failover.
- **Cascading Failures:** One service fails → overloads dependent services → chain reaction. Fix: circuit breakers, timeouts, bulkheads, retries with backoff.
- **Containerization (Docker):** Package app + dependencies into a container. Runs the same everywhere. Lightweight alternative to VMs.
- **Migrating to Microservices:** Strangler Fig pattern — gradually replace parts of the monolith with microservices instead of a full rewrite.

---

## 9. Monitoring & Logging

- **Logging:** Record events (errors, requests, state changes) for debugging. Tools: ELK Stack (Elasticsearch, Logstash, Kibana), Splunk.
- **Monitoring Metrics:** Track system health — CPU, memory, latency, error rates, throughput. Tools: Prometheus, Grafana, Datadog.
- **Anomaly Detection:** Automatically detect unusual patterns (traffic spikes, error surges). Uses statistical thresholds or ML models to trigger alerts.

---

## 10. Security

- **Tokens for Auth:** JWT (JSON Web Token) — stateless token containing user info + signature. Server doesn't need to store sessions.
- **SSO (Single Sign-On):** Log in once, access multiple apps (e.g., Google account → Gmail, YouTube, Drive).
- **OAuth:** Authorization framework. Lets third-party apps access user data without sharing passwords (e.g., "Login with Google").
- **Access Control Lists (ACL):** Define who can access what resource with what permission (read, write, delete).
- **Rule Engines:** Evaluate business rules dynamically (e.g., "users with role=admin can delete posts"). Decouples rules from code.
- **Encryption:** At rest (stored data) and in transit (TLS/SSL). Symmetric (AES — same key) vs Asymmetric (RSA — public/private key pair).

---

## 11. System Design Tradeoffs

- **Push vs Pull:** Push = server sends updates to client (WebSocket, notifications). Pull = client polls server periodically. Push is real-time but complex; Pull is simple but adds latency.
- **Consistency vs Availability:** Strong consistency means slower responses. High availability means data might be stale. Choose based on use case (banking = consistency, social media = availability).
- **SQL vs NoSQL:** SQL for structured data, complex queries, ACID. NoSQL for flexible schemas, horizontal scaling, high write throughput.
- **Memory vs Latency:** More caching = lower latency but higher memory cost.
- **Throughput vs Latency:** Batching increases throughput but adds latency per individual request.
- **Accuracy vs Speed:** Real-time approximate results (e.g., trending topics) vs batch-processed exact results (e.g., analytics reports).
