# High Level Design (HLD) — Interview & Exam Notes

---

## 1. Introduction / Overview

**High Level Design (HLD)** is the process of designing the **overall architecture** of a system. It defines what components exist, how they communicate, where data lives, and how the system scales. HLD happens *before* Low Level Design (LLD) and is the bridge between business requirements and actual implementation.

**Key Goals of HLD:**
- Break the system into major services/components
- Define communication protocols and data flows
- Choose databases, caches, message queues, and external integrations
- Address non-functional requirements: scalability, availability, reliability, latency, security
- Identify single points of failure and plan for redundancy

**HLD vs LLD:**

| Aspect         | HLD                                             | LLD                                        |
|----------------|-------------------------------------------------|--------------------------------------------|
| Granularity    | Service/component level                         | Class/method level                         |
| Audience       | Architects, Tech Leads, Stakeholders            | Developers                                 |
| Output         | Architecture diagrams, data flow diagrams       | Class diagrams, sequence diagrams, pseudocode |
| Focus          | What to build and how pieces communicate        | How to implement each piece                |
| Tools          | Boxes/arrows, sequence diagrams                 | UML class diagrams, code                   |

---

## 2. System Design Vocabulary

| Term                    | Definition                                                               |
|-------------------------|--------------------------------------------------------------------------|
| Scalability             | Ability to handle increasing load                                        |
| Availability            | Fraction of time the system is operational (e.g., 99.99%)                |
| Reliability             | Probability of correct operation over time                               |
| Latency                 | Time for a single operation to complete                                  |
| Throughput              | Number of operations per second                                          |
| Fault Tolerance         | System continues operating despite failures                              |
| Durability              | Data survives failures once committed                                    |
| Consistency             | All nodes see the same data at the same time                             |
| Partition Tolerance     | System operates despite network partitions                               |
| SLA                     | Service Level Agreement — contractual uptime/performance guarantee       |
| SLO                     | Service Level Objective — internal target (e.g., p99 latency < 100ms)   |
| SLI                     | Service Level Indicator — measured metric (e.g., actual p99 latency)    |
| RPO                     | Recovery Point Objective — max acceptable data loss (time)              |
| RTO                     | Recovery Time Objective — max acceptable downtime                        |

---

## 3. Scalability

### 3.1 Vertical vs Horizontal Scaling

| Type               | Description                                      | Pros                              | Cons                            |
|--------------------|--------------------------------------------------|-----------------------------------|---------------------------------|
| Vertical (Scale Up)| Add more CPU/RAM to existing server              | Simple; no code change            | Hardware limits; single point of failure; expensive |
| Horizontal (Scale Out) | Add more servers to distribute load          | Virtually unlimited; fault tolerant | Requires stateless design; more complexity |

### 3.2 Stateless vs Stateful Architecture

- **Stateless:** Each request carries all information needed; any server can handle it. Enables easy horizontal scaling.
- **Stateful:** Server remembers client state between requests. Use **sticky sessions** or offload state to a shared store (Redis, DB).

### 3.3 Load Balancing

A **Load Balancer (LB)** distributes incoming traffic across multiple servers.

**Algorithms:**

| Algorithm               | Description                                               | Best For                          |
|-------------------------|-----------------------------------------------------------|-----------------------------------|
| Round Robin             | Requests go to each server in turn                        | Homogeneous servers               |
| Weighted Round Robin    | Servers with higher capacity get more requests            | Heterogeneous servers             |
| Least Connections       | Route to server with fewest active connections            | Long-lived connections            |
| IP Hash                 | Client IP hashed to always route to same server           | Sticky sessions without cookies   |
| Random                  | Random server selection                                   | Simple; stateless services        |
| Resource-Based          | Route based on current CPU/memory of servers              | Dynamic workloads                 |

**Types of Load Balancers:**
- **L4 (Transport Layer):** Routes based on IP + TCP/UDP port; fast, no content inspection
- **L7 (Application Layer):** Routes based on HTTP headers, URL, cookies; more flexible; can do SSL termination

**Health Checks:** LBs periodically probe backends; remove unhealthy servers automatically.

---

## 4. Caching

Caching stores a copy of frequently accessed data in a **faster storage layer** to reduce latency and database load.

### 4.1 Cache Levels

| Level            | Example                              | Latency        |
|------------------|--------------------------------------|----------------|
| CPU Cache (L1/L2/L3) | Hardware                         | ~1 ns          |
| In-process cache | HashMap, Guava Cache                 | ~100 ns        |
| Distributed cache | Redis, Memcached                    | ~1 ms          |
| CDN              | CloudFront, Akamai                   | ~10 ms         |
| Database buffer  | InnoDB buffer pool, PostgreSQL cache | ~1 ms          |

### 4.2 Cache Strategies

| Strategy          | Description                                                       | Write Behavior                    |
|-------------------|-------------------------------------------------------------------|-----------------------------------|
| Cache-Aside (Lazy)| App checks cache first; on miss, loads from DB and populates cache | App writes to DB; cache invalidated or expired |
| Write-Through     | Write to cache and DB synchronously                               | Consistent; higher write latency  |
| Write-Behind (Write-Back) | Write to cache immediately; async write to DB            | Low latency writes; risk of loss  |
| Read-Through      | Cache automatically loads from DB on miss                         | Cache manages read logic           |
| Refresh-Ahead     | Proactively refresh cache before expiry                           | Low latency for hot keys           |

