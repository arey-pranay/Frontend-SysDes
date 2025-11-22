## Foundational Concepts

The methods for delivering updates fall into two main strategies:

| Strategy | Description | Initiator | Techniques |
| :--- | :--- | :--- | :--- |
| **Pull** | The **client** requests updates from the server periodically. | Client | Short Polling, Long Polling |
| **Push** | The **server** sends messages to the client. | Server | Server-Sent Events, WebSockets |

**Comet** is an umbrella term for techniques that establish a long-held HTTP connection to **push** messages from the server to the client, primarily including Long Polling and Server-Sent Events [[02:02](http://www.youtube.com/watch?v=8Uyka3fzXek&t=122)]. 
***

## 1. Short Polling

**Short Polling** is the simplest technique where the client periodically asks the server for updates at a static interval (e.g., every 5 seconds) [[05:59](http://www.youtube.com/watch?v=8Uyka3fzXek&t=359)].

| Advantages | Disadvantages |
| :--- | :--- |
| **Simplicity:** No persistent connection needed; operates over standard HTTP [[06:06](http://www.youtube.com/watch?v=8Uyka3fzXek&t=366)]. | **Inefficiency:** Majority of requests may be useless if updates are rare or random [[07:07](http://www.youtube.com/watch?v=8Uyka3fzXek&t=427)]. |
| **Easy Scaling:** Scaling is straightforward (like standard HTTP) [[10:26](http://www.youtube.com/watch?v=8Uyka3fzXek&t=626)]. | **Latency:** Updates that occur between intervals are missed [[06:53](http://www.youtube.com/watch?v=8Uyka3fzXek&t=413)]. |

**Use Cases:**
* When updates are required **periodically** (e.g., every 5 minutes), not in real-time [[08:08](http://www.youtube.com/watch?v=8Uyka3fzXek&t=488)].
* When there is **no access to server code** (e.g., third-party or deprecated backend) [[07:40](http://www.youtube.com/watch?v=8Uyka3fzXek&t=460)].

***

## 2. Long Polling

**Long Polling** is a technique where the server holds the client's HTTP request open until new data is available. Once data is sent, the connection closes, and the client immediately re-establishes a new request [[12:06](http://www.youtube.com/watch?v=8Uyka3fzXek&t=726)].

| Advantages | Disadvantages |
| :--- | :--- |
| **Efficiency:** Solves the "useless request" problem of Short Polling [[12:21](http://www.youtube.com/watch?v=8Uyka3fzXek&t=741)]. | **Persistent Connection:** Requires additional backend work and can be interrupted by proxies/firewalls [[12:45](http://www.youtube.com/watch?v=8Uyka3fzXek&t=765)]. |
| **Broadest Browser Support:** Operates over standard HTTP (simple XML HTTP Request) [[14:47](http://www.youtube.com/watch?v=8Uyka3fzXek&t=887)]. | **Not True Real-Time:** Potential delay when updates occur during the necessary reconnection process [[17:00](http://www.youtube.com/watch?v=8Uyka3fzXek&t=1020)]. |

### Scaling Challenges with Long Polling

Scaling becomes challenging when a stateful architecture is used (data is stored in the server's memory, not a shared database) [[18:16](http://www.youtube.com/watch?v=8Uyka3fzXek&t=1096)]. To ensure a client's subsequent request goes to the correct server, two main solutions exist:

1.  **Sticky Sessions:** A technique where the **load balancer** is forced to redirect a client's requests to the same server (based on client ID) every time [[19:28](http://www.youtube.com/watch?v=8Uyka3fzXek&t=1168)].
    * **Drawback:** Can lead to **uneven load distribution** and **data loss** if the server goes down [[20:58](http://www.youtube.com/watch?v=8Uyka3fzXek&t=1258)]. 2.  **Shared Storage:** Storing data (e.g., upload progress) in a shared database or cache. This makes the servers **stateless**, offering better reliability and simpler load balancing [[19:58](http://www.youtube.com/watch?v=8Uyka3fzXek&t=1198)].

***

## 3. Server-Sent Events (SSE)

**SSE** is a dedicated **push technology** that establishes a true **persistent, one-directional** connection over standard HTTP/1.1 or HTTP/2 [[22:28](http://www.youtube.com/watch?v=8Uyka3fzXek&t=1348)].

| Advantages | Disadvantages |
| :--- | :--- |
| **True Persistent Connection:** Minimizes latency as no reconnection is needed [[22:50](http://www.youtube.com/watch?v=8Uyka3fzXek&t=1370)]. | **One-Directional:** Not suitable for bidirectional needs (e.g., chat input) [[25:12](http://www.youtube.com/watch?v=8Uyka3fzXek&t=1512)]. |
| **Low Overhead:** Lower HTTP overhead than polling (less bytes per message) [[23:14](http://www.youtube.com/watch?v=8Uyka3fzXek&t=1394)]. | **Connection Limit:** Limited to six concurrent connections per browser before HTTP/2 [[24:18](http://www.youtube.com/watch?v=8Uyka3fzXek&t=1458)]. |
| **Built-in Features:** Automatic reconnection, event IDs, and event naming [[23:22](http://www.youtube.com/watch?v=8Uyka3fzXek&t=1402)]. | **Authentication:** Does not support the `Authorization` header [[36:09](http://www.youtube.com/watch?v=8Uyka3fzXek&t=2169)]. |
| **Efficiency:** Supports **connectionless pushes** to save battery life on mobile devices [[23:36](http://www.youtube.com/watch?v=8Uyka3fzXek&t=1416)]. | **Traffic:** Less efficient than WebSockets [[24:08](http://www.youtube.com/watch?v=8Uyka3fzXek&t=1448)]. |

**Use Cases:**
* Real-time notifications (news/social feeds) [[25:57](http://www.youtube.com/watch?v=8Uyka3fzXek&t=1557)].
* Live updates for data like cryptocurrency prices or sports scores [[26:03](http://www.youtube.com/watch?v=8Uyka3fzXek&t=1563)].
* Progress updates for long-running processes [[26:12](http://www.youtube.com/watch?v=8Uyka3fzXek&t=1572)].

***

## 4. WebSockets

**WebSockets** provide a true **bidirectional, full-duplex** channel that runs on its own protocol (**WS/WSS**), initiated through an HTTP handshake [[26:28](http://www.youtube.com/watch?v=8Uyka3fzXek&t=1588)].

| Advantages | Disadvantages |
| :--- | :--- |
| **Bidirectional Channel:** Native support for two-way communication [[26:47](http://www.youtube.com/watch?v=8Uyka3fzXek&t=1607)]. | **Complexity:** More complex to implement and maintain [[27:27](http://www.youtube.com/watch?v=8Uyka3fzXek&t=1647)]. |
| **Maximum Efficiency:** **Minimal latency** and **lowest message overhead** (not being HTTP-based) [[26:56](http://www.youtube.com/watch?v=8Uyka3fzXek&t=1616)]. | **Protocol Issues:** The separate protocol (**WS/WSS**) can be blocked by proxies/firewalls not configured to handle it [[27:37](http://www.youtube.com/watch?v=8Uyka3fzXek&t=1657)]. |
| **Binary Data:** Supports efficient transfer of binary data (e.g., media files) [[27:10](http://www.youtube.com/watch?v=8Uyka3fzXek&t=1630)]. | **No HTTP/2 Features:** Lacks core HTTP/2 benefits like **multiplexing** [[32:36](http://www.youtube.com/watch?v=8Uyka3fzXek&t=1956)]. |

**Use Cases:**
* **Collaborative tools** (Figma, Google Docs) [[29:27](http://www.youtube.com/watch?v=8Uyka3fzXek&t=1767)].
* **Multiplayer games** [[29:32](http://www.youtube.com/watch?v=8Uyka3fzXek&t=1772)].
* **Chat applications**—any scenario where ultra-low latency and active client-to-server messaging are required [[29:56](http://www.youtube.com/watch?v=8Uyka3fzXek&t=1796)].

### WebSockets' Core Problems

1.  **Multiplexing and TCP Connections:** Since WebSockets don't use HTTP/2's multiplexing, each new connection (e.g., for different chat groups or different browser tabs) requires its own independent **TCP connection** [[32:55](http://www.youtube.com/watch?v=8Uyka3fzXek&t=1975)]. This can lead to inefficiencies.
2.  **Authentication:** Like SSE, the WebSocket API does not allow setting the standard `Authorization` header [[35:54](http://www.youtube.com/watch?v=8Uyka3fzXek&t=2154)]. Workarounds include [[36:14](http://www.youtube.com/watch?v=8Uyka3fzXek&t=2174)]:
    * **Query Parameters:** Sending the token in the URL (less secure).
    * **Cookies:** The simplest method, as the initial connection uses an HTTP handshake (requires the app and WebSocket server to be on the same domain) [[38:35](http://www.youtube.com/watch?v=8Uyka3fzXek&t=2315)].
    * **First Message:** Sending the token in the very first WebSocket message (vulnerable to DoS attacks if invalid tokens open the connection first) [[38:04](http://www.youtube.com/watch?v=8Uyka3fzXek&t=2284)].

***

## Summary of Real-Time Update Techniques

| Technique | Use Cases | Advantages | Disadvantages |
| :--- | :--- | :--- | :--- |
| **Short Polling** [[40:26](http://www.youtube.com/watch?v=8Uyka3fzXek&t=2426)] | Updates at fixed intervals (e.g., every 30 seconds), non-real-time requirements. | Simplicity, easy scaling. | Inefficient network traffic, no real-time updates. |
| **Long Polling** [[40:50](http://www.youtube.com/watch?v=8Uyka3fzXek&t=2450)] | Near real-time updates, maximum browser support, infrequent updates. | Operates over HTTP (benefits from HTTP/2 features), broad browser support. | Potential delays between updates (due to reconnection), HTTP overhead. |
| **Server-Sent Events** [[41:14](http://www.youtube.com/watch?v=8Uyka3fzXek&t=2474)] | Real-time updates with one-directional communication, frequent server messages. | True persistent connection, built-in features (reconnection, event IDs), connectionless pushes. | HTTP overhead, not suitable for bidirectional needs. |
| **WebSockets** [[41:31](http://www.youtube.com/watch?v=8Uyka3fzXek&t=2491)] | Bidirectional communication, collaborative tools, multiplayer games, minimal delay required. | Highly efficient network traffic, minimal delay, supports binary data. | Complex to implement, non-HTTP protocol issues, lacks HTTP/2 multiplexing. |
