

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




# Search-Optimized Databases

## Concept

* Specialized databases designed to handle **full-text search** efficiently.

* Standard relational queries are **not optimized** for full-text search.

* Techniques used to speed up searches:

  1. **Indexing**
  2. **Tokenization**
  3. **Stemming**

* Functionally, they often build an **inverted index** to quickly find documents containing given words.

---

## Key Concepts

| Concept            | Description                                                                                             |
| ------------------ | ------------------------------------------------------------------------------------------------------- |
| **Inverted Index** | Maps **words → documents** where they appear. Enables fast lookup.                                      |
| **Tokenization**   | Break text into words or terms for indexing.                                                            |
| **Stemming**       | Reduce words to their **root form** (e.g., “running” → “run”) to match queries.                         |
| **Fuzzy Search**   | Tolerates **misspellings or variations**. Uses techniques like **edit distance** to find similar words. |


The pipeline works in a few steps. When an article is ingested, the text engine tokenizes the document into individual words and uses stemming to reduce them to their root forms (so 'investing' and 'invests' both become 'invest'). It stores this in the inverted index.

At query time, if a user types 'invsting', the database doesn't just fail. It applies fuzzy search algorithms—specifically calculating the Levenshtein edit distance—to realize 'invsting' is only one character away from the root word 'invest'. It then instantly retrieves the mapped documents, completely bypassing the need for a table scan."

---

## Scaling

* Scale by **adding nodes to a cluster**.
* Shard data across nodes for **horizontal scaling**.
* Often used in **distributed search engines** like:

  * Elasticsearch
  * Apache Solr