### 4.3 Cache Eviction Policies

| Policy   | Description                                              |
|----------|----------------------------------------------------------|
| LRU      | Evict Least Recently Used item                           |
| LFU      | Evict Least Frequently Used item                         |
| FIFO     | Evict oldest entry first                                 |
| TTL      | Evict after a fixed Time To Live expires                 |
| Random   | Evict a random item                                      |

### 4.4 Cache Pitfalls

| Problem          | Description                                               | Solution                           |
|------------------|-----------------------------------------------------------|------------------------------------|
| Cache Stampede   | Many requests hit DB simultaneously on cache expiry       | Mutex lock; probabilistic expiry   |
| Cache Avalanche  | Many keys expire at same time                             | Randomize TTL values               |
| Cache Penetration| Requests for non-existent keys bypass cache repeatedly    | Cache null results; Bloom filter   |
| Thundering Herd  | System restart causes flood of DB queries                 | Warm-up cache on startup           |
| Hot Key          | One key receives disproportionate traffic                 | Replicate hot key to multiple shards |

### 4.5 Redis Overview

- **Data structures:** String, List, Hash, Set, Sorted Set, Streams, HyperLogLog, Bitmaps
- **Persistence:** RDB snapshots, AOF (Append-Only File), or hybrid
- **High availability:** Redis Sentinel (monitoring + failover), Redis Cluster (partitioning)
- **Use cases:** Sessions, leaderboards, pub/sub, rate limiting, distributed locks

---

## 5. Databases

### 5.1 SQL vs NoSQL

| Aspect            | SQL (Relational)                          | NoSQL                                    |
|-------------------|-------------------------------------------|------------------------------------------|
| Schema            | Fixed, predefined                         | Dynamic / schema-less                    |
| Query Language    | SQL                                       | Varies by type                           |
| Transactions      | Full ACID                                 | Often BASE (eventual consistency)        |
| Scalability       | Vertical primarily (read replicas for horizontal) | Horizontal by design              |
| Joins             | Native, efficient                         | Usually avoided; denormalized data       |
| Best For          | Structured data, complex queries          | Flexible/unstructured data, high scale   |

### 5.2 NoSQL Types

| Type           | Examples                     | Best Use Case                              |
|----------------|------------------------------|--------------------------------------------|
| Document       | MongoDB, CouchDB             | Flexible schemas, catalogs, user profiles  |
| Key-Value      | Redis, DynamoDB              | Sessions, caching, simple lookups          |
| Column-Family  | Cassandra, HBase             | Time series, write-heavy, wide rows        |
| Graph          | Neo4j, Amazon Neptune        | Social networks, fraud detection           |
| Search         | Elasticsearch, OpenSearch    | Full-text search, log analytics            |
| Time Series    | InfluxDB, TimescaleDB        | Metrics, IoT, monitoring                   |

### 5.3 Database Replication

**Purpose:** High availability, read scaling, disaster recovery.

| Type               | Description                                                        |
|--------------------|--------------------------------------------------------------------|
| Master-Slave (Primary-Replica) | Writes go to master; replicated to slaves for reads  |
| Master-Master      | Multiple masters; all accept writes; conflict resolution needed    |
| Synchronous Replication | Master waits for replica acknowledgment; no data loss; slower |
| Asynchronous Replication | Master doesn't wait; possible data loss on failure; faster  |

### 5.4 Database Sharding

**Sharding** splits data across multiple DB instances (shards) to scale writes and storage.

| Strategy           | Description                                          | Pros / Cons                               |
|--------------------|------------------------------------------------------|-------------------------------------------|
| Hash-based         | `shard = hash(key) % N`                              | Even distribution; hard to rebalance      |
| Range-based        | Records in range [A-M] go to shard 1, [N-Z] to shard 2 | Easy range queries; hot spots possible |
| Directory-based    | Lookup table maps key to shard                       | Flexible; lookup table is bottleneck      |
| Geo-based          | Shard by geographic region                           | Low latency per region; uneven load       |

**Challenges:** Cross-shard queries, transactions, rebalancing (use **consistent hashing** for dynamic cluster).

### 5.5 Consistent Hashing

Maps both keys and servers onto a **virtual ring**. A key is assigned to the first server clockwise from its hash position.

- Adding/removing a server only affects adjacent keys (O(K/N) keys remapped vs O(K) in regular hashing)
- **Virtual nodes:** each server maps to multiple ring positions for better distribution

### 5.6 CAP Theorem

> A distributed system can guarantee at most **two of three** properties simultaneously:
> - **C**onsistency: every read gets the most recent write
> - **A**vailability: every request gets a response (not necessarily latest data)
> - **P**artition Tolerance: system works despite network partitions

**In practice:** Network partitions happen, so you must choose **CP** or **AP**.

| System       | Chooses | Reason                                    |
|--------------|---------|-------------------------------------------|
| HBase, Zookeeper | CP  | Stops serving during partition to stay consistent |
| Cassandra, CouchDB | AP | Continues serving; may return stale data |
| MySQL (with sync replication) | CP | |

### 5.7 PACELC Theorem

Extension of CAP: **Even without partition**, there's a tradeoff between **Latency** and **Consistency**.

`If Partition: choose between A and C; Else: choose between L and C`

