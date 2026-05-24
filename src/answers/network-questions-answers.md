---
title: Network Questions - Answers
layout: layouts/page.njk
---

## Traditionally, why has it been better to serve site assets from multiple domains?

This was primarily about **bypassing browser connection limits**. Browsers historically limited parallel connections to **6–8 per domain**. By sharding assets across multiple domains (e.g., `static1.example.com`, `static2.example.com`), you could download more resources simultaneously, reducing overall page load time.

**However**, this is largely outdated advice today. Modern browsers support HTTP/2 and HTTP/3, which use multiplexing, allowing many requests to stream concurrently over a single connection. Domain sharding now actually hurts performance because it:
- Creates additional DNS lookups
- Prevents connection reuse
- Negates HTTP/2's multiplexing benefits

The modern approach is to serve assets from a single domain or a small number of domains over HTTP/2+.

## Describe the process from the time you type in a website's URL to it finishing loading on your screen

1. **URL Parsing** - The browser parses the URL to extract the protocol (https), domain, port, path, etc.

2. **DNS Lookup** - The browser checks its cache, then the OS cache, then queries a DNS resolver to translate the domain name into an IP address. This may involve recursive queries through root, TLD, and authoritative nameservers.

3. **TCP Connection** - The browser establishes a TCP connection to the server via a three-way handshake (SYN, SYN-ACK, ACK). With TLS/HTTPS, an additional TLS handshake occurs to negotiate encryption.

4. **HTTP Request** - The browser sends an HTTP GET request to the server, including headers like `Host`, `User-Agent`, `Accept`, `Cookie`, etc.

5. **Server Processing** - The server receives the request, processes it (routing, middleware, database queries, rendering), and generates an HTTP response with status code, headers, and body (usually HTML).

6. **Response Reception** - The browser receives the initial HTML document and begins parsing it incrementally.

7. **HTML Parsing & DOM Construction** - The browser parses HTML into a DOM tree. When it encounters external resources:
   - **CSS** - Downloaded and parsed into CSSOM; blocks rendering but not parsing
   - **JavaScript** - Downloaded and executed; blocks HTML parsing unless `async` or `defer`
   - **Images/Fonts** - Downloaded asynchronously

8. **Render Tree Construction** - The DOM and CSSOM are combined into a render tree of visible elements with their computed styles.

9. **Layout** - The browser calculates the exact position and size of each element.

10. **Painting** - The browser fills in pixels for each element (rasterization).

11. **Compositing** - Layers are assembled by the GPU and displayed on screen.

12. **Post-Load** - `onload` fires, then deferred scripts execute, and lazy-loaded assets may continue downloading.

## What are the differences between Long-Polling, WebSockets, and Server-Sent Events?

### Long-Polling
- The client sends an HTTP request and the server **holds it open** until data is available or a timeout occurs
- Once the client receives a response, it immediately sends another request
- Simulates real-time behavior over standard HTTP
- **Downsides**: High latency, overhead from repeated HTTP headers, connection churn
- **Use case**: Legacy real-time apps, environments where WebSockets aren't supported

### WebSockets
- A **full-duplex, persistent connection** upgraded from an HTTP handshake
- Both client and server can send messages at any time with minimal overhead
- Stateful protocol running over TCP (uses `ws://` or `wss://`)
- **Benefits**: Low latency, bidirectional, lightweight framing
- **Use case**: Chat apps, live games, collaborative editing, real-time dashboards

### Server-Sent Events (SSE)
- A **unidirectional** connection where the server pushes data to the client over standard HTTP
- Client opens a connection and the server streams events with `text/event-stream` content type
- Built-in reconnection and event ID support for resuming streams
- **Limitation**: Server → client only; no built-in binary support
- **Use case**: Live feeds, notifications, real-time logs, stock tickers

## Explain the following request and response headers

### Expires, Date, Age, and If-Modified-Since

- **`Date`** - Response header indicating when the response was generated on the server. Used for age calculations.

- **`Expires`** - Response header specifying an absolute date/time after which the resource is considered stale. Largely superseded by `Cache-Control: max-age`. Problematic because client/server clock skew causes incorrect caching behavior.

- **`Age`** - Response header (added by intermediate caches) indicating how many seconds the response has been in cache. The browser uses this to determine freshness: `Age + response_age < max_age`.

- **`If-Modified-Since`** - Request header sent with a timestamp. The server checks if the resource has changed since that time. If unchanged, returns `304 Not Modified` with no body, saving bandwidth. If changed, returns `200 OK` with the new content.

### Do Not Track (DNT)
A request header (`DNT: 1`) that signals the user's preference **not to be tracked** across sites for advertising or analytics. It was a voluntary standard-sites could respect or ignore it. It's now considered deprecated in favor of stronger privacy mechanisms like the **Global Privacy Control (GPC)** header, and because most sites simply ignored it.

