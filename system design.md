
communication protocol in networking
HTTP is like sending email, you wait, send and get reply; client initiates communication; communication request-driven; need to load header.

Real-time update
Server-Sent Event(SSE) -build on top of HTTP, the connection is keep open, server send update as they happen
for example, driver location updated to rider 
- rider no need to frequently send out message to get driver current location
- riderjust need initial message



websocket is **bidirectional** connection like phone call, both server and client can sent out data anytime

HTTPs
Can think of the old Upython plugin,  first start python server, waiting for response, then unity send a command, server open a thread to run that task, then return when complete; the connection has short timelimited;  request-driven

| Feature    | HTTP            | SSE             | WebSocket  |
| ---------- | --------------- | --------------- | ---------- |
| Direction  | Client → Server | Server → Client | Both ways  |
| Connection | Short-lived     | Long-lived      | Long-lived |
| Real-time  | ❌              | ✅ (one-way)   | ✅ (full)   |
| Complexity | Low             | Low             | Medium     |
| Overhead   | High (repeated) | Low             | Very low   |