---

## 6. Messaging and Event-Driven Architecture

### 6.1 Why Messaging?

- **Decoupling:** Producer and consumer don't need to know each other
- **Asynchronous processing:** Fire and forget; consumer processes at its own pace
- **Buffering:** Handle traffic bursts by queuing requests
- **Reliability:** Messages persisted until successfully processed

### 6.2 Message Queue vs Event Streaming

| Aspect          | Message Queue (RabbitMQ, SQS)      | Event Streaming (Kafka)              |
|-----------------|------------------------------------|--------------------------------------|
| Delivery        | Message deleted after consumption  | Log retained; consumers read offsets |
| Consumers       | Competing consumers (one gets it)  | Consumer groups; each group gets all  |
| Ordering        | Per-queue FIFO                     | Per-partition ordering                |
| Replay          | Not supported                      | Replay from any offset               |
| Use case        | Task queues, job processing         | Event sourcing, audit log, analytics  |

### 6.3 Apache Kafka Architecture

```
Producers → [Topic (partitioned)] → Consumers
                  |
            [Partitions: P0, P1, P2]
                  |
        [Offsets: 0, 1, 2, 3, ...]
```

**Key Concepts:**
- **Topic:** Named category; split into partitions for parallelism
- **Partition:** Ordered, immutable log; each record has an offset
- **Consumer Group:** Multiple consumers sharing work; each partition assigned to one consumer
- **Broker:** Kafka server; a cluster has multiple brokers
- **Replication Factor:** Each partition replicated across N brokers for fault tolerance
- **Zookeeper / KRaft:** Cluster metadata management

**Delivery Guarantees:**
- At-most-once: message may be lost
- At-least-once: message may be duplicated (idempotent consumers needed)
- Exactly-once: via transactions (more overhead)

### 6.4 Common Messaging Patterns

| Pattern              | Description                                              |
|----------------------|----------------------------------------------------------|
| Point-to-Point       | One producer, one consumer per message (queue)           |
| Publish-Subscribe    | One producer, many consumers (topic)                     |
| Fan-Out              | Message broadcast to multiple queues simultaneously      |
| Dead Letter Queue    | Failed messages moved to DLQ for inspection              |
| Saga Pattern         | Distributed transaction via sequence of local transactions + compensating events |
| CQRS                 | Separate read and write models; events sync them         |
| Event Sourcing       | State derived from sequence of events (immutable log)    |

---

## 7. Content Delivery Network (CDN)

A **CDN** is a geographically distributed network of proxy servers that serve static content (images, JS, CSS, video) from the **closest edge node** to the user.

**Benefits:**
- Reduces latency (geo-proximity)
- Offloads traffic from origin server
- Absorbs DDoS attacks at the edge
- Built-in redundancy across PoPs (Points of Presence)

**Types:**
- **Push CDN:** You upload content to CDN proactively; good for rarely changed files
- **Pull CDN:** CDN fetches from origin on first request, caches, serves subsequent requests; good for dynamic traffic

**Examples:** Cloudflare, AWS CloudFront, Akamai, Fastly.

---

## 8. API Design

### 8.1 REST Principles

| Principle          | Description                                                        |
|--------------------|--------------------------------------------------------------------|
| Client-Server      | Separation of concerns; UI separate from data storage              |
| Stateless          | No session state on server; each request is self-contained         |
| Cacheable          | Responses labeled cacheable/non-cacheable                          |
| Uniform Interface  | Consistent resource naming, HTTP verbs, status codes               |
| Layered System     | Client doesn't know if it's talking to proxy, cache, or server     |

**HTTP Verbs:**

| Verb    | Action               | Idempotent | Safe |
|---------|----------------------|------------|------|
| GET     | Read resource        | Yes        | Yes  |
| POST    | Create resource      | No         | No   |
| PUT     | Full update/replace  | Yes        | No   |
| PATCH   | Partial update       | No         | No   |
| DELETE  | Delete resource      | Yes        | No   |

**HTTP Status Codes:**

| Range | Category      | Examples                                                |
|-------|---------------|---------------------------------------------------------|
| 2xx   | Success       | 200 OK, 201 Created, 204 No Content                     |
| 3xx   | Redirection   | 301 Moved Permanently, 304 Not Modified                 |
| 4xx   | Client Error  | 400 Bad Request, 401 Unauthorized, 403 Forbidden, 404 Not Found, 429 Too Many Requests |
| 5xx   | Server Error  | 500 Internal Server Error, 502 Bad Gateway, 503 Service Unavailable |

### 8.2 REST vs GraphQL vs gRPC

| Aspect        | REST                        | GraphQL                          | gRPC                             |
|---------------|-----------------------------|----------------------------------|----------------------------------|
| Protocol      | HTTP/1.1                    | HTTP/1.1                         | HTTP/2                           |
| Data Format   | JSON/XML                    | JSON                             | Protobuf (binary)                |
| Fetching      | Fixed endpoints per resource | Client specifies exact fields    | Method calls (RPC)               |
| Over-fetching | Common                      | Eliminated                       | N/A                              |
| Best For      | Public APIs, CRUD            | Complex queries, mobile apps     | Internal services, streaming     |
| Schema        | OpenAPI/Swagger              | GraphQL Schema Definition Language | Proto files                   |

### 8.3 API Gateway

