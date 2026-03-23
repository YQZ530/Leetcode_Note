

# Load Balancer — What to Know for Interviews

## 1. What is a Load Balancer?

* A load balancer distributes incoming requests across **multiple servers**.
* Goal:

  * Prevent overload on a single machine
  * Improve availability and scalability

👉 Key idea:

> Load balancer = enables **horizontal scaling**

---

## 2. When Should You Use It?

Use a load balancer when:

* You have **multiple servers handling the same type of request**
* Traffic is too large for a single machine
* You want:

  * High availability
  * Fault tolerance

### Example

* 1 server → 10K requests/sec → overloaded
* Add 5 servers + load balancer → each handles ~2K

---

## 3. What You Should Say in an Interview (Minimum)

If time is limited, just say:

> “We can horizontally scale this service and place a load balancer in front to distribute traffic across instances.”

---

## 4. Where to Put Load Balancer

### In Interviews (Best Practice)

* Put **ONE load balancer at the entry point**:

```
Client → Load Balancer → Web Servers → DB
```

* For deeper systems:

  * Mention services are **horizontally scaled**
  * Don’t draw load balancer everywhere unless asked

---

## 5. What You Should Actually Know (Core Concepts)

### 1. Basic Algorithms (just name them)

* Round Robin
* Least Connections
* Hash-based (e.g., user ID)

---

### 2. Health Checks

* Load balancer detects unhealthy servers
* Stops sending traffic to them
---

### 3. Stateless vs Stateful (IMPORTANT)

* Stateless services → easy to load balance ✅
* Stateful services → harder (need **sticky sessions** or shared state)


## 6. What NOT to Overdo

❌ Don’t:

* Draw load balancer in front of every microservice
* Explain low-level networking details
* Overcomplicate


---

## 7. Layer 4 Vs Layer 7(Advanced) 

You can mention:
* Layer 4 (TCP) vs Layer 7 (HTTP) load balancing
* Global vs regional load balancing
* Integration with CDN


> A load balancer distributes incoming requests across multiple servers to enable horizontal scaling and improve availability. I would use it whenever I have multiple instances of a service handling the same requests. In my design, I would place a load balancer at the entry point and assume services are horizontally scaled behind it.


Great — this is a **very important concept**, especially when talking about load balancing and WebSocket.

---

# Sticky Session (Session Affinity)

## Definition (backend routing decision.)

* A **sticky session** means:

  > Requests from the same user are always routed to the **same server**.

## Why Do We Need It?

Normally, a load balancer distributes requests like this:

```text
User → Load Balancer → Server A / Server B / Server C (random)
```

But sometimes, the server stores **session state locally**, such as:
* Login session
* Shopping cart
* Temporary user data

 If requests go to different servers:

* User may lose session
* Or need to re-authenticate

Load balancer with sticky session:
User → Load Balancer → Server A (always)
* Load balancer “remembers”:

  * User → Server mapping
* Future requests from that user go to the same server


## How It Works

1. **Cookie-based**

   * Load balancer sets a cookie like:
     ```
     SERVER_ID = A
     ```
   * Future requests routed to Server A

2. **IP Hash**

   * Hash(user IP) → always maps to same server


## Downsides (VERY IMPORTANT)

Sticky sessions hurt scalability:

* ❌ Uneven load (some servers get more users)
* ❌ Hard to rebalance traffic
* ❌ If server crashes → session lost
* ❌ Not truly stateless


## When Sticky Session Is Still Used

* WebSocket connections (must stay on same server)
* Legacy systems/Temporary/simple setups

## Better Alternative (Preferred in System Design)

👉 **Make services stateless**

* Store session in shared storage:

  * Database
  * Redis (most common)

```text
User → Load Balancer → Any Server → Redis (shared session)
```

✅ Benefits:

* Any server can handle request
* Easier horizontal scaling
* No need for sticky sessions

---


异步（Asynchronous）

异步 = 发出去后不用等对方完成就继续做自己的事情