### Cache-Control
A request and response header that defines **caching directives** for browsers and intermediate caches. Key directives:
- **`max-age=N`** - Cache for N seconds
- **`no-cache`** - Must revalidate before using cached copy
- **`no-store`** - Don't cache anything at all
- **`public`** - Can be cached by any cache (even authenticated responses)
- **`private`** - Only cacheable by the browser, not CDNs/proxies
- **`must-revalidate`** - Once stale, must check with server before using
- **`immutable`** - Resource never changes; don't revalidate even on reload

### Transfer-Encoding
A hop-by-hop response header that specifies the **form of encoding used to transfer the body** between nodes. The most common value is **`chunked`**, which means data is sent in chunks with size prefixes, allowing the server to start sending before the full response size is known. This enables streaming responses. Unlike `Content-Encoding` (which is end-to-end and about compression like gzip), `Transfer-Encoding` is about how the message is transmitted.

### ETag (Entity Tag)
A response header containing a **unique identifier** (usually a hash or version) for a specific version of a resource. The client sends it back in **`If-None-Match`** requests. If the resource hasn't changed, the server returns `304 Not Modified`. More precise than date-based validation because it catches content changes that don't modify the timestamp. Multiple ETags can be compared for optimistic concurrency control.

### X-Frame-Options
A response header that controls whether a page can be displayed inside a **`<frame>`, `<iframe>`, or `<object>`**. This prevents **clickjacking attacks**. Values:
- **`DENY`** - Cannot be framed at all
- **`SAMEORIGIN`** - Can only be framed by pages from the same origin
- **`ALLOW-FROM uri`** - Can be framed by a specific origin (deprecated)

This is now largely superseded by the `frame-ancestors` directive in **Content-Security-Policy (CSP)**.

## What are HTTP methods? List all HTTP methods that you know, and explain them.

HTTP methods (verbs) define the **intended action** on a resource. The primary ones:

- **`GET`** - Retrieve a resource. Safe (no side effects) and idempotent. Should only return data.

- **`POST`** - Create a new resource or submit data for processing. Not safe, not idempotent. The server determines the resulting resource's URI.

- **`PUT`** - Replace or create a resource at a specific URI. Idempotent-multiple identical requests produce the same result.

- **`PATCH`** - Partially modify a resource. Not necessarily idempotent. More efficient than PUT for small updates.

- **`DELETE`** - Remove a resource. Idempotent-deleting something that's already gone is still successful.

- **`HEAD`** - Identical to GET but returns only headers, no body. Useful for checking existence or metadata without transferring the full resource.

- **`OPTIONS`** - Ask what methods are supported for a resource. Used for CORS preflight requests. Returns `Allow` header listing permitted methods.

- **`TRACE`** - Echoes back the request for diagnostic purposes. Often disabled due to security concerns (cross-site tracing attacks).

- **`CONNECT`** - Establishes a tunnel (often for TLS through a proxy). Used for HTTPS connections through proxies.

## What is domain pre-fetching and how does it help with performance?

Domain pre-fetching (DNS prefetching) tells the browser to **resolve domain names to IP addresses before the user actually clicks a link**. It's done via:

```html
<link rel="dns-prefetch" href="//api.example.com">
```

**How it helps**: DNS lookups can take 20–120ms. By resolving domains early, during idle time or page load, the browser eliminates that latency when the resource is actually needed. This is especially valuable for:

- Third-party domains (analytics, CDNs, APIs)
- Origins you know the user will navigate to next
- Resource domains referenced in CSS/JS that the preload scanner hasn't discovered yet

Related performance techniques include:
- **`preconnect`** - DNS + TCP + TLS handshake upfront
- **`prefetch`** - Downloads and caches a full resource for future navigation
- **`preload`** - Downloads a resource needed soon on the current page with higher priority

## What is a CDN and what is the benefit of using one?

A **Content Delivery Network (CDN)** is a geographically distributed network of edge servers that cache and serve static (and sometimes dynamic) content closer to end users.

### Benefits:

- **Reduced Latency** - Users are served from the nearest edge node rather than the origin server, reducing round-trip time significantly.

- **Improved Load Times** - Shorter distance means faster content delivery, directly improving Core Web Vitals (LCP, FID, CLS).

- **Origin Offloading** - The CDN absorbs the majority of traffic, drastically reducing load on the origin server and preventing it from becoming a bottleneck.

- **Scalability** - CDNs handle traffic spikes (like product launches or DDoS attacks) without any infrastructure changes on your end.

- **Availability & Redundancy** - If one edge node goes down, traffic routes to the next nearest one. CDNs also protect against origin server failures by serving cached content.

- **Built-in Optimization** - Most CDNs offer automatic features like:
  - Image optimization and resizing
  - Brotli/Gzip compression
  - HTTP/3 support
  - Edge computing (running code at the edge)
  - Security features (DDoS protection, WAF, bot mitigation)

- **Lower Bandwidth Costs** - Since most requests never hit the origin, data transfer costs drop significantly.