An **API Gateway** sits between clients and backend services, providing:
- **Routing** to appropriate microservice
- **Authentication and authorization** (JWT, OAuth2)
- **Rate limiting** and throttling
- **SSL termination**
- **Request/response transformation**
- **Logging and monitoring**

Examples: Kong, AWS API Gateway, NGINX, Apigee.

### 8.4 API Versioning Strategies

| Strategy         | Example                                  | Notes                            |
|------------------|------------------------------------------|----------------------------------|
| URI versioning   | `/api/v1/users`                          | Most common; clear; breaks REST  |
| Query param      | `/api/users?version=1`                   | Easy; not cacheable              |
| Header versioning| `Accept: application/vnd.api.v1+json`   | Clean URI; harder to test        |
| Subdomain        | `v1.api.example.com/users`               | Flexible; DNS overhead           |

---

## 9. Microservices Architecture

### 9.1 Monolith vs Microservices

| Aspect           | Monolith                              | Microservices                         |
|------------------|---------------------------------------|---------------------------------------|
| Deployment       | Single deployable unit                | Independent services deployed separately |
| Scaling          | Scale entire app                      | Scale individual services             |
| Development      | Simple initially; gets complex        | Complex initially; better long-term   |
| Failure          | One bug can crash everything          | Isolated failures                     |
| Technology       | Single stack                          | Polyglot (different stacks per service) |
| Data             | Shared database                       | Database per service                  |
| Communication    | In-process method calls               | Network calls (REST, gRPC, events)    |

### 9.2 Microservices Patterns

| Pattern              | Problem Solved                                          | Solution                             |
|----------------------|---------------------------------------------------------|--------------------------------------|
| API Gateway          | Client routing to many services                         | Single entry point for clients       |
| Service Discovery    | Services finding each other dynamically                 | Service Registry (Consul, Eureka)    |
| Circuit Breaker      | Prevent cascading failures                              | Track failure rate; open circuit     |
| Saga                 | Distributed transactions across services                | Choreography or Orchestration        |
| CQRS                 | Different read/write loads                              | Separate read/write models           |
| Sidecar              | Cross-cutting concerns per service                      | Proxy container handles concerns     |
| Strangler Fig        | Migrate monolith to microservices incrementally         | Route features one by one to new service |
| Bulkhead             | Isolate failures between components                     | Separate thread pools per service    |

### 9.3 Service Mesh

A **service mesh** (e.g., Istio, Linkerd) handles service-to-service communication concerns as infrastructure:
- Mutual TLS (mTLS) between services
- Traffic management (retries, timeouts, circuit breaking)
- Observability (distributed tracing, metrics)
- Fine-grained authorization policies

---

## 10. Distributed System Concepts

### 10.1 Consensus Algorithms

Used to agree on a value across distributed nodes despite failures.

| Algorithm  | Description                                             | Used In                         |
|------------|---------------------------------------------------------|---------------------------------|
| Paxos      | Multi-phase protocol for consensus; complex to implement | Google Chubby, Zookeeper (ZAB) |
| Raft       | Simpler than Paxos; leader election + log replication   | etcd, CockroachDB, TiKV         |

**Raft Basics:**
- Nodes are in one of three states: Leader, Follower, Candidate
- Leader receives all writes; replicates to followers
- If leader fails, election: node becomes candidate, requests votes, majority → new leader

### 10.2 Distributed Locking

- **Purpose:** Mutual exclusion across multiple nodes
- **Redis SETNX:** `SET key value NX PX 30000` — set if not exists with TTL
- **Redlock Algorithm:** Acquire lock on majority of N Redis nodes; release all on completion
- **ZooKeeper ephemeral nodes:** Create ephemeral node; node deleted when client disconnects

### 10.3 Vector Clocks & Logical Clocks

- **Lamport Clocks:** Assign logical timestamp; causal ordering
- **Vector Clocks:** Per-process counter; detect causality and conflicts in distributed systems

### 10.4 Leader Election

**Using Zookeeper:**
1. All nodes create an ephemeral sequential znode under `/election/`
2. Node with the smallest znode ID becomes leader
3. Other nodes watch the next smaller znode for deletion (avoids herd effect)
4. If leader dies, its ephemeral node deleted → next node becomes leader

### 10.5 Two-Phase Commit (2PC)

Distributed transaction protocol across multiple databases:
1. **Prepare phase:** Coordinator asks all participants to prepare; each votes Yes/No
2. **Commit phase:** If all voted Yes, coordinator sends Commit; else sends Abort

**Limitation:** Coordinator is a single point of failure; blocking protocol (participants hold locks).

**Three-Phase Commit (3PC):** Adds a pre-commit phase to avoid blocking, but still vulnerable to network partitions.

---

## 11. Common HLD Interview Problems

### 11.1 Design URL Shortener

**Functional Requirements:** Create short URL, redirect to original, optional analytics, custom aliases, expiry.

**Non-Functional Requirements:** 100M URLs, 100:1 read:write ratio, < 100ms latency, 99.99% availability.

**Architecture Components:**
```
Client → CDN → Load Balancer → [URL Service] → [Short URL DB (sharded)]
                                     ↓
                               [Cache (Redis)]
                                     ↓
                             [Analytics Service] → [Analytics DB (Cassandra)]
```

