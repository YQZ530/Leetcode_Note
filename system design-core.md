
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



Absolutely! I’ve reformatted your notes in **clean Markdown**, clarified typos, and added small clarifications so it’s **interview-ready**.

---

# CAP Theorem

## Overview

* CAP theorem is used when **designing distributed systems**.
* It describes the **trade-offs of system behavior during network partitions**.
* **Important:** CAP only applies **when a network partition occurs**.

---

## CAP Components

| Letter                      | Meaning                                                            |
| --------------------------- | ------------------------------------------------------------------ |
| **C – Consistency**         | All nodes see the same data at the same time                       |
| **A – Availability**        | Every request receives a response, even if some nodes fail         |
| **P – Partition Tolerance** | System continues to operate despite network failures between nodes |

> **Note:** Network partitions are **unavoidable** in distributed systems. The main design choice is between **Consistency** and **Availability** during a partition.

---

## Consistency vs Availability

### 1. Consistency

* During a network partition, some nodes **may refuse requests** rather than return stale data.
* Guarantees **strong correctness**, but may reduce availability.

### 2. Availability

* All nodes **keep serving requests**, even during partition.
* Some nodes **may return stale or slightly outdated data**.

---

## Examples

* **Availability-focused systems**:

  * Social media feeds
  * Recommendation systems
  * Analytics dashboards
  * Users can tolerate slight data staleness (e.g., feed 1–2s old), but the service must remain up.

* **Consistency-focused systems**:

  * Banking systems
  * Inventory management
  * Booking systems (avoid overbooking)
  * Require **strong consistency** to prevent errors

> Mixed approach: In e-commerce, product descriptions and reviews → **eventual consistency**,
> but inventory counts and order processing → **strong consistency**.

---

## PACELC Theorem

* Extension of CAP theorem:
  **P**artition → choose **A**vailability or **C**onsistency
  **E**lse → choose **L**atency or **C**onsistency

* Implication:

  * Even with **no partitions**, strong consistency adds **latency** because nodes must coordinate before responding.

---

## Interview Tip

* When discussing **replication or distributed data**, be ready to answer consistency questions.
* Safe guideline:

  * **Eventual consistency** is fine for feeds, dashboards, recommendations.
  * **Strong consistency** is needed for money, inventory, or limited resources (booking systems).





Perfect — thanks for sharing! I’ll **reformat your notes in Markdown**, clean up typos, and clarify concepts so it’s **ready for system design interviews**.

---

# Database & Storage Systems

---

## 1. Relational Database (RDBMS)

### Key Features

* **SQL Joins**: Combine data across multiple tables.
* **Indexes**: Organize data for faster queries, typically implemented using **B-trees**.
* **Transactions**: Group multiple operations together as **atomic**, ensuring consistency.

---

## 2. NoSQL Databases

### Overview

* Designed for **flexible schemas** and **high scalability**.
* Types of NoSQL databases:

---

### 2.1 Key-Value Stores

* Concept: Data stored as **key → value pairs**.
* Extremely fast lookups.
* **Use Case:** Session management in web apps.

---

### 2.2 Document Databases

* Stores data in **documents** (often JSON-like), can contain nested structures.
* Each document may have **different schema**.
* **Use Case:** E-commerce product catalog

  * Books → author, publisher
  * Electronics → warranty, manufacturer
* **Why NoSQL:** Flexibility to store and query **nested fields**.

---

### 2.3 Column-Family Stores

* Stores data in **columns rather than rows**.
* Optimized for **fast aggregations over large datasets**.
* Examples: Cassandra, HBase

---

### 2.4 Graph Databases

* Focus on **relationships between data**.
* Nodes = entities, Edges = connections
* **Example:** Neo4j
* **Use Case:** Social networks, recommendation systems

  * Example scenario: “Suggest friends with at least 2 mutual connections”

---

## 3. Blob Storage

* For **large unstructured data** (images, videos)
* Examples: Amazon S3, Google Cloud Storage

### Usage

* Update a blob → get back a **URL** to download later
* **Do not use S3 as a primary database**

  * Typically, a **core database stores pointers/URLs** to the blob

### Key Features

| Feature         | Description                                                                  |
| --------------- | ---------------------------------------------------------------------------- |
| Durability      | Replication + erasure coding ensures data survives server failures           |
| Scalability     | Can store massive amounts of data                                            |
| Cost            | Pay per storage/requests                                                     |
| Security        | Data encryption at rest and in transit                                       |
| Upload/Download | Can upload/download directly from clients                                    |
| Chunking        | Supports large file uploads → resume interrupted uploads or parallel uploads |

---

## 4. API Gateway

* **Role in Microservice Architecture**:

  * Single entry point for clients
  * Routes requests to appropriate microservice
  * Handles:

    * Authentication & authorization
    * Rate limiting
    * Request aggregation / composition
    * Protocol translation (HTTP → gRPC, etc.)



