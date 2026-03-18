
communication protocol in networking
HTTP is like sending email, you wait, send and get reply; client initiates communication; communication request-driven; need to load header.

Real-time update
Server-Sent Event(SSE) -build on top of HTTP, the connection is keep open, server send update as they happen
for example, driver location updated to rider 
- rider no need to frequently send out message to get driver current location
- riderjust need initial message
  
Rider wants updates like:
“Driver accepted your ride”
“Driver is arriving”
“Ride started”
Server pushes these events to the client.


websocket is **bidirectional** connection like phone call, both server and client can sent out data anytime

Example: driver(client) send to driver app(server)
GPS coordinates every second
Status updates (“Arrived”, “Picked up”, etc.)
better use websocket because driver(clients) frequenctly send out loc to server



HTTPs
Can think of the old Upython plugin,  first start python server, waiting for response, then unity send a command, server open a thread to run that task, then return when complete; the connection has short timelimited;  request-driven

| Feature    | HTTP            | SSE             | WebSocket  |
| ---------- | --------------- | --------------- | ---------- |
| Direction  | Client → Server | Server → Client | Both ways  |
| Connection | Short-lived     | Long-lived      | Long-lived |
| Real-time  | ❌              | ✅ (one-way)   | ✅ (full) | 
| Complexity | Low             | Low             | Medium     |
| Overhead   | High (repeated) | Low             | Very low   |




gRPC (Remote Procedure Call) framekwork for *internal* service-to-service communication; use binary serialization and HTTP/2
significantly faster than JSON over HTTP; but binary serialization make less friendly to web browswer;  not use for public facing API, used for internal micro services
COmmon practice is REST for external  API and gRPC
Example:
Uber-like System (Internal Microservices)

Ride Service → Driver Service
Ride Service calls Driver Service to find nearby drivers
-Uses gRPC for fast, reliable communication
-Internal only — not exposed to rider app directly




Backround infor supplement:


1.Application Layer (HTTP)
You type a URL or click a button → your app generates a request
Adds headers, method (GET/POST), etc.

2.Presentation Layer

Encodes the data (UTF-8, JSON, Protobuf)
Maybe compresses or encrypts it (TLS)

3.Session Layer

Keeps track of which session/connection this request belongs to (cookies, session IDs)

4.Transport Layer (TCP)

Splits the message into packets

Adds sequence numbers for ordering and checksum for reliability

5.Network Layer (IP)

Adds source/destination IP addresses
Decides how to route the packet through the network

6.Data Link Layer
Converts packets into frames for your network interface (Ethernet/Wi-Fi)
Adds MAC addresses for local delivery

7.Physical Layer
Sends the actual bits over wires, fiber, or radio'

Think of analgy of writing letter:
1. write content (application)- > translate in right langueage(presentation)-> put date for reference(session) -> put in multiple envelopes for realiablity( Transport)-> put desitantion address (Network to lookup)-> hand to post office(data link
   -> the post office move it(physical)