**Key Decisions:**
- **ID Generation:** Auto-increment + Base62 encoding OR UUID (risk of collision) → prefer distributed ID generator (Snowflake)
- **DB Choice:** Key-value store (Cassandra/DynamoDB) for URL → originalURL mapping; scale easily
- **Cache:** Redis with LRU; cache top 20% hot URLs (handles 80% traffic)
- **Redirect:** 301 (permanent, cached by browser) vs 302 (temporary, every request reaches server for analytics)

---

### 11.2 Design a Twitter/X

**Functional:** Post tweets (280 chars), follow users, home timeline, search.

**Back-of-envelope:**
- 300M users, 100M daily active users (DAU)
- 100M tweets/day → ~1200 writes/sec
- Timeline: 30 tweets/read × 100M DAU → heavy reads (fanout problem)

**Architecture Components:**
```
[Mobile/Web] → [API Gateway] → [Tweet Service]
                                    → [Tweet DB (MySQL/Cassandra)]
                                    → [Media Service → Object Storage (S3)]
                              → [Timeline Service]
                                    → [Timeline Cache (Redis)] ← precomputed
                              → [Notification Service → Push Gateway]
                              → [Search Service (Elasticsearch)]
                              → [Follow Service → Graph DB / MySQL]
```

**Fanout Strategies:**

| Strategy     | Description                                                     | Best For              |
|--------------|-----------------------------------------------------------------|-----------------------|
| Fanout on Write (Push) | Pre-compute timelines; write to all followers' feeds on tweet | Low follower count  |
| Fanout on Read (Pull)  | Compute timeline on request; merge followed users' tweets  | High follower count (celebrities) |
| Hybrid       | Push for regular users; pull for celebrities                    | Twitter's actual approach |

---

### 11.3 Design a WhatsApp / Chat System

**Functional:** 1:1 messaging, group chats, online presence, message delivery receipts.

**Back-of-envelope:**
- 2B users, 50B messages/day
- Peak: 500K messages/sec
- Message storage: 50B × 100 bytes = 5TB/day

**Architecture Components:**
```
[Client] ──WebSocket──> [Chat Service (stateful)] ──> [Message Queue (Kafka)]
                                                            ↓
                                                  [Message Storage (Cassandra)]
                                                  [Notification Service]
                                                  [Presence Service (Redis)]
```

**Key Decisions:**
- **Protocol:** WebSocket for persistent bidirectional connection (vs polling)
- **Message Storage:** Cassandra — `(userId, recipientId, messageId)` partition key; write-heavy
- **Delivery Receipts:** Message states: Sent → Delivered → Read
- **Group Messaging:** Fanout to all group members via message queue
- **Presence:** Redis with TTL; heartbeat from client every 30s

---

### 11.4 Design a Ride-Sharing App (Uber/Lyft)

**Functional:** Request ride, match driver, track location, calculate fare, payment.

**Back-of-envelope:**
- 20M daily trips
- Location updates from drivers every 5s: 1M active drivers × (1/5) = 200K updates/sec

**Architecture Components:**
```
[Rider App] → [API Gateway] → [Ride Service]
                               → [Matching Service] ← [Driver Location Service]
                               → [Pricing Service]
                               → [Trip Service → Trip DB (MySQL)]
                               → [Notification Service]
[Driver App] → [Location Service] → [Location Cache (Redis Geospatial)]
                                  → [Kafka (location events)]
```

**Key Decisions:**
- **Geo-indexing:** Redis `GEOADD`/`GEORADIUS` for O(N) proximity queries; or S2 Geometry cells for sharded geospatial lookups
- **Matching Algorithm:** Find nearby drivers → rank by ETA → assign closest available
- **Dynamic Pricing:** Surge multiplier based on supply/demand ratio in a geo zone
- **GPS Updates:** Driver app sends location via UDP (tolerates loss); stored in Redis with TTL

---

### 11.5 Design Netflix / Video Streaming

**Functional:** Upload videos, transcode, stream at adaptive bitrate, search, recommendations.

**Back-of-envelope:**
- 200M subscribers, 100M hours of streaming/day
- 1 hour HD video ≈ 4GB → 100M × 4GB / 86400s ≈ 46TB/s bandwidth

**Architecture Components:**
```
[Content Creator] → [Ingest Service] → [Raw Storage (S3)]
                                     → [Transcoding Pipeline (AWS Elemental)]
                                       → [Multiple Resolutions (360p/720p/1080p/4K)]
                                         → [CDN (OpenConnect / CloudFront)]
                                                  ↓
[Viewer] ← [CDN Edge Node] ← [DASH/HLS Adaptive Bitrate Streaming]
                                  ↑
                         [Playback Service] → [Content DB]
                                           → [DRM Service]
```

**Key Decisions:**
- **ABR Streaming:** DASH or HLS — player adjusts quality based on available bandwidth
- **CDN:** Netflix OpenConnect caches content at ISP PoPs; 95%+ traffic served from CDN
- **Transcoding:** Parallel transcoding to multiple bitrates/resolutions at upload time
- **Recommendation:** Collaborative filtering + content-based; trained offline, served online
- **Search:** Elasticsearch with fuzzy matching and faceted filters

---

### 11.6 Design a Search Autocomplete System

**Functional:** Show top 5-10 suggestions as user types.

**Back-of-envelope:**
- Google: 5B searches/day; 20 chars avg; 5 keystrokes/prefix → 50B prefix lookups/day → 600K/sec