---

Queue
- serve as buffer for busty traffic , distributing work across a system


Great notes — this is already strong. I’ll **clean it up into structured Markdown**, fix wording, and add a few **important clarifications for interviews**.

---

# Queue (Message Queue)

## What is a Queue?

* A queue is a system that **buffers messages between producers and consumers**.
* Flow:
  ```
  Producer → Queue → Workers (Consumers)
  ```
* Producers send messages and **don’t wait for processing**.
* Workers process messages **asynchronously at their own pace**.
---

## Why Use a Queue?

### 1. Buffer for Bursty Traffic

* Smooths out spikes in traffic.
* Example:

  * System can handle **200 req/sec**
  * Spike = **1000 requests**
  * → 800 requests wait in queue instead of being dropped

---

### 2. Decoupling (VERY IMPORTANT)

* Producer and consumer are **independent**
* You can:
  * Scale them separately
  * Deploy/restart consumers without affecting producers

---

### 3. Asynchronous Processing

* Useful for **non-urgent work**
* Improves user experience (fast response)

## When NOT to Use a Queue

* ❌ **Synchronous / low-latency systems**

  * Example: API requiring < 500ms response
* Why?

  * Queue introduces **waiting time**
  * Breaks latency guarantees

---

## Common Use Cases

### 1. Ride-Sharing (Bursty Traffic)

* Ride requests spike during peak hours
* Queue buffers requests → system processes steadily

### 2. Background Jobs (Work Distribution)

* Example: Photo processing system

  * User uploads image → task goes to queue
  * Workers process in parallel

---

## Key Concepts

### 1. Message Ordering

* Most queues: **FIFO (First In, First Out)**
* Some systems (like Apache Kafka):

  * Ordering is guaranteed **within a partition**, not globally

### 2. Retry Mechanism

* Failed messages are retried automatically
* Configurable:

  * Retry count
  * Delay between retries

### 3. Dead Letter Queue (DLQ)

* Stores messages that **fail repeatedly**
* Useful for:

  * Debugging
  * Auditing failures

### 4. Scaling with Partitions

* Queue can be split into **multiple partitions**
* Each partition processed by different workers
* Requires a **partition key** to keep related messages together

### 5. Backpressure (VERY IMPORTANT)

* Problem:

  * System handles 200 req/sec but receives 300 req/sec
  * Queue keeps growing → system overload

* Solution:

  * **Slow down producers**
  * Reject requests when queue is full


---

## Common Queue Technologies

* Apache Kafka

  * Distributed, high-throughput, supports streaming

* Amazon SQS

  * Fully managed queue service

---

# ⚠️ Important Clarifications / Fixes

### 1. Queue ≠ Always FIFO

* FIFO is common, but:

  * Kafka → ordered per partition only

---

### 2. Queue Does NOT Increase Capacity

* It only **buffers load**
* If incoming rate > processing rate:

  * Queue grows indefinitely

---

### 3. Latency Trade-off

* Queue improves **throughput & reliability**
* But increases **latency**

---

# 🔥 Interview Answer (Perfect)

> A queue is used to decouple producers and consumers and to buffer bursty traffic. It allows systems to process requests asynchronously and scale independently. However, it introduces latency, so it’s best used for background or non-critical tasks rather than synchronous low-latency operations.

---

# ✅ Key Takeaways

* Queue = **buffer + decoupling + async processing**
* Use for:

  * Bursty traffic
  * Background jobs
* Avoid for:

  * Low-latency synchronous systems
* Watch out for:

  * Backpressure
  * Ordering limitations

---






---
Queue vs Load Balancer

The core difference comes down to **Time (Synchronous vs. Asynchronous)**. 

### 1. Load Balancer (Distribute it NOW)
A load balancer distributes **real-time, synchronous** traffic across multiple servers. 
* **The Client Expectation:** The user is actively waiting on the other end of the screen for a response (e.g., loading a webpage).
* **How it works:** It acts like a traffic cop. A request comes in, the load balancer checks which server has the least amount of work, and routes the request there instantly. 
* **Analogy:** A receptionist at a busy clinic. They point you to Doctor A or Doctor B so no one doctor gets all the patients, but you still see the doctor *right now*.

