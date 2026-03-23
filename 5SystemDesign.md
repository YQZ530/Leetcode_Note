

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
