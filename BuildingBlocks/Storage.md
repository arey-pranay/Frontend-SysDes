## Core Concepts

### What is an Origin?
A key concept for all web storage is the **Origin**, which defines the scope of the data. An Origin is a combination of three components [[02:17](http://www.youtube.com/watch?v=CegvBL1L8qA&t=137)]:

1.  **Protocol:** (e.g., `http` or `https`)
2.  **Host Name:** (the address of the site)
3.  **Port:** (the server port, often hidden if default is used)

### Storages Out of Scope
The video specifically omits discussing the File System API due to poor browser support, and WebSQL because it is deprecated and has been replaced by IndexedDB [[01:59](http://www.youtube.com/watch?v=CegvBL1L8qA&t=119)].

***

## Client-Side Web Storages

| Storage Type | Key Behavior & Lifetime |
| :--- | :--- |
| **URL Search Parameters** | Survives page refresh, but lost when the tab/page is closed [[03:19](http://www.youtube.com/watch?v=CegvBL1L8qA&t=199)]. |
| **Cookies** | Sent with every HTTP request; lifetime is configurable (session to long-term) [[05:40](http://www.youtube.com/watch?v=CegvBL1L8qA&t=340)]. |
| **Session Storage** | Cleared when the **tab is closed**; not shared between tabs [[13:54](http://www.youtube.com/watch?v=CegvBL1L8qA&t=834)]. |
| **Local Storage** | **Persistent** (data remains after the tab is closed, subject to quotas) [[09:42](http://www.youtube.com/watch?v=CegvBL1L8qA&t=582)]. |
| **Cache API** | Designed for request/response pairs; managed by developers/service workers [[15:35](http://www.youtube.com/watch?v=CegvBL1L8qA&t=935)]. |
| **IndexedDB** | **Persistent** NoSQL database; structured data, large capacity, complex data types [[19:19](http://www.youtube.com/watch?v=CegvBL1L8qA&t=1159)]. |

### 1. URL Search Parameters
This is the query string part of the URL. While not a conventional storage, it offers a unique advantage [[03:04](http://www.youtube.com/watch?v=CegvBL1L8qA&t=184)].

* **Advantages:** **Easy to Share** with other users or save in browser bookmarks [[03:39](http://www.youtube.com/watch?v=CegvBL1L8qA&t=219)].
* **Disadvantages:** Very **fragile** and easily lost, visible (not secure), and has a **small capacity** (~2,000 symbols) [[04:08](http://www.youtube.com/watch?v=CegvBL1L8qA&t=248)].
* **Use Cases:** Storing **navigation state** (page number, filters, search query) or **current page configuration** (e.g., a map view) [[04:53](http://www.youtube.com/watch?v=CegvBL1L8qA&t=293)].

### 2. Cookies
Cookies are distinct because they are designed for communication with the server.

* **Advantages:** They can be set by the **server** and are **automatically sent** with every HTTP request [[05:37](http://www.youtube.com/watch?v=CegvBL1L8qA&t=337)]. They also allow for better protection against Cross-Site Scripting (XSS) via the **HTTP-only** flag [[05:54](http://www.youtube.com/watch?v=CegvBL1L8qA&t=354)].
* **Disadvantages:** Limited to **4 kilobytes** per origin and increase HTTP overhead because they are sent with *every* request [[06:02](http://www.youtube.com/watch?v=CegvBL1L8qA&t=362)].
* **Use Cases:**
    * **Session Management:** Ideal for storing a user session token [[06:24](http://www.youtube.com/watch?v=CegvBL1L8qA&t=384)].
    * **Personalization:** Allowing the server to return a personalized page on the very first request (e.g., set language) [[07:29](http://www.youtube.com/watch?v=CegvBL1L8qA&t=449)].
    * **Tracking User Behavior:** Used by third-party ad providers to identify and track users across different sites [[08:23](http://www.youtube.com/watch?v=CegvBL1L8qA&t=503)].

### 3. Local Storage and Session Storage
These are often grouped together as simple, key-value storages.

| Feature | Local Storage | Session Storage |
| :--- | :--- | :--- |
| **Persistence** | **Persistent** (data remains) [[09:42](http://www.youtube.com/watch?v=CegvBL1L8qA&t=582)]. | Cleared when the **tab is closed** [[13:54](http://www.youtube.com/watch?v=CegvBL1L8qA&t=834)]. |
| **Capacity** | Typically **5 MB** per origin [[10:07](http://www.youtube.com/watch?v=CegvBL1L8qA&t=607)]. | From 5 MB to 15 MB per origin [[14:42](http://www.youtube.com/watch?v=CegvBL1L8qA&t=882)]. |
| **API** | **Synchronous** and slow, blocking the main thread [[10:24](http://www.youtube.com/watch?v=CegvBL1L8qA&t=624)]. | **Synchronous** and slow, blocking the main thread [[14:48](http://www.youtube.com/watch?v=CegvBL1L8qA&t=888)]. |
| **Data Types**| **String-only**, requiring manual encoding/decoding [[11:04](http://www.youtube.com/watch?v=CegvBL1L8qA&t=664)]. | **String-only** [[14:48](http://www.youtube.com/watch?v=CegvBL1L8qA&t=888)]. |
| **Workers** | Not available in web or service workers [[10:37](http://www.youtube.com/watch?v=CegvBL1L8qA&t=637)]. | Not available in web or service workers [[14:48](http://www.youtube.com/watch?v=CegvBL1L8qA&t=888)]. |
* **Local Storage Use Cases:** Simple persistent storage for user preferences (theme, language) or temporary data for simple **offline-first** applications [[11:29](http://www.youtube.com/watch?v=CegvBL1L8qA&t=689)].
* **Session Storage Use Cases:** Caching data for the duration of a single user session or passing user/system data across screens in a multi-step process (like a wizard) [[14:55](http://www.youtube.com/watch?v=CegvBL1L8qA&t=895)]. Its automatic data cleanup is a key advantage [[14:29](http://www.youtube.com/watch?v=CegvBL1L8qA&t=869)].

### 4. Cache API
This is a dedicated storage for request/response pairs, unlike the general-purpose key-value storages [[16:23](http://www.youtube.com/watch?v=CegvBL1L8qA&t=983)].

* **Key Advantage:** It is accessible in **Service Workers**, making it essential for robust offline functionality [[15:43](http://www.youtube.com/watch?v=CegvBL1L8qA&t=943)].
* **Function:** Service workers can intercept network requests, check the Cache API for a response (e.g., an HTML page or static file), and return the cached asset, eliminating the need to contact the server [[15:52](http://www.youtube.com/watch?v=CegvBL1L8qA&t=952)].
* **Distinction from Browser Cache:**
    * **Browser Cache** is controlled by the browser using HTTP headers (`Cache-Control`) [[17:39](http://www.youtube.com/watch?v=CegvBL1L8qA&t=1059)].
    * **Cache API** is controlled by developers and allows manual writing and reading [[17:58](http://www.youtube.com/watch?v=CegvBL1L8qA&t=1078)].

### 5. IndexedDB (IDB)
IndexedDB is the most powerful client-side storage, functioning as a NoSQL database in the browser [[19:19](http://www.youtube.com/watch?v=CegvBL1L8qA&t=1159)].

* **Advantages:**
    * **Large Capacity** [[19:23](http://www.youtube.com/watch?v=CegvBL1L8qA&t=1163)].
    * **Fast Asynchronous API** (does not block the main thread) [[19:35](http://www.youtube.com/watch?v=CegvBL1L8qA&t=1175)].
    * Supports **indexing, transactions, versioning**, and nearly all JavaScript data types [[19:28](http://www.youtube.com/watch?v=CegvBL1L8qA&t=1168)].
    * Supports storing **binary files** (e.g., media content for offline use) [[22:09](http://www.youtube.com/watch?v=CegvBL1L8qA&t=1329)].
* **Disadvantages:**
    * **Steep Learning Curve** (wrappers like `idb` are recommended) [[19:42](http://www.youtube.com/watch?v=CegvBL1L8qA&t=1182)].
    * **Intelligent Tracking Prevention (ITP):** In browsers like Safari, data is deleted after **7 days of user inactivity**, meaning client-side storage is not truly long-term [[19:58](http://www.youtube.com/watch?v=CegvBL1L8qA&t=1198)].
* **Use Cases:** Storing **large amounts of data**, data with **complex structure**, binary files, and essential for **offline-first applications** [[21:36](http://www.youtube.com/watch?v=CegvBL1L8qA&t=1296)].

***

## Server-Side Storage: Remote Databases

Remote databases are a crucial part of the storage decision, as moving data to the server can be the smartest choice [[25:12](http://www.youtube.com/watch?v=CegvBL1L8qA&t=1512)].

* **Advantages:**
    * **Highest Reliability** [[25:31](http://www.youtube.com/watch?v=CegvBL1L8qA&t=1531)].
    * **Security:** Ideal for storing **sensitive data** (credentials, personal information) [[25:40](http://www.youtube.com/watch?v=CegvBL1L8qA&t=1540)].
    * **Data Sharing** across devices [[25:47](http://www.youtube.com/watch?v=CegvBL1L8qA&t=1547)].
* **Disadvantages:**
    * **Complexity and High Costs** [[25:56](http://www.youtube.com/watch?v=CegvBL1L8qA&t=1556)].
    * **Slower User Interface** due to required server requests for every data interaction [[27:06](http://www.youtube.com/watch?v=CegvBL1L8qA&t=1626)].
    * **Impossible Offline Use** [[27:17](http://www.youtube.com/watch?v=CegvBL1L8qA&t=1637)].

***

## Quotas and Reliability Policies

### Shared vs. Individual Quotas
Many persistent storages share a single quota pool, which can lead to conflicts [[22:35](http://www.youtube.com/watch?v=CegvBL1L8qA&t=1355)].

* **Shared Quota:** **Local Storage, IndexedDB, and Cache API** share the same disk space quota. If one storage consumes it all, others cannot write data [[22:50](http://www.youtube.com/watch?v=CegvBL1L8qA&t=1370)].
* **Individual Quota:** Cookies, Session Storage, and Browser Cache operate on their own separate quotas [[23:57](http://www.youtube.com/watch?v=CegvBL1L8qA&t=1437)].

### Eviction Policy
Browsers manage data deletion through two policies when quotas are exceeded [[24:10](http://www.youtube.com/watch?v=CegvBL1L8qA&t=1450)]:

1.  **Best Effort (Default):** The browser automatically deletes data based on the **Least Recently Used (LRU)** policy to free up space [[24:21](http://www.youtube.com/watch?v=CegvBL1L8qA&t=1461)].
2.  **Persistent Mode:** The browser **will not automatically delete** data. Write operations throw a `QuotaExceededError`. This is required for applications that need guaranteed offline storage [[24:30](http://www.youtube.com/watch?v=CegvBL1L8qA&t=1470)].

***

## System Design Example: Storing User Drafts

For an application like Facebook, deciding where to store a user's post draft (which can be long and contain complex data or attachments) is a critical choice [[31:04](http://www.youtube.com/watch?v=CegvBL1L8qA&t=1864)].

* **Storage Decision for Drafts:**
    * **Remote Database (Primary):** Selected as the best primary solution because of its **reliability** and the need for **data sharing** (starting a draft on a phone, continuing on a desktop) [[31:57](http://www.youtube.com/watch?v=CegvBL1L8qA&t=1917)].
    * **IndexedDB (Secondary):** Used as a temporary solution for **offline mode** [[33:12](http://www.youtube.com/watch?v=CegvBL1L8qA&t=1992)].
    * **Local Storage (Rejected):** Not suitable due to its synchronous API (slow for long content), small capacity, and lack of support for complex structures and binary files [[32:40](http://www.youtube.com/watch?v=CegvBL1L8qA&t=1960)].

* **Final Strategy:**
    1.  **Online:** Save drafts to the **Remote Database** [[33:52](http://www.youtube.com/watch?v=CegvBL1L8qA&t=2032)].
    2.  **Offline:** Save drafts to **IndexedDB** [[33:56](http://www.youtube.com/watch?v=CegvBL1L8qA&t=2036)].
    3.  **Back Online:** **Synchronize** data between the IndexedDB and the Remote Database [[34:08](http://www.youtube.com/watch?v=CegvBL1L8qA&t=2048)].
