
# Sharding

## Definition

- splitting data across **multiple independent database servers (shards)**.
- Used when a **single database can no longer handle the load** (especially writes or data size).

---

## When You Don’t Need Sharding Yet

- Modern databases like PostgreSQL or MySQL can handle:
  * **10K–50K+ writes/sec**
  * Very large datasets (hundreds of GB to TB)

### Example

* **100K reads/sec + 10K writes/sec**

* Use **read replicas**
* Primary handles writes, replicas handle reads
👉 No need for sharding yet.

---

## Scaling Strategy (Correct Order)

1. Optimize queries (indexes, caching)
2. Vertical scaling (bigger machine)
3. Add read replicas
4. Partition tables (within one DB)
5.  Sharding (last resort)

---------------

# Consistent Hashing

## What Problem It Solves

* Distributes data evenly across servers
* Minimizes data movement when:

  * Adding a server
  * Removing a server

General usage:
 distrubte cached like redis use it to distribute keys across cache node ; distributed databased used it for sharding; some load balancer use it assign request backend server. CDN use it to route request to edge server.
In interview: 
1. distributed cache topic, we use consistent hashing to distribute data across cache node;  
2. database sharding - we'll use consistent hashing for the shard key

---

## How It Works

* Think of a **hash ring (circular space)**:

  * Both **servers and keys** are hashed onto the ring
* Each key is assigned to:

  * The **next server clockwise**

---

## Behavior

### Adding a Server

* Only keys between:

  * **new server → previous server**
* Need to move

✅ Minimal data reshuffling

---

### Removing a Server

* Its keys move to:

  * The **next server clockwise**

---

## Why It’s Useful

* Avoids massive rehashing (unlike simple hash mod N)
* Common in:

  * Distributed caches (e.g., Redis clusters)
  * Sharded systems

---
### 4. Important Missing Insight (VERY IMPORTANT)

👉 Consistent hashing is:

* **Mostly used for caching and distributed systems**
* **Not always used directly for database sharding**

In DB sharding, more common strategies:

* Range-based sharding (user_id 1–1M, 1M–2M)
* Hash-based sharding (hash(user_id) % N)

 When Consistent Hashing *Might* Be Used in Databases

* When:
  * Nodes frequently join/leave
  * You want **minimal data movement**
* But:
  * Adds complexity
  * Makes querying harder



# Interview Insight 


> We use consistent hashing to distribute keys across cache nodes.
> When a node is added or removed, only a small portion of keys need to be remapped.

>  For database sharding, I would typically use hash-based or range-based partitioning. Consistent hashing is an option if we expect frequent node changes, but it adds complexity and is less common for relational databases.



---
s
# Consistent Hashing

## General Usage

Consistent hashing is used to **distribute load/data across nodes with minimal reshuffling** when nodes change.

### Common Use Cases

* **Distributed Cache**

  * Systems like Redis clusters use it to distribute keys across cache nodes.

* **Load Balancing**

  * Some load balancers use it to map requests (e.g., by user ID) to same backend servers for better session stickiness or better cache locality.


* **CDN Routing**

  * CDNs may use hashing strategies to route users to specific edge servers (though geo-based routing is more common).

* **Distributed Systems (General)**

  * Any system where nodes frequently join/leave and you want minimal data movement.




# Networking & Real-Time Communication

## OSI Layers (Important for System Design)

* OSI has **7 layers**, but we focus on **Application, Transport, and Network layers**:

| Layer       | Role                          | Examples             |
| ----------- | ----------------------------- | -------------------- |
| Application | Defines protocols             | HTTP, WebSocket, SSE |
| Transport   | Handles delivery, reliability | TCP, UDP             |
| Network     | Routing and addressing        | IP                   |

* **HTTP** operates on the **Application layer**
* **TCP** operates on the **Transport layer**

---

## Protocols for Real-Time Communication

### 1. HTTP

* Request-driven, **client-initiated**
* Short-lived, stateless
* High overhead (headers + repeated requests)
* Good for normal request-response operations

---

### 2. Server-Sent Events (SSE)

* One-way server → client streaming
* Built on top of HTTP
* Long-lived connection
* Ideal for **real-time updates where client does NOT send frequent data**

  * Example: Rider app receiving driver location updates

---

### 3. WebSocket

* **Bidirectional communication** (server ↔ client)
* Persistent connection
* Each client = one open connection
* Scaling challenge:

  * Hard to manage millions of concurrent connections
  * Requires **connection-aware load balancing**, sticky sessions, or shared state
* Best for **frequent two-way communication**, e.g., live GPS updates from drivers

---

# Data Modeling

## 1. Relational vs NoSQL

### NoSQL (e.g., MongoDB)

* Flexible schema → good if **data structure changes frequently**
* Often used for semi-structured or unstructured data

### Relational SQL

#### Normalization

* Split data across tables to avoid duplication

  * Example: `user`, `order`, `product` tables
* Pros:

  * Data consistency → update once, reflected everywhere
* Cons:

  * Joins can be expensive for multi-table queries
  * Can slow reads if tables are large

#### Denormalization

* Duplicate data to **avoid joins**, optimize reads

  * Example: Store `user_name` in `orders` table
* Pros:

  * Faster reads
* Cons:

  * Updates require **propagating changes** across tables

**System Design Tip:**

* Start with normalized relational model
* Denormalize selectively for **hot paths** or read-heavy queries

---

## 2. Indexing

* Index your database to **speed up queries**
* Trade-off: indexes **increase write cost and storage**, but dramatically improve read performance

---

## 3. Caching

### Purpose

* Store frequently accessed data in **fast memory**
* Reduces database latency

  * Redis cache hit: ~1ms
  * Database query: ~20–50ms
  * → 20–50× faster

### Strategy: Cache-Aside

1. Check cache first
2. If miss → query DB
3. Store result in cache with TTL

### CDN Caching

* Different from DB caching
* Stores **static assets** (images, video)
* Serves from edge servers close to users
* Reduces latency and bandwidth usage

---

# ✅ Key Corrections & Clarifications

1. **Typos / Grammar**

   * “overload head” → **high overhead**
   * “frequecely” → **frequently**
   * “long-live connection” → **long-lived connection**
   * “UCP” → **UDP**

2. **Concept Clarifications**

   * WebSocket scaling requires sticky sessions or shared state **because of persistent connections**, not just “hard to manage”
   * Start **normalized model first**, then denormalize **only for performance hot paths**
   * CDN caching is **not dynamic DB caching** — only for static assets

3. **Additional Notes**

   * Cache-aside with TTL is **most common pattern**, but also know **write-through / write-back** for completeness
   * Indexing is **critical for normalized tables** with many joins





