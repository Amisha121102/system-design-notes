# System Design Guide

---

## HLD (High-Level Design)

### 1. Fundamentals
- Serverless and Serverful
- Horizontal and Vertical Scaling
- What are Threads and Pages
- How does the Internet work

### 2. Databases
- SQL vs NoSQL databases
- In-Memory DBs
- Data Replication, Migration, Data Partitioning, Sharding

### 3. Consistency vs Availability
- Data Consistency and its levels
- Isolation and its levels
- CAP Theorem

### 4. Cache
- Redis, Memcached
- LFU, LRU, Segmented LRU etc.
- Content Delivery Network (CDN)

### 5. Networking
- TCP vs UDP
- HTTP vs HTTPS
- WebSockets
- WebRTC and Video Streaming

### 6. Load Balancer
- Algorithms (Stateless & Stateful)
- Consistent Hashing
- Proxy & Reverse Proxy
- Rate Limiting

### 7. Message Queues
- Async Processing (Kafka, RabbitMQ)
- Publisher-Subscriber Model

### 8. Monoliths vs Microservices
- Why Microservices
- Concept of "Single Point of Failure"
- Avoiding Cascading Failures
- Containerization (Docker)
- Migrating to Microservices

### 9. Monitoring & Logging
- Logging Events & Monitoring Metrics
- Anomaly Detection

### 10. Security
- Tokens for Auth
- SSO & OAuth
- Access Control Lists & Rule Engines
- Encryption

### 11. System Design Tradeoffs
- Push vs Pull Architecture
- Consistency vs Availability
- SQL vs NoSQL
- Memory vs Latency
- Throughput vs Latency
- Memory vs Latency
- Accuracy vs latency vs throughput

### 12. System Design Case Studies
- YouTube
- Twitter
- WhatsApp
- Uber
- Amazon
- Dropbox
- Netflix
- Instagram
- Zoom
- Booking.com / Airbnb

---

## LLD (Low-Level Design)

### 1. OOPS & SOLID Principles

### 2. Design Patterns
- **Creational** — Singleton, Factory etc.
- **Structural** — Proxy, Bridge etc.
- **Behavioral** — Strategy, Command, Observer etc.

### 3. Concurrency & Thread Safety
- Thread-safe Injection
- Locking Mechanisms
- Producer-Consumer
- Race Conditions and Synchronization

### 4. UML Diagrams

### 5. APIs
- API Design
- Request-Response Object Modelling
- Versioning and Extensibility
- Clean Code Principles: DRY, SRP etc.
- Avoiding God Classes

### 6. Common LLD Problems
- Design a Tic-Tac-Toe or Chess Game
- Splitwise App
- Design a Parking Lot
- Elevator System with Multiple Lifts
- Notification System
- Food Delivery App
- Movie Ticket Booking System
- URL Shortener
- Design a Logging Framework
- Design a Rate Limiter