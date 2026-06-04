# System Design Basics

> A beginner-friendly primer on system design. It explains the core building blocks, gives you a repeatable framework for the interview, and walks through two worked mini-examples.
>
> You don't need to memorize everything — understand the **trade-offs** and be able to reason out loud.

---

## What Is System Design?

System design is the process of defining the **architecture, components, and data flow** of a software system to meet specific requirements — both **functional** (what it does) and **non-functional** (how well it does it: scalability, availability, latency, cost).

In interviews, you're evaluated on how you **structure ambiguity**, make **trade-offs**, and communicate — not on producing a single "correct" answer.

**Key non-functional goals:**

| Goal | Question it answers |
|------|---------------------|
| Scalability | Can it handle 10×/100× more load? |
| Availability | Is it up when users need it? (e.g. 99.9%) |
| Latency | How fast does it respond? |
| Consistency | Do all users see the same data? |
| Reliability | Does it behave correctly over time? |
| Cost | Is it affordable to run? |

---

## Scalability: Vertical vs Horizontal

**Vertical scaling (scale up):** add more power (CPU, RAM) to a single machine.
**Horizontal scaling (scale out):** add more machines and distribute the load.

| | Vertical (scale up) | Horizontal (scale out) |
|---|---|---|
| How | Bigger server | More servers |
| Limit | Hardware ceiling | Near-unlimited |
| Complexity | Simple | Needs load balancing, coordination |
| Failure | Single point of failure | Resilient (redundancy) |
| Cost curve | Expensive at the top | More linear |

```
Vertical:                 Horizontal:
   [ BIG server ]            [server] [server] [server]
                                   \     |     /
                                   [ Load Balancer ]
```

> Modern large systems favor **horizontal scaling** with stateless services so any instance can handle any request.

---

## Load Balancing

A **load balancer (LB)** distributes incoming traffic across multiple servers to improve throughput, reliability, and availability.

```
            ┌──────────────┐
 Clients ──▶│ Load Balancer│──▶ Server 1
            │              │──▶ Server 2
            └──────────────┘──▶ Server 3
```

**Common algorithms:**

| Algorithm | How it picks a server |
|-----------|----------------------|
| Round Robin | Each server in turn |
| Least Connections | Server with fewest active connections |
| IP Hash | Based on client IP (sticky) |
| Weighted | Stronger servers get more traffic |

The LB also performs **health checks** and stops routing to unhealthy instances. For session affinity, prefer **stateless** servers (store sessions in Redis) so the LB can route freely.

---

## Caching

A **cache** stores frequently accessed data in fast storage (memory) to reduce latency and database load.

**Where caches live:** browser → CDN → reverse proxy → application (Redis/Memcached) → database.

**Cache strategies:**

| Strategy | How it works | Trade-off |
|----------|-------------|-----------|
| Cache-aside (lazy) | App checks cache; on miss, loads DB then fills cache | First read slow; can go stale |
| Write-through | Write to cache and DB together | Consistent; slower writes |
| Write-behind | Write to cache, async to DB | Fast; risk of loss |
| TTL expiry | Entries expire after a time | Simple staleness control |

> **"There are only two hard things in CS: cache invalidation and naming things."** Decide how stale data is tolerable, then pick a strategy.

---

## Databases

### SQL vs NoSQL

| | SQL (Relational) | NoSQL |
|---|---|---|
| Schema | Fixed, structured | Flexible |
| Examples | PostgreSQL, MySQL | MongoDB, Cassandra, Redis, DynamoDB |
| Scaling | Vertical (harder to shard) | Horizontal (built-in) |
| Consistency | Strong (ACID) | Often eventual (BASE) |
| Best for | Complex queries, transactions | Huge scale, flexible/unstructured data |

### Replication

Copying data across multiple database nodes.

```
        writes
          │
      ┌───▼────┐   replicate   ┌─────────┐
      │ Primary│──────────────▶│ Replica │ ◀── reads
      └────────┘──────────────▶│ Replica │ ◀── reads
                                └─────────┘
```

- **Primary-replica (master-slave):** writes go to the primary, reads can be spread across replicas. Improves read scalability and provides failover.
- **Trade-off:** replication lag → replicas may serve slightly stale data (eventual consistency).

### Sharding (Partitioning)

Splitting data **horizontally** across multiple databases so each holds a subset.

```
 users A–H ──▶ Shard 1
 users I–P ──▶ Shard 2
 users Q–Z ──▶ Shard 3
```

