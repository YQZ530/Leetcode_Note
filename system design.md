
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

### ✅ Notes / Fixes Made

1. Clarified SSE vs WebSocket usage and directionality.
2. Corrected some grammar and spelling (e.g., “frequenctly” → “frequently”).
3. gRPC is **internal only**, not just “less friendly to web browsers”.
4. Added more precise analogy for TCP/IP layers.



