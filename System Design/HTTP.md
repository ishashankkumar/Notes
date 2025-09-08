# Introduction to HTTP Protocol

HTTP (Hypertext Transfer Protocol) is a fundamental protocol enabling browsers and servers to communicate, primarily for sending and receiving data. While other protocols exist, HTTP is one of the most widely used.

HTTP is an **application-level protocol** and relies on lower layers (TCP/UDP) to handle transport. It doesn’t guarantee delivery itself — that’s TCP (or QUIC in HTTP/3).

HTTPS is a more secure version of HTTP, incorporating encryption and security certificates (like TLS). For most discussions, HTTP and HTTPS can be considered interchangeable due to their shared underlying principles.

---

## Core Ideas of HTTP

### 1. Statelessness

- HTTP has no memory of past interactions. Each request is treated as a new and unrelated event by the server.
- Requests are self-contained, meaning all necessary information (e.g., authentication tokens, session information) must be included in every request.

**Benefits:**
- **Simplicity:** Server doesn’t need to store state.
- **Scalability:** Requests can be distributed across servers.
- **Resilience:** A crash doesn’t wipe session state.

**State Management:** Developers add state manually using cookies, tokens, or sessions. Statelessness doesn’t mean servers *can’t* store state, only that the **protocol doesn’t enforce it**.  

💡 **Analogy:** Ordering pizza delivery. Each time you order, you must provide your full address — unless the restaurant builds a loyalty account system (cookies/sessions).

---

### 2. Client-Server Model

- **Client:** Browser or app initiating requests.  
- **Server:** Hosts resources, processes requests, and sends responses.  

🔍 REST, GraphQL, and gRPC all extend this same model, with different communication styles.

---

### Underlying Transport Protocol

- HTTP relies on **TCP**, which ensures reliable delivery with a 3-way handshake.
- HTTPS adds **TLS encryption**.

- HTTP/3 is built on **QUIC over UDP**, where encryption is **mandatory** and handshakes are faster.

---

## HTTP Versions and Evolution

- **HTTP 1.0:** New connection per request.  
- **HTTP 1.1:** Persistent connections, chunk transfer, caching.  
- **HTTP 2.0:** Multiplexing, binary framing, HPACK compression, server push.  
- **HTTP 3.0:** QUIC over UDP, no head-of-line blocking, faster handshakes.

- HTTP/2 suffers from **head-of-line blocking** at the TCP level. QUIC (HTTP/3) fixes this by allowing independent streams.

---

## HTTP Messages

Two types:
- **Request:** Sent by client.  
- **Response:** Sent by server.  

**Structure:**  
- HTTP Version  
- Headers  
- Blank line  
- Body  

**Requests include:** Method, URL, Host.  
**Responses include:** Status Code, optional Reason Phrase.

🔍  
- Requests often include **query parameters** (`?id=123`).  
- Reason phrases (e.g., `200 OK`) are optional in HTTP/2+.

---

## HTTP Headers

Headers = metadata (key-value pairs).  

💡 **Analogy:** Like an envelope on a parcel — lets handlers route it without opening the package.

### Types
1. **Request Headers** (from client)  
    - `User-Agent`, `Authorization`, `Accept`  

2. **General Headers** (both directions)  
    - `Date`, `Cache-Control`, `Connection`  

3. **Representation Headers** (about body)  
    - `Content-Type`, `Content-Length`, `Content-Encoding`, `ETag`  

4. **Security Headers**  
    - `HSTS`, `CSP`, `X-Frame-Options`, `X-Content-Type-Options`, `Set-Cookie`  

🔍  
- **Custom headers** often use `X-` prefix (e.g., `X-Request-ID`).  
- **Hop-by-hop vs end-to-end headers:** Some (like `Connection`) are only for one connection segment and aren’t forwarded.

---

## HTTP Methods

- **GET:** Fetch data (safe, idempotent).  
- **POST:** Create resource (not idempotent).  
- **PATCH:** Partial update.  
- **PUT:** Full replacement.  
- **DELETE:** Delete resource.  
- **OPTIONS:** Query server capabilities (often in CORS).  
- **HEAD:** Same as GET, but no body (useful for metadata).  
- **TRACE:** Echo request (debugging).  

### Idempotence
- **Idempotent:** GET, PUT, DELETE.  
- **Non-idempotent:** POST.  

---

## CORS (Cross-Origin Resource Sharing)

### Same-Origin Policy
Browsers restrict scripts from making cross-origin requests. CORS defines safe exceptions.

### Flows
1. **Simple requests:** GET/POST/HEAD with simple headers.  
2. **Pre-flight requests:** OPTIONS first, then actual request (for custom headers, PUT/DELETE, etc.).

- CORS is **only enforced by browsers**. Tools like `curl` or backend services ignore it.  

💡 **Analogy:** CORS is like a **bouncer at a club** — even if the band says “all welcome,” the bouncer (browser) decides who gets in.

---

## HTTP Status Codes

Categories:  
- **1xx:** Informational (`100 Continue`, `101 Switching Protocols`)  
- **2xx:** Success (`200 OK`, `201 Created`, `204 No Content`)  
- **3xx:** Redirection (`301`, `302`, `304`)  
- **4xx:** Client errors (`400`, `401`, `403`, `404`, `405`, `409`, `429`)  
- **5xx:** Server errors (`500`, `501`, `502`, `503`, `504`)  
- `422 Unprocessable Entity` (common in APIs for validation errors).

---

## HTTP Caching

- **Initial GET:** Server returns resource + headers (`Cache-Control`, `ETag`, `Last-Modified`).  
- **Subsequent GETs:** Client uses `If-None-Match` / `If-Modified-Since`.  
- **Server:** Replies `304 Not Modified` or `200 OK` with new content.  

- `Vary` header tells caches which request headers matter (e.g., `Vary: Accept-Encoding`).  

💡 **Analogy:** Like leftovers in a fridge. Before cooking, you check: “Is it expired? Or still good (304 Not Modified)?”

---

## Content Negotiation

Client and server agree on format/language/encoding.  

- **Media Type:** `Accept: application/json`  
- **Language:** `Accept-Language: en`  
- **Encoding:** `Accept-Encoding: gzip, br`  

- Supports **q-values** (weights):  
  ```http
  Accept: text/html;q=0.8, application/json;q=1.0
  ```

---

## Persistent Connections

- HTTP/1.0: One TCP connection per request.  
- HTTP/1.1: Persistent by default (`keep-alive`).  
- `Connection: close` explicitly shuts down.  

🔍 HTTP/2 multiplexing = multiple streams per one connection.

---

## Handling Large Requests/Responses

1. **Large Request (Uploads):** `multipart/form-data` with boundary.  
2. **Large Response (Streaming):** Chunked transfer, `text/event-stream`, persistent connection.

🔍 WebSockets & SSE are common for **real-time streaming**.

---

## SSL / TLS / HTTPS

- **SSL:** Old, insecure.  
- **TLS:** Modern, secure (TLS 1.3 is latest).  
- **HTTPS:** HTTP over TLS.  

🔍 TLS provides **three guarantees**:  
1. Confidentiality (encryption)  
2. Integrity (tamper-proof)  
3. Authentication (verify server identity)  

💡 **Analogy:** Visiting a bank. TLS is the private glass room — outsiders know you went to the bank, but not what you said.  
