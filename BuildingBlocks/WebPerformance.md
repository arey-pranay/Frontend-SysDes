## 1. Defining Web Performance and Tradeoffs

Web performance is defined by two critical factors: **how fast the application loads** and **how fast the application works** after loading [[01:01](http://www.youtube.com/watch?v=KUdqbIHn8Ic&t=61)]. An application must excel in both areas to be considered fully performant [[01:19](http://www.youtube.com/watch?v=KUdqbIHn8Ic&t=79)].

### Loading Time vs. UI Speed Priorities
Software development involves balancing tradeoffs. Depending on the application's nature, one direction may be prioritized:

| Priority | Application Type & Rationale | Criticality Examples |
| :--- | :--- | :--- |
| **Loading Time** | **Competitive Markets** (must be faster than competitors) [[01:54](http://www.youtube.com/watch?v=KUdqbIHn8Ic&t=114)], **Mobile Users** (poor connections worsen slow loading) [[02:12](http://www.youtube.com/watch?v=KUdqbIHn8Ic&t=132)], **E-commerce/Impulse Purchases** (Conversion Rate Dependency) [[02:22](http://www.youtube.com/watch?v=KUdqbIHn8Ic&t=142)]. | **Amazon** found a 100ms delay led to a 1% drop in sales [[03:48](http://www.youtube.com/watch?v=KUdqbIHn8Ic&t=228)]. **Google** found 53% of mobile users abandon a site taking longer than 3 seconds to load [[03:56](http://www.youtube.com/watch?v=KUdqbIHn8Ic&t=236)]. |
| **UI Speed** | **Task-Oriented Applications** (e.g., Google Sheets, Figma) for specific user tasks [[07:07](http://www.youtube.com/watch?v=KUdqbIHn8Ic&t=427)], **Long Session Time** (slow interface negative effects are magnified) [[07:35](http://www.youtube.com/watch?v=KUdqbIHn8Ic&t=455)], **B2B Services** (rich feature set outweighs minor loading delays) [[05:19](http://www.youtube.com/watch?v=KUdqbIHn8Ic&t=319)], **Apps with Frequent Interactions** [[08:10](http://www.youtube.com/watch?v=KUdqbIHn8Ic&t=490)]. | For B2B apps, revenue depends on UI speed; a slow, unresponsive UI leads to client loss [[08:03](http://www.youtube.com/watch?v=KUdqbIHn8Ic&t=483)]. **Figma** is a prime example where UI speed is critical due to long, interaction-heavy sessions [[08:38](http://www.youtube.com/watch?v=KUdqbIHn8Ic&t=518)]. |

***

## 2. Improving Loading Time Metrics

The loading process involves a series of steps, each tied to a specific metric that should be optimized.

### 2.1. Time to First Byte (TTFB)
TTFB measures the time from the client requesting a page until the browser receives the first byte of data from the server [[01:15:50](http://www.youtube.com/watch?v=KUdqbIHn8Ic&t=4550)]. A good target is **under 200 milliseconds** [[01:17:09](http://www.youtube.com/watch?v=KUdqbIHn8Ic&t=4629)].

Optimization Goals: **Process the request on the server as quickly as possible** and **deliver data to the client as quickly as possible** [[01:17:45](http://www.youtube.com/watch?v=KUdqbIHn8Ic&t=4665)].

| Technique | Description |
| :--- | :--- |
| **Content Delivery Network (CDN)** | Serves static files and caches resources closer to the end user to minimize the distance and speed of light delay [[01:18:29](http://www.youtube.com/watch?v=KUdqbIHn8Ic&t=4709)]. **Image of CDN architecture** |
| **Server-Side Optimization** | Crucial for dynamic pages (SSR/Hydration). Includes **indexing** database tables, using **batch queries**, and dynamically **scaling** server resources [[01:26:06](http://www.youtube.com/watch?v=KUdqbIHn8Ic&t=5166)]. |
| **DNS Lookup Optimization** | Uses **DNS Pre-fetching** to resolve domain names to IP addresses in advance for critical resources [[01:27:10](http://www.youtube.com/watch?v=KUdqbIHn8Ic&t=5230)]. |
| **Compression** | Reduces asset size. **Gzip** is widely supported, while **Brotli** offers better compression rates and faster decompression (use Brotli as primary, Gzip as fallback) [[01:28:34](http://www.youtube.com/watch?v=KUdqbIHn8Ic&t=5314)]. |
| **Response Caching** | Caches heavy database queries and third-party API responses (especially those with rate limits or high latency) using services like **Redis** or **Memcached** [[01:30:28](http://www.youtube.com/watch?v=KUdqbIHn8Ic&t=5428)]. |
| **Reduce HTTP Overhead** | Minimize unnecessary headers (e.g., `Server`, `X-Powered-By`) and limit **cookies** to relevant paths and domains [[01:31:38](http://www.youtube.com/watch?v=KUdqbIHn8Ic&t=5498)]. |

#### CDN Details (Cache Hit Ratio)
A high **Cache Hit Ratio (CHR)** is the primary goal, measuring how often the CDN serves content from its cache versus having to request it from the origin server (a "Cache Miss") [[01:31:38](http://www.youtube.com/watch?v=KUdqbIHn8Ic&t=5498)].

* **To Improve CHR**: Handle query parameters effectively (normalize or ignore them), avoid unnecessary cookies, and apply long **Time To Live (TTL)** for static assets [[01:39:59](http://www.youtube.com/watch?v=KUdqbIHn8Ic&t=5999)].
* **Caching Strategy**: Cache **Public Resources** (static content with long TTL) and potentially **Dynamic Content** (short TTL during high traffic). **Never cache Private Resources** (user-specific or sensitive data) [[01:22:10](http://www.youtube.com/watch?v=KUdqbIHn8Ic&t=4930)].

***

### 2.2. First Contentful Paint (FCP) and Largest Contentful Paint (LCP)
**FCP** measures when the first visible element is rendered [[01:16:21](http://www.youtube.com/watch?v=KUdqbIHn8Ic&t=4581)]. Target: **under 1.8 seconds**.
**LCP** measures when the largest visible content on the page is rendered [[01:16:29](http://www.youtube.com/watch?v=KUdqbIHn8Ic&t=4589)]. Target: **under 2.5 seconds**.

Optimization is guided by the **PURPLE Pattern** (**P**reload, **U**pdate, **R**ender, **P**re-cache, **L**azy-load, **E**verything):

#### 1. Prioritize Above-The-Fold (ATF) Content
**ATF** content is the part of the page visible without scrolling [[01:35:42](http://www.youtube.com/watch?v=KUdqbIHn8Ic&t=5742)]. All resources needed to render this section must be loaded immediately.

* **Preloading**: Fetching specific resources in advance that the browser's preload scanner cannot detect automatically (e.g., lazy-loaded images, critical CSS background images, fonts defined with `@font-face`) [[01:34:02](http://www.youtube.com/watch?v=KUdqbIHn8Ic&t=5642)].
* **Inlining Critical CSS/JS**: Embedding essential CSS and JavaScript directly into the HTML to render the ATF content without waiting for external file downloads [[01:36:21](http://www.youtube.com/watch?v=KUdqbIHn8Ic&t=5781)].

#### 2. Optimization for Media (Images and Video)
Use modern, optimized formats and ensure they are delivered quickly, especially for the LCP element.

* **Image Formats**: Use **AVIF** (superior compression, image quality, HDR support) and **WebP** (faster processing, broader support) [[01:43:04](http://www.youtube.com/watch?v=KUdqbIHn8Ic&t=6184)]. PNG and JPEG serve as fallbacks.
* **Video Formats**: Use **AV1** (open-source, ideal for streaming and 4K/8K) or **VP9** (Google's open-source, widely supported) within the **WebM** container [[01:44:47](http://www.youtube.com/watch?v=KUdqbIHn8Ic&t=6287)].
* **CDN Media Optimization**: CDNs can automatically convert formats, compress, and adjust resolutions based on the user's device and network [[01:47:20](http://www.youtube.com/watch?v=KUdqbIHn8Ic&t=6440)].

#### 3. Caching and Lazy Loading
Manage resource loading dynamically to only load what is needed when it is needed.

* **Client-Side Caching**: Uses three types of storage [[01:37:23](http://www.youtube.com/watch?v=KUdqbIHn8Ic&t=5843)]:
    * **Browser Cache** (automatic, long TTL for static assets).
    * **JavaScript Memory** (session-based, clears on page reload).
    * **Service Worker Cache** (intercepts requests, supports offline functionality) [[01:38:14](http://www.youtube.com/watch?v=KUdqbIHn8Ic&t=5894)].
    * **Stale Content Avoidance**: Use **Cache Busting** (e.g., adding version hashes to file names like `main.abc123.js`) to force the browser to fetch new files after updates [[01:38:50](http://www.youtube.com/watch?v=KUdqbIHn8Ic&t=5930)].
* **Lazy Loading**: Conditional loading of non-critical assets (e.g., images, code, third-party libraries) only when they are near or visible in the user's viewport [[01:39:22](http://www.youtube.com/watch?v=KUdqbIHn8Ic&t=5962)]. Overuse can be detrimental to user experience [[01:39:59](http://www.youtube.com/watch?v=KUdqbIHn8Ic&t=5999)].

#### 4. JavaScript Optimization
Minimize the size and execution time of the main thread script.

* **Tree Shaking**: Removes unused code during the build process, relying on ES modules' static analyzability [[01:48:56](http://www.youtube.com/watch?v=KUdqbIHn8Ic&t=6536)].
* **Code Splitting**: Divides the JavaScript bundle into smaller, loadable chunks (by page, route, or component) using dynamic imports, which is especially relevant for Single Page Applications (SPAs) [[01:50:57](http://www.youtube.com/watch?v=KUdqbIHn8Ic&t=6657)].
* **Defer/Async Loading**: Delays the loading and execution of non-critical scripts [[01:49:14](http://www.youtube.com/watch?v=KUdqbIHn8Ic&t=6554)].

***

### 2.3. Time to Interactive (TTI)
TTI is the time it takes for the page to become fully interactive (buttons, links, etc. are responsive) [[01:51:53](http://www.youtube.com/watch?v=KUdqbIHn8Ic&t=6713)]. Target: **under 3.8 seconds**. This requires JavaScript to be fully loaded, parsed, and executed.

Optimization Goals: **Avoid executing heavy JavaScript on the main thread** [[01:52:17](http://www.youtube.com/watch?v=KUdqbIHn8Ic&t=6737)].

| Technique | Description |
| :--- | :--- |
| **Delegating Work (Main Thread)** | Break long-running JavaScript tasks into smaller chunks and run them in a new event loop cycle using **`setTimeout`** or **`requestAnimationFrame`** [[01:53:27](http://www.youtube.com/watch?v=KUdqbIHn8Ic&t=6807)]. For low-priority tasks, use **`requestIdleCallback`** [[01:53:50](http://www.youtube.com/watch?v=KUdqbIHn8Ic&t=6830)]. |
| **Web Workers** | Move heavy computations (e.g., image processing, large math operations) to a separate background thread (**Web Worker**) to keep the main thread free for UI interaction [[01:53:35](http://www.youtube.com/watch?v=KUdqbIHn8Ic&t=6815)]. |
| **Server-Side Computation** | Offload tasks like data processing, filtering, and intensive calculations to the back end. This increases server costs but frees the client-side main thread [[01:54:57](http://www.youtube.com/watch?v=KUdqbIHn8Ic&t=6897)]. |
| **Predictive Prefetching** | Anticipates user actions (e.g., navigation patterns) and preloads resources (images, scripts, HTML) for pages the user is likely to visit next, reducing perceived latency [[01:56:08](http://www.youtube.com/watch?v=KUdqbIHn8Ic&t=6968)]. |

***

## 3. Improving UI Speed (Smooth Animations)

UI speed focuses on smooth animations, quick server responses, and computations that do not block the page once it has loaded [[01:56:50](http://www.youtube.com/watch?v=KUdqbIHn8Ic&t=7010)].

### 3.1. GPU Acceleration and the Rendering Pipeline

The key to smooth animation is using **GPU Acceleration** and focusing on the Composition Layer [[01:57:13](http://www.youtube.com/watch?v=KUdqbIHn8Ic&t=7033)].

* **Prefer CSS over JavaScript Animations**: JS animations run on the CPU (main thread), which can block user interactions. CSS animations can be offloaded to the GPU [[01:57:22](http://www.youtube.com/watch?v=KUdqbIHn8Ic&t=7042)].
* **GPU-Friendly Properties**: Only CSS properties that do not trigger layout or paint recalculations are suitable for the GPU. These are typically **`transform`** and **`opacity`** [[01:58:11](http://www.youtube.com/watch?v=KUdqbIHn8Ic&t=7091)].
* **Rendering Pipeline**: The browser renders a page through a sequence of steps. GPU-accelerated animations skip the most expensive steps (**Layout** and **Paint**) and only work on the final step, **Composite** [[01:59:18](http://www.youtube.com/watch?v=KUdqbIHn8Ic&t=7158)]. **Image of Web Rendering Pipeline**

    1.  **JavaScript** (changes styles) [[01:59:31](http://www.youtube.com/watch?v=KUdqbIHn8Ic&t=7171)]
    2.  **Style** (recalculates computed styles) [[01:59:43](http://www.youtube.com/watch?v=KUdqbIHn8Ic&t=7183)]
    3.  **Layout** (computes geometry/position – *most expensive*) [[01:59:52](http://www.youtube.com/watch?v=KUdqbIHn8Ic&t=7192)]
    4.  **Paint** (fills in visual styles like background) [[02:00:09](http://www.youtube.com/watch?v=KUdqbIHn8Ic&t=7209)]
    5.  **Composite Layers** (merges painted elements into a final image – *GPU-friendly*) [[02:00:25](http://www.youtube.com/watch?v=KUdqbIHn8Ic&t=7225)]

* **Composition Layer Promotion**: Browsers automatically promote elements to their own layer for animations. Manual promotion using the CSS property **`will-change: transform`** or **`will-change: opacity`** can proactively create the layer to avoid animation delays, but must be used sparingly as it consumes GPU memory [[02:02:08](http://www.youtube.com/watch?v=KUdqbIHn8Ic&t=7328)].

### 3.2. Unblocking the Main Thread

To maintain responsiveness and a **60 frames per second** (one frame every 16.6 milliseconds) render rate [[02:06:07](http://www.youtube.com/watch?v=KUdqbIHn8Ic&t=7567)], the main thread must remain free.

* **Minimizing DOM Manipulations**: Changes to the Document Object Model (DOM) trigger expensive style and layout recalculations [[02:06:17](http://www.youtube.com/watch?v=KUdqbIHn8Ic&t=7577)].
    * Group multiple DOM changes into a single update to minimize Layout Recalculations [[02:06:44](http://www.youtube.com/watch?v=KUdqbIHn8Ic&t=7604)].
    * Avoid reading and writing to the DOM repeatedly in a loop.
* **Avoiding Layout Thrashing**: This occurs when JavaScript repeatedly reads layout properties (e.g., `offsetHeight`) and then immediately modifies the DOM, forcing the browser to recalculate the layout many times in quick succession [[02:07:02](http://www.youtube.com/watch?v=KUdqbIHn8Ic&t=7622)].

    * **Solution**: Separate style reads and style writes. First, complete all style reads (e.g., outside a loop), then execute all style writes (e.g., inside the loop) [[02:07:46](http://www.youtube.com/watch?v=KUdqbIHn8Ic&t=7666)]. **Image of Layout Thrashing**
