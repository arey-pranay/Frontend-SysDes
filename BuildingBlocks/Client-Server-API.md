## 1. Client-Server Communication Fundamentals
The series begins by classifying communication based on the API consumer:

* **Client-to-Server Communication:** Involves a client (browser, mobile app) and a remote server. Since human users interact with the client, **usability and convenience** of the API are sometimes prioritized over performance.
* **Service-to-Service Communication:** Communication between backend services. This typically prioritizes **high speed, low memory, and low traffic consumption**.

APIs are also categorized as **private/first-party** (internal) or **public/third-party** (for external use).

***

## 2. REST (Representational State Transfer)
**REST** is an architectural style for designing networked applications, outlined by Roy Fielding in his doctoral dissertation.

### Core Principles of REST (Restful API)
A truly **Restful API** must adhere to six key principles:

1.  **Uniform Interface:** Makes the API more discoverable and less coupled with the backend. This involves:
    * **Resource Identification:** Using **URIs** (e.g., `/users/123`) to address resources.
    * **Self-Descriptive Messages:** Including headers like `Content-Type` to correctly parse the response.
    * **HATEOAS (Hypermedia As The Engine of Application State):** The server response includes links (hypermedia) or instructions to guide the client on what actions to perform next, making the client less dependent on documentation. (Note: Most APIs commonly called "REST" today do not fully implement HATEOAS).
2.  **Statelessness:** The server does not store client state between requests; each request must contain all necessary information (e.g., session info in headers or cookies).
3.  **Cacheability:** Responses should be labeled as cacheable or non-cacheable using headers like `Cache-Control`. Caching can occur in the browser, proxy server, or CDN.
4.  **Client-Server Architecture:** Separates the client UI from the data, making it easier to use the same view on different devices and hiding server construction details.
5.  **Layered System:** Hides the complex infrastructure (proxies, load balancers) between the client and the server from the client.
6.  **Code-on-Demand (Optional):** Servers can temporarily extend client functionality by transferring executable code, such as JavaScript.

### Advantages and Disadvantages

| Area | Advantages (REST over HTTP/JSON) | Disadvantages |
| :--- | :--- | :--- |
| **Simplicity** | Comprehensible rules, familiar protocol (HTTP), and human-readable data (JSON). | **Over-fetching:** Fetching too much data that isn't needed by the client. |
| **Low Coupling** | Client and server are less coupled if REST is built correctly. | **Under-fetching:** Not fetching enough data, requiring multiple requests to get all required entities. |
| **Web Suitability** | HTTP was designed to be compatible with REST. | **Versioning:** Requires versioning to ensure backward compatibility, adding complexity. |
| **Infrastructure** | Most infrastructure knows how to work with HTTP, facilitating maintenance. | **Uncontrollable Endpoints:** Becomes difficult to manage and document as the API grows large. |
| **Caching** | Native browser support for HTTP caching with correct headers. | **Validation:** No built-in mechanism (schemas/typing) to validate data on the client side. |

### Solutions to Over- and Under-fetching in REST
* **Over-fetching Fixes:** Use **Sparse Field Sets** (adding required fields to the URL, but limited by URL length) or create **additional, tailored endpoints**.
* **Under-fetching Fixes:** Add an **additional endpoint** that aggregates data or ask the backend team to **add fields** (increases coupling and may result in over-fetching for other consumers).

### Use Cases for REST
REST is often the default choice when you don't know why you need alternatives:
* **Public API:** The most distributed solution, familiar to most developers.
* **Simple Applications:** With mostly **CRUD** (Create, Read, Update, Delete) operations, where over/under-fetching is not critical.
* **Binary Data/Streaming/CRDT/Authentication:** GraphQL and RPC are not well-suited for these.

***

## 3. GraphQL (Query Language for APIs)
**GraphQL** is a query language and runtime for APIs that allows the client to request **only the specific data they need** in a single query.

### Advantages
* **Reduced Resource Consumption:** Solves the problem of Under- and Over-fetching in a single query.
* **Usability:** Eliminates the need to memorize hundreds of dedicated endpoints and parameters.
* **Static Analysis & Documentation:** It is **schema-based**, providing strong typing, and enabling automatic documentation generation.

### Disadvantages and Solutions

| Problem | Explanation | Solution |
| :--- | :--- | :--- |
| **N+1 Problem** | Backend developers cannot predict which data will be requested, leading to multiple, inefficient database queries. | **Batching:** Deferring database resolution until all data needed for a request is known, allowing a single, grouped SQL request. |
| **Security Issues (DoS)** | The flexible nature allows clients to perform very deeply nested requests that can crash the application. | **Avoid public API usage** (use REST instead), use **Quotas** to limit query depth, or apply **Throttling** to limit request frequency. |
| **Network Level Caching** | Typically used via a single `POST` endpoint, making caching difficult as all requests look the same to the server/proxy. | **Persisted Queries:** Client sends a hash of the query string to the server, which can be found and executed (improves performance by avoiding parsing). |