**Data Structure:** **Trie** for prefix matching; but naive trie too slow → optimize with:
- Top-K frequencies stored at each trie node
- Regular recomputation (daily batch job updates frequencies)

**Architecture:**
```
[User keystroke] → [API Gateway] → [Autocomplete Service]
                                       → [Trie Cache (Redis/in-memory)]
                                       → [Search Frequency DB]
[Log Aggregator] → [Batch Job] → updates Trie → [Trie DB]
```

**Filtering:** Content moderation removes offensive suggestions from trie.

---

### 11.7 Design a Distributed Key-Value Store

**Functional:** get(key), put(key, value), delete(key); tunable consistency.

**Key Design Decisions:**

| Decision         | Options                                                   |
|------------------|-----------------------------------------------------------|
| Data partitioning | Consistent hashing with virtual nodes                    |
| Replication      | Replicate to N nodes; coordinator + N-1 peers             |
| Consistency model | Quorum: W + R > N (strong); W=1, R=1 (eventual)          |
| Conflict resolution | Last-write-wins (timestamp) or vector clocks + client merge |
| Failure detection | Gossip protocol                                          |
| Anti-entropy     | Merkle tree comparison to sync replicas                   |

**Inspired by:** Amazon Dynamo, Apache Cassandra.

---

## 12. Reliability and Fault Tolerance

### 12.1 Availability Tiers

| Availability | Downtime/Year | Downtime/Month |
|--------------|---------------|----------------|
| 99%          | 3.65 days     | 7.3 hours      |
| 99.9%        | 8.76 hours    | 43.8 minutes   |
| 99.99%       | 52.6 minutes  | 4.4 minutes    |
| 99.999%      | 5.26 minutes  | 26.3 seconds   |

**Formula:** `Availability (systems in sequence) = A1 × A2 × ... × An`
**Formula:** `Availability (systems in parallel) = 1 − (1−A1) × (1−A2)`

### 12.2 Circuit Breaker Pattern

**States:**
- **Closed:** Normal operation; requests flow through; failure count tracked
- **Open:** Too many failures; requests fail immediately without calling downstream
- **Half-Open:** Trial requests allowed; if they succeed, return to Closed; else back to Open

```
[Request] → [Circuit Breaker] → [Service]
                ↑
    [Failure Rate Monitor]
```

**Libraries:** Resilience4j (Java), Hystrix (deprecated), Polly (.NET).

### 12.3 Retry with Exponential Backoff

```
retryDelay = min(baseDelay × 2^attempt + jitter, maxDelay)
```

- **Jitter** prevents synchronized retries from all clients (thundering herd)
- Set a maximum number of retries (e.g., 3-5)
- Only retry on **transient errors** (5xx, network timeout); never on 4xx

### 12.4 Bulkhead Pattern

Isolate failures between components by using **separate thread pools or connection pools** per downstream service. Like bulkheads in a ship — one flooded compartment doesn't sink the whole ship.

### 12.5 Failover Strategies

| Strategy          | Description                                          |
|-------------------|------------------------------------------------------|
| Active-Active     | Multiple instances all serve traffic; automatic load distribution |
| Active-Passive    | Standby takes over when primary fails; slight delay  |
| Warm Standby      | Standby is running but not serving traffic           |
| Cold Standby      | Standby must be started up when primary fails        |

### 12.6 Chaos Engineering

**Principle:** Intentionally inject failures in production to discover weaknesses before they cause outages.

**Tools:** Netflix Chaos Monkey, Gremlin, Litmus (Kubernetes).

**Practice:** Define steady-state metrics → inject failure → observe → fix → repeat.

---

## 13. Security in System Design

### 13.1 Authentication vs Authorization

| Concept        | Definition                                              | Example                         |
|----------------|---------------------------------------------------------|---------------------------------|
| Authentication | Verify **who you are** (identity)                       | Login with username/password    |
| Authorization  | Verify **what you can do** (permissions)                | Admin can delete; user cannot   |

### 13.2 Common Auth Patterns

**JWT (JSON Web Token):**
```
Header.Payload.Signature
{alg: "HS256"} . {sub: "user123", exp: 1234567890} . HMAC(header+payload, secret)
```
- Stateless; no server-side session storage
- Verify signature on each request
- Short expiry (15-60 min) + refresh token pattern

**OAuth 2.0 Flow (Authorization Code):**
```
User → App → Auth Server → (user approves) → Authorization Code
App → Auth Server → (exchange code) → Access Token + Refresh Token
App → Resource Server + Access Token → Protected Data
```

**Session-based Auth:**
- Server stores session in Redis/DB
- Client stores session ID in cookie (HttpOnly, Secure, SameSite)
- Suitable for server-rendered apps

### 13.3 Encryption

| Type                | Description                                          | Use Case                        |
|---------------------|------------------------------------------------------|---------------------------------|
| TLS/HTTPS           | Transport Layer Security; in-transit encryption      | All web traffic                 |
| AES-256             | Symmetric encryption; fast                           | Encrypting stored data          |
| RSA                 | Asymmetric encryption; slow                          | Key exchange, signatures        |
| bcrypt / Argon2     | Password hashing with salt + work factor             | Storing passwords                |
| E2E Encryption      | Only endpoints can decrypt                           | WhatsApp messages, Signal       |