- **Shard key** determines placement (e.g. user ID hash). Choose carefully to avoid **hot shards**.
- **Pros:** scales writes and storage beyond one machine.
- **Cons:** cross-shard queries/joins and rebalancing are hard.

---

## CAP Theorem

In a distributed system you can only fully guarantee **two of three** during a network partition:

| Letter | Meaning |
|--------|---------|
| **C** — Consistency | Every read sees the latest write |
| **A** — Availability | Every request gets a (non-error) response |
| **P** — Partition tolerance | System works despite network failures |

```
            Consistency
               /\
              /  \
             /    \
    CP ◀────/      \────▶ AP
           /        \
  Availability ◀────── Partition Tolerance
```

Because network partitions **will** happen, real systems choose between:
- **CP** (e.g. traditional RDBMS, MongoDB default): stay consistent, sacrifice availability during a partition.
- **AP** (e.g. Cassandra, DynamoDB): stay available, accept eventual consistency.

---

## Message Queues

A **message queue** decouples producers from consumers, enabling **asynchronous** processing.

```
 Producer ──▶ [ Queue: ▣ ▣ ▣ ] ──▶ Consumer(s)
```

**Why use one:**
- **Decoupling** — services don't call each other directly.
- **Load leveling** — absorb traffic spikes; consumers process at their pace.
- **Resilience** — messages persist if a consumer is down.
- **Async work** — emails, image processing, notifications.

**Examples:** RabbitMQ, Amazon SQS, Apache Kafka (also a distributed log/streaming platform).

---

## CDN (Content Delivery Network)

A **CDN** is a network of geographically distributed edge servers that cache content close to users.

```
 User (India)  ──▶ Edge (Mumbai)   ─┐
 User (USA)    ──▶ Edge (New York) ─┼──▶ Origin server (only on cache miss)
 User (Europe) ──▶ Edge (London)   ─┘
```

- Serves static assets (images, CSS, JS, video) and increasingly dynamic content from the **nearest** location → lower latency.
- Reduces load on the origin and improves availability.
- Examples: Cloudflare, Akamai, AWS CloudFront.

---

## API Gateway

A single entry point that sits in front of backend services and handles **cross-cutting concerns**.

```
 Clients ──▶ [ API Gateway ] ──▶ Auth Service
                   │           ──▶ User Service
                   │           ──▶ Order Service
       (auth, rate limiting, routing, logging)
```

**Responsibilities:** request routing, authentication/authorization, rate limiting, request/response transformation, logging, and aggregation. Common in microservice architectures.

---

## Microservices vs Monolith

| | Monolith | Microservices |
|---|---|---|
| Structure | One deployable unit | Many small services |
| Early speed | Fast to build | Slower to set up |
| Deployment | All at once | Independent per service |
| Scaling | Scale the whole app | Scale hot services only |
| Team fit | Small teams | Many autonomous teams |
| Complexity | Low | High (network, ops, data) |
| Failure isolation | Weak | Strong |

> **Advice:** start with a **well-structured monolith**; split into microservices when team size, scaling needs, or deployment friction justify the added complexity.

---

## Rate Limiting

Restricting how many requests a client can make in a time window — protects against abuse, brute force, and overload.

**Common algorithms:**

| Algorithm | Idea |
|-----------|------|
| Token Bucket | Tokens refill at a fixed rate; each request spends one |
| Leaky Bucket | Requests processed at a steady rate; overflow dropped |
| Fixed Window | Count per fixed interval (e.g. 100/min) |
| Sliding Window | Smooths edges of fixed windows for fairness |

Typically implemented at the **API gateway** or with a shared store like **Redis**. Return HTTP **429 Too Many Requests** when exceeded.

---

## How to Approach a System Design Interview

A repeatable framework. **Don't jump to drawing boxes** — clarify first.

1. **Clarify requirements (functional & non-functional).** Ask questions. Who uses it? Core features? Read-heavy or write-heavy? Scale?
2. **Estimate scale (back-of-the-envelope).** Users, requests/sec (QPS), data size, read:write ratio. This drives your decisions.
3. **Define the API.** List the main endpoints/operations (the contract).
4. **Design the data model.** Entities, relationships, SQL vs NoSQL choice.
5. **Draw the high-level architecture.** Clients → LB → services → cache → DB. Add a queue/CDN where useful.
6. **Deep-dive into 1–2 components.** Wherever the interviewer probes — sharding, caching, the hot path.
7. **Address bottlenecks & trade-offs.** Single points of failure, scaling reads/writes, consistency, monitoring.
8. **Summarize.** Recap the design and how it meets the requirements.