### Use Cases for GraphQL
* **Multiple Clients:** When an API server has multiple clients (e.g., web, iOS, Android) that need different data sets.
* **Mobile Clients:** Critical for mobile applications where limited traffic makes over-fetching a significant issue.
* **Complex Data Relationships:** When using REST becomes hard and requires many requests or frequent adjustments to endpoints.

***

## 4. RPC (Remote Procedure Call) Approaches
**RPC** is a communication pattern where a client calls procedures or functions on a remote server. The key difference from REST is that **REST describes entities** (e.g., `GET /users`), while **RPC describes actions or functions** (e.g., `getUsers()`).

### gRPC (Google RPC)
A specific RPC implementation using **HTTP/2** as a transport protocol and **Protocol Buffers (Protobuf)** for data serialization.

* **Advantages:** Better **performance** (due to HTTP/2 and Protobuf) and strong **typing/validation**.
* **Disadvantages:** Not natively supported by browsers (requires a proxy to work), and debugging is more difficult because Protobuf is not human-readable.
* **Use Cases:** Primarily for **Service-to-Service communication** (microservices) and Client-Server communication where **high data transfer speed** is critical.

### tRPC (TypeScript RPC)
An RPC implementation designed for **TypeScript monorepos** that uses JSON RPC.

* **Advantages:** Shared types between frontend and backend, full **static type safety**, and auto-complete for inputs/outputs/errors on the client. It is simpler to implement and doesn't require new query languages or infrastructure like gRPC.
* **Disadvantages:** **TypeScript is required** for both client and server, and **under-fetching** remains a problem because procedures are static.
* **Use Cases:** Perfect for **TypeScript monorepos** and full-stack teams working closely together.

***

## 5. Summary, System Design, and Interview Tips
### Coupling

The choice of protocol is often related to **coupling**, which measures how strongly the client is connected with the backend.

| Protocol | Coupling Level | Description |
| :--- | :--- | :--- |
| **tRPC** | **Maximum** | Full complexity is hidden inside functions; both teams must use TypeScript. |
| **REST** | **Medium** | Flexible enough to choose any language/protocol, making it suitable for multiple teams/public use. |
| **GraphQL** | **Least** | Provides the most freedom to the client to choose what data to query, offering maximum flexibility. |

**Rule of Thumb:** If backend and frontend teams are separate (or in different companies), consider REST or GraphQL.

### Protocol Selection Summary 
| Application Type | Protocol Recommendation | Rationale |
| :--- | :--- | :--- |
| **Private API** | REST, GraphQL, gRPC, or tRPC | All are possible depending on specific needs. |
| **Public API** | **REST** or **GraphQL** | Requires low coupling and flexibility for unknown consumers. |
| **Microservices** | **gRPC** or **Supergraph (GraphQL)** | gRPC for high performance; Supergraph for a unified data layer. |
| **Client-Server Architecture** | **REST** | Simple APIs, binary data/streaming, authentication, or limited resources. |
| | **GraphQL** | Multiple application versions (especially mobile), complex responses combining many entities. |
| | **tRPC** | Teams that work closely, full-stack teams, or monorepos with TypeScript. |
| | **gRPC** | When high performance is critical and development speed can be sacrificed. |

### Real-Life Examples

1.  **Stack Overflow (Clone)**:
    * **Features:** Search, showing posts, creating posts (mostly CRUD).
    * **Requirements:** Frontend/Backend not in monorepo, no dedicated mobile version.
    * **Conclusion:** **REST** is the best choice. While GraphQL could solve under-fetching (e.g., five requests for a post with comments vs. one query), REST offers familiarity, fast implementation, easy maintenance, and flexible API structure, making it suitable for an MVP.

2.  **Netflix (Clone)**:
    * **Features:** Search, movie details, recommendations.
    * **Requirements:** Mobile version/app exists, major audience uses cell phones, and clients need **different sets of fields** depending on screen size (e.g., desktop vs. mobile).
    * **Conclusion:** **GraphQL** is the most suitable choice. It allows multiple front-end teams/clients to be independent of the backend and naturally solves the problem of Under- and Over-fetching for clients with diverse data needs.

### Interview Tips

* **Always Describe Your Choices:** Explicitly state the protocol you choose and *why* you didn't choose the alternatives, highlighting the trade-offs.
* **Don't Forget About Trade-offs:** Discuss the negative scenarios and potential risks associated with your chosen technology.
* **Consider Other Teams/Business Requirements:** Factor in the cost of the solution and the complexity of implementation and support for the backend team.
* **Focus on High-Level Architecture:** Do not dive into implementation details unless specifically asked; focus on the rationale for your high-level decisions.
* **Know the Difference:** Be clear about the difference between a **Restful API** (follows all principles, including HATEOAS) and a more common "REST-like" or **Rest-foolish** API (which may ignore principles for convenience).

***