### 13.4 Common Vulnerabilities

| Vulnerability     | Description                                           | Mitigation                       |
|-------------------|-------------------------------------------------------|----------------------------------|
| SQL Injection     | Malicious SQL in inputs                               | Parameterized queries, ORM       |
| XSS               | Inject scripts into pages viewed by others            | CSP headers, HTML encoding       |
| CSRF              | Forge requests using user's session                   | CSRF tokens, SameSite cookies    |
| SSRF              | Server makes request to internal network on attacker's behalf | Validate/whitelist URLs   |
| Broken Auth       | Weak session management, brute force                  | Rate limiting, MFA, strong hashing |
| Sensitive Data Exposure | Data unencrypted in transit or at rest          | TLS, encrypt at rest, mask PII   |

---

## 14. Observability

### 14.1 The Three Pillars

| Pillar  | What                                              | Tools                          |
|---------|---------------------------------------------------|--------------------------------|
| Metrics | Aggregated numerical measurements over time       | Prometheus, Grafana, Datadog   |
| Logs    | Timestamped, discrete event records               | ELK Stack (Elasticsearch, Logstash, Kibana), Loki |
| Traces  | End-to-end request path across services           | Jaeger, Zipkin, AWS X-Ray      |

### 14.2 Key Metrics to Track

**RED Method (for services):**
- **Rate:** Requests per second
- **Errors:** Error rate (%)
- **Duration:** Latency (p50, p95, p99)

**USE Method (for resources):**
- **Utilization:** % time resource is busy
- **Saturation:** Amount of work queued
- **Errors:** Error count

### 14.3 Distributed Tracing

Each request gets a **Trace ID**; each hop gets a **Span ID**. Spans form a tree showing exactly where time was spent across services.

```
Trace ID: abc123
├── Span: API Gateway (5ms)
│   ├── Span: Auth Service (2ms)
│   └── Span: User Service (8ms)
│       └── Span: DB Query (6ms)
```

### 14.4 Alerting Best Practices

- Alert on **symptoms**, not causes (alert on high error rate, not CPU usage)
- Use **multi-window, multi-burn-rate** alerting to catch both fast and slow burns
- Keep **alert fatigue** low — every alert should be actionable
- Define **runbooks** for each alert with clear remediation steps

---

## 15. Deployment and Infrastructure

### 15.1 Deployment Strategies

| Strategy          | Description                                           | Risk         | Rollback      |
|-------------------|-------------------------------------------------------|--------------|---------------|
| Big Bang          | Deploy all at once; all users see new version         | High         | Redeploy old  |
| Rolling           | Replace instances one by one                          | Medium       | Continue rolling |
| Blue-Green        | New version in "green" env; switch traffic instantly  | Low          | Flip traffic back |
| Canary            | Route small % of traffic to new version; gradually increase | Very low | Reduce %    |
| Feature Flags     | Deploy code but enable features selectively           | Very low     | Toggle flag   |
| A/B Testing       | Different versions for different user segments        | Very low     | Toggle segment |

### 15.2 Containerization and Orchestration

**Docker:**
- Packages application + dependencies into a portable container image
- Images are immutable; containers are instances of images
- `Dockerfile` defines layers; smaller layers = faster builds

**Kubernetes (K8s):**

| Concept      | Description                                                |
|--------------|------------------------------------------------------------|
| Pod          | Smallest deployable unit; one or more containers           |
| Deployment   | Manages desired state (replicas, rolling updates)          |
| Service      | Stable network endpoint for a set of pods                  |
| Ingress      | HTTP routing rules from outside to services                |
| ConfigMap    | Non-sensitive configuration                                |
| Secret       | Sensitive configuration (passwords, keys)                  |
| HPA          | Horizontal Pod Autoscaler; scale pods based on CPU/memory  |
| Namespace    | Virtual cluster for resource isolation                     |

### 15.3 CI/CD Pipeline

```
Code Push → CI Pipeline:
  [Build] → [Unit Tests] → [Integration Tests] → [Security Scan] → [Container Build]
                                                                          ↓
CD Pipeline: [Push to Registry] → [Deploy to Staging] → [Smoke Tests] → [Deploy to Prod]
```

**Best Practices:**
- Each commit triggers the full pipeline
- Keep pipeline under 15 minutes
- Feature flags enable trunk-based development
- Automated rollback on failed health checks

### 15.4 Infrastructure as Code (IaC)

| Tool        | Type                 | Primary Use                              |
|-------------|----------------------|------------------------------------------|
| Terraform   | Declarative          | Multi-cloud infrastructure provisioning  |
| Pulumi      | Imperative (real code)| Multi-cloud; familiar programming languages |
| CloudFormation | AWS-native        | AWS-specific infrastructure              |
| Ansible     | Agentless config mgmt| Configuration, application deployment    |
| Helm        | K8s package manager  | Kubernetes application packaging         |

---

## 16. Storage Systems

### 16.1 Object Storage

- **Examples:** AWS S3, Google Cloud Storage, Azure Blob
- Store files as objects with metadata; not block or file system
- Infinitely scalable, cheap, durable (11 nines with multi-AZ)
- Access via HTTP/HTTPS; no mounting required
- **Use cases:** Images, videos, backups, data lake, static assets

### 16.2 Block Storage