### 2. Message Queue (Buffer it for LATER)
A queue stores **background, asynchronous** tasks until a server is ready to process them. 
* **The Client Expectation:** The user *does not* wait for the result. They click a button, get a "Success! We are processing this" message, and walk away.
* **How it works:** It acts as a shock absorber. If 10,000 tasks come in at once, they sit safely in the queue. Your backend servers (workers) pull tasks from the queue one by one at their own maximum speed without crashing. 
* **Analogy:** A restaurant ticket rail. The waiter drops the order ticket on the rail and walks away. The chefs pull tickets off the rail one at a time as they finish cooking the previous meal. 

---

### The TL;DR for an Interview
> "A **Load Balancer** distributes live traffic horizontally across multiple servers to ensure a fast, synchronous response. A **Message Queue** decouples services by buffering asynchronous tasks, protecting the database or backend from being overwhelmed by sudden spikes in heavy processing."




---

### Distributed Event Streams


### The Core Analogy: The Tape Recorder vs. The Mailbox
* **A Message Queue (RabbitMQ / SQS):** is like a **Mailbox**. A message arrives, a worker takes it out, processes it, and the message is deleted forever. It is designed for *temporary* task buffering.
* **A Stream (Kafka / Kinesis):** is like a **Tape Recorder** or a **Financial Ledger**. Events are appended to the end of the log. When a worker reads an event, *the event is not deleted*. It stays on the tape. Because it stays there, other workers can read it, or you can "rewind the tape" and read it again. 

Here is how to intuitively understand the advanced concepts from your notes:

### 1. Event Sourcing (Rewinding the Tape)
In a traditional database, you store the **current state**. If someone buys a stock, you update their balance from $1,000 to $500. The old number is gone. 
**Event Sourcing** means you don't just save the final state; you save every single action as an immutable event on the stream: `[Deposited $1000] -> [Bought AAPL for $500]`. 
* **Why interviewers care:** If your database ever crashes and corrupts, you can literally "replay" the stream from day one to perfectly reconstruct the user's account balance. It is the ultimate audit trail.

### 2. Multiple Consumers (The Radio Broadcast)
In a standard queue, if the "Email Service" grabs a user registration event, the "Analytics Service" can no longer see it. It’s gone.
Because Streams don't delete data after it's read, they act like a **Radio Broadcast**. 
* **Real-world example:** In a stock trading application, when a new stock price arrives, it is published to the stream. The `LiveChartService` can read it to update the UI, the `DatabaseArchiver` can read it to save it to PostgreSQL, and the `AlertService` can read it to send push notifications—all at the exact same time, completely independently.

### 3. Partitioning (Multi-Lane Highways)
If you are ingesting 1 million stock market ticks per second, a single stream (one server) will crash. 
**Partitioning** splits the stream into a multi-lane highway. 
* **The Partition Key:** You assign a "key" to ensure related data stays in the same lane. For example, you use the stock ticker symbol (e.g., "TSLA") as the key. This guarantees that all Apple stock updates go to Partition A, and all Tesla updates go to Partition B. This allows you to scale horizontally across dozens of servers while keeping the timeline of events perfectly in order for each specific stock.
* Each partition can be consumed independently → horizontal scaling
* Partition key ensures related events stay in the same partition

### 4. Windowing (Slicing Time)
Streams are infinite; they never stop. But analytics require boundaries. **Windowing** is simply slicing that infinite stream into manageable buckets of time.
* Group events by time or count for batch-like processing
* **Real-world example:** If you are processing live chat messages, you don't want to process them one by one. You use a "10-second window" to group all messages that arrived in the last 10 seconds, calculate the "trending keywords" for that specific slice of time, and then send that summary to the frontend.