> **Communicate constantly.** Think out loud, state assumptions, and discuss trade-offs. The interviewer is evaluating your reasoning.

**Handy estimation cheats:**
- 1 day ≈ 86,400 seconds (~10⁵).
- 1 million requests/day ≈ ~12 requests/sec average (plan for peaks 2–10×).

---

## Worked Mini-Example 1: Design a URL Shortener (e.g. bit.ly)

**1. Requirements**
- Functional: shorten a long URL → short code; redirect short code → original URL.
- Non-functional: very **read-heavy** (redirects ≫ creates), low latency, high availability.

**2. Scale estimate (assumed)**
- 100M new URLs/month, read:write ≈ 100:1 → redirects dominate. Optimize reads.

**3. API**
```
POST /shorten      { "url": "https://..." }  -> { "short": "abc123" }
GET  /{shortCode}  -> 301 redirect to original URL
```

**4. Data model** (key-value fits well)

| Field | Type |
|-------|------|
| short_code (PK) | string (e.g. 7 chars) |
| long_url | string |
| created_at | timestamp |

A 7-character base62 code (`[A-Za-z0-9]`) gives 62⁷ ≈ **3.5 trillion** combinations — plenty.

**5. Generating the short code**
- Option A: hash the URL (e.g. MD5) and take a slice — handle collisions by re-hashing.
- Option B (cleaner): a **counter / ID generator** encoded to base62 — guarantees uniqueness, no collisions.

**6. High-level architecture**
```
 Client ──▶ Load Balancer ──▶ App Servers ──▶ Cache (Redis)  ──hit──▶ return URL
                                     │             │ miss
                                     │             ▼
                                     └────────▶ Database (KV store)
   Redirects served from cache for low latency; CDN can cache hot ones.
```

**7. Trade-offs & scaling**
- Cache hot short codes in Redis (cache-aside) — redirects are the hot path.
- Shard the DB by short_code; add read replicas.
- Use **301** (permanent, cacheable) or **302** (lets you track clicks) — a real trade-off.
- Analytics (click counts) can be done async via a **message queue** to avoid slowing redirects.

---

## Worked Mini-Example 2: Design a Basic Chat App

**1. Requirements**
- Functional: 1:1 messaging, real-time delivery, message history, online/offline status.
- Non-functional: low latency, reliable delivery, scalable to many concurrent connections.

**2. Key challenge**
- HTTP request/response isn't ideal for real-time push. Use **WebSockets** (persistent, bidirectional) so the server can push messages instantly. (Fallback: long polling.)

**3. API / events**
```
WS connect          -> establish persistent connection
send_message        { to, text }
receive_message     { from, text, timestamp }
GET /conversations/{id}/messages?cursor=...   (history, paginated)
```

**4. Data model**

| Table | Key fields |
|-------|-----------|
| users | id, name, last_seen |
| conversations | id, participant_ids |
| messages | id, conversation_id, sender_id, text, created_at, delivered |

Messages are write-heavy and time-ordered → a NoSQL store like **Cassandra** (partition by conversation_id, sorted by time) fits well.

**5. High-level architecture**
```
  User A ⇄ WebSocket ⇄ ┌──────────────┐
                       │ Chat Servers │⇄ Message Queue / Pub-Sub (Redis/Kafka)
  User B ⇄ WebSocket ⇄ └──────────────┘
                              │
                              ▼
                     Message DB (Cassandra)  +  Presence (Redis)
```

**6. How a message flows**
1. User A sends a message over its WebSocket to a chat server.
2. Server persists it to the DB and publishes it to a **pub/sub** channel.
3. The chat server holding **User B's** connection receives it and pushes it down B's WebSocket.
4. If B is offline, store as undelivered and deliver on reconnect (or send a push notification).

**7. Trade-offs & scaling**
- A user may connect to **any** chat server → use **pub/sub (Redis/Kafka)** so servers can route messages to whichever server holds the recipient's connection.
- Track **presence** (online/offline) in Redis with heartbeats/TTL.
- Scale horizontally by adding chat servers behind a load balancer that supports sticky WebSocket connections.
- Guarantee delivery with acknowledgements and retry/idempotency.

---

### How to use this file
- Learn the **building blocks** above, then practice the **8-step framework** on new problems.
- Always state assumptions and discuss **trade-offs** out loud.
- Try designing: a news feed, a rate limiter, a file-storage service, and a ride-sharing app using the same framework.