- **Examples:** AWS EBS, Google Persistent Disk
- Raw storage volumes attached to VMs; OS sees them as disks
- Low latency, high IOPS; good for databases, boot volumes
- Tied to a single AZ

### 16.3 File Storage / NAS

- **Examples:** AWS EFS, Google Filestore, NFS
- Shared file system mounted by multiple instances
- Higher latency than block; good for shared config, code, ML model files

### 16.4 Data Warehouse vs Data Lake

| Aspect         | Data Warehouse                            | Data Lake                                  |
|----------------|-------------------------------------------|--------------------------------------------|
| Data format    | Structured, processed                     | Raw, structured + unstructured             |
| Schema         | Schema-on-write                           | Schema-on-read                             |
| Users          | Business analysts, SQL queries            | Data scientists, ML engineers              |
| Examples       | Snowflake, BigQuery, Redshift             | S3 + Glue, Delta Lake, Databricks          |

---

## 17. Back-of-Envelope Estimation

### 17.1 Key Numbers to Remember

| Quantity                    | Value                        |
|-----------------------------|------------------------------|
| Latency: L1 cache           | ~1 ns                        |
| Latency: Main memory (RAM)  | ~100 ns                      |
| Latency: SSD random read    | ~100 µs                      |
| Latency: HDD seek           | ~10 ms                       |
| Latency: Same datacenter    | ~0.5 ms                      |
| Latency: Cross-continent    | ~150 ms                      |
| Throughput: SSD             | ~500 MB/s read               |
| Throughput: Network (1 Gbps)| ~125 MB/s                    |
| 1 million seconds           | ~11.5 days                   |
| 1 billion                   | 10^9                         |
| 1 trillion                  | 10^12                        |

### 17.2 Estimation Framework

1. **Clarify scale:** DAU, requests per day, data size
2. **Convert to per-second:** requests/day ÷ 86,400 ≈ requests/sec
3. **Estimate storage:** records × bytes per record
4. **Estimate bandwidth:** requests/sec × bytes per request
5. **Identify bottlenecks:** CPU, memory, disk I/O, network

**Example — Twitter:**
```
300M users, 50M daily tweets
Tweets/sec = 50M / 86400 ≈ 580 writes/sec
Read QPS = 580 × 100 (read:write ratio) = 58K reads/sec
Storage = 50M tweets/day × 200 bytes = 10GB/day = ~3.6TB/year
Media (10% tweets have image) = 5M × 200KB = 1TB/day
```

---

## 18. System Design Interview Framework

### Step-by-Step Approach (45-minute interview)

| Time      | Step                                | What to Do                                             |
|-----------|-------------------------------------|--------------------------------------------------------|
| 0-5 min   | Clarify requirements                | Functional + non-functional; scale; constraints        |
| 5-10 min  | Back-of-envelope estimation         | DAU, QPS, storage, bandwidth                           |
| 10-15 min | High-level design                   | Draw main components, data flow                        |
| 15-30 min | Deep dive into components           | DB choice, caching strategy, API design                |
| 30-40 min | Address bottlenecks and scale       | Sharding, replication, CDN, queue                      |
| 40-45 min | Wrap up and discuss trade-offs      | What you'd do differently with more time               |

### Questions to Ask Before Designing

- How many users? DAU/MAU?
- Read-heavy or write-heavy?
- What's the acceptable latency?
- What's the required availability (99.9%? 99.99%)?
- Any geographic distribution? Multi-region?
- Budget constraints?
- Consistency vs availability preference?
- Any existing systems to integrate with?

### Common Mistakes to Avoid

| Mistake                          | Better Approach                                 |
|----------------------------------|-------------------------------------------------|
| Over-engineering from the start  | Start simple; add complexity when justified     |
| Ignoring non-functional requirements | Ask and address scale, latency, availability |
| Single database for everything   | Right DB for the right job                      |
| Not drawing anything             | Always start with a diagram                     |
| Deep-diving too early            | Confirm high-level before detailing             |
| Not discussing trade-offs        | Every decision has trade-offs; articulate them  |

---

## 19. Quick Reference — Technology Choices

| Need                           | Recommended Technology                           |
|--------------------------------|--------------------------------------------------|
| Relational data, ACID          | PostgreSQL, MySQL                                |
| High write throughput          | Cassandra, DynamoDB                              |
| In-memory cache                | Redis, Memcached                                 |
| Full-text search               | Elasticsearch, OpenSearch                        |
| Time series data               | InfluxDB, TimescaleDB, Prometheus                |
| Graph data                     | Neo4j, Amazon Neptune                            |
| Object storage                 | AWS S3, GCS                                      |
| Message queue (task queue)     | RabbitMQ, AWS SQS                                |
| Event streaming                | Apache Kafka, AWS Kinesis                        |
| Coordination / distributed lock| ZooKeeper, etcd                                  |
| Container orchestration        | Kubernetes                                       |
| Service mesh                   | Istio, Linkerd                                   |
| CDN                            | CloudFront, Cloudflare, Fastly                   |
| Real-time communication        | WebSocket, WebRTC, Socket.IO                     |
| API Gateway                    | Kong, AWS API Gateway, NGINX                     |
| Metrics + Alerting             | Prometheus + Grafana, Datadog                    |
| Distributed tracing            | Jaeger, Zipkin, AWS X-Ray                        |
| Log aggregation                | ELK Stack, Loki + Grafana                        |
