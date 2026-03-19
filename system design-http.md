
# Communication Protocols in Networking

## 1. HTTP

* Analogy: Like sending an email — you send a message and wait for a reply.
* **Client-initiated** communication.
* Request-driven: client sends a request, server responds.
* Requires sending headers with each request → overhead can be high.
* Connection is short-lived.

Example:
Old Python plugin workflow:

1. Start Python server.
2. Unity sends a command → server opens a thread, executes task, and returns result.

---

## 2. Real-Time Updates

### Server-Sent Events (SSE)

* Built on top of HTTP.
* **Long-lived connection**: server pushes updates as they happen.
* **One-way**: server → client.
* Example: Rider app receives updates without polling:

  * “Driver accepted your ride”
  * “Driver is arriving”
  * “Ride started”
* **Use case**: client mostly listens for updates (e.g., rider tracking driver location).

### WebSocket

* **Bidirectional** connection — both server and client can send data anytime.
* Example: Driver app sends GPS coordinates to server every second.
* Better than HTTP when clients frequently send data.
* **Use case**: high-frequency updates from clients, like driver location.

---

## 3. Comparison Table

| Feature    | HTTP                    | SSE             | WebSocket  |
| ---------- | ----------------------- | --------------- | ---------- |
| Direction  | Client → Server         | Server → Client | Both ways  |
| Connection | Short-lived             | Long-lived      | Long-lived |
| Real-time  | ❌                       | ✅ (one-way)     | ✅ (full)   |
| Complexity | Low                     | Low             | Medium     |
| Overhead   | High (repeated headers) | Low             | Very low   |

---

## 4. gRPC

* Framework for **internal service-to-service communication**.
* Uses **binary serialization** and HTTP/2 → faster than JSON over HTTP.
* Not browser-friendly → not used for public-facing APIs.
* Common practice: REST for external APIs, gRPC for internal microservices.

### Example: Uber-like System (Internal Microservices)

* Ride Service → Driver Service: calls Driver Service to find nearby drivers.
* gRPC ensures **fast and reliable internal communication**.



# Choosing Between WebSocket, SSE, or HTTP (Long Polling) for Real-Time Problems

## Key Consideration

* **WebSocket**: Each connection is **stateful**, which makes horizontal scaling challenging when handling millions of concurrent connections.
* Choose the protocol based on **directionality of updates** and **frequency of client-server messages**.

---
## Example: Updating Driver Location in Rider App

### 1. Less Thoughtful Solution

* Use WebSocket for **everything**:

  * Bidirectional connection: driver ↔ rider app.
  * Can send driver updates back and forth.
* ❌ Problem: Introduces complexity with **millions of open connections**, hard to scale horizontally.

### 2. Thoughtful Solution

#### Server-Sent Events (SSE)

* **One-way server → client** updates.
* Rider only **receives driver location**, no need to push messages back.
* ✅ Simpler and sufficient for many real-time notifications.

#### HTTP with Long Polling

* Client repeatedly sends requests to check for updates.
* Useful if updates are **infrequent** or if **SSE/WebSocket is not feasible**.
* ❌ Higher overhead than SSE, but easier to implement in simple setups.




---

## 5. Networking Layers (TCP/IP Model + OSI Analogy)

| Layer           | Function                                                        | Analogy                                                 |
| --------------- | --------------------------------------------------------------- | ------------------------------------------------------- |
| Application     | Your app generates a request (GET/POST, headers)                | Writing the content of a letter                         |
| Presentation    | Encodes data (UTF-8, JSON, Protobuf), compresses/encrypts (TLS) | Translating letter to proper language                   |
| Session         | Tracks session/connection (cookies, session IDs)                | Adding date/reference for context                       |
| Transport (TCP) | Splits message into packets, adds sequence numbers & checksums  | Putting content into multiple envelopes for reliability |
| Network (IP)    | Adds source/destination IP, routes packet                       | Writing destination address                             |
| Data Link       | Converts packets to frames, adds MAC addresses                  | Handing envelope to local post office                   |
| Physical        | Sends bits over wires/fiber/radio                               | Post office moves the mail physically                   |

---



