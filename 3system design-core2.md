

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



