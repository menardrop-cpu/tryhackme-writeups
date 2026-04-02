# Client-Server 

**Platform** : TryHackMe  
**Module** : Computer Fundamentals  

---

## Objective

Understand the client-server model and the core concepts that make networked computing possible: DNS, protocols, ports, and HTTP communication.

---

## Task 1 — Introduction

Before networks existed, computers worked in isolation. They stored their own files, ran their own programs, and did not communicate with other systems. As organizations began connecting systems, specialization emerged: some machines became service providers, others became consumers of those services. This is the origin of the client-server model.

Key precursor networks: ARPANET, CYCLADES, NPL, NSFNET.

---

## Task 2 — The Pizza Delivery Analogy

### Core concepts mapped to the pizza analogy

| Computer concept | Pizza analogy |
|---|---|
| Client | Alice — initiates the request |
| Server | Luigi's Pizza — fulfills the request |
| Request | Order: "Large pepperoni and a coke" |
| Response | The pizza delivered back |
| Protocol | The shared language and ordering rules |
| Port | The specific door used for takeaway vs dine-in vs delivery |
| DNS | The GPS — converts a name into a location (IP address) |

### Key rules

* **The client always initiates the request** — the server never reaches out first
* If the request is malformed or the resource is unavailable, the server returns an error response
* A single server can run multiple services simultaneously, each on a different port

### Concepts defined

**Protocol**  
Defines how a client and server communicate. Specifies:
* Which commands both sides understand (e.g. GET)
* How a request is structured
* What syntax is used
* What response to give to valid and invalid requests

**Port**  
Identifies a specific service running on a server. Each service listens on a dedicated port. A single server can run multiple services, each on a different port.

**DNS (Domain Name System)**  
Translates a human-readable domain name into an IP address — the actual network location of the server. Works like a GPS: you provide a name, it returns coordinates.

**IP Address**  
The network address of a server. Equivalent to a postal address for computer systems.

### Task answers

**Q: What do we use to identify a specific service on a server?**  
`Port`

**Q: What do we call the address of a server?**  
`IP address`

---

## Task 3 — Web Communication in Practice

### HTTP(S)

HTTP (Hypertext Transfer Protocol) is the protocol used for web communication. HTTPS is the secure version (encrypted via TLS).

Key characteristic: **stateless** — each request is processed independently. The server retains no memory of previous requests.

### Statefulness in practice

Modern websites introduce statefulness at the application level to work around HTTP's stateless nature:

```
1. User submits login credentials (POST request)
2. Server authenticates the user
3. Server creates a session identifier
4. Session ID is stored in a cookie or token
5. Browser sends the session ID with every subsequent request
6. Server recognises the session → user stays logged in
```

Without this mechanism, the user would need to re-authenticate on every single request.

### HTTP Methods

| Method | Purpose |
|---|---|
| GET | Retrieve a resource from the server |
| POST | Send data to the server (forms, login...) |
| PUT | Create or replace a resource |
| DELETE | Remove a resource |
| PATCH | Partially modify a resource |
| HEAD | Retrieve headers only, no body |
| OPTIONS | List the methods supported by the server |
| CONNECT | Establish a tunnel (used for HTTPS proxies) |
| TRACE | Diagnostic — echoes the request back |

Primary focus: **GET** — the most common method, used every time a browser loads a page.

### Anatomy of a GET request

When a user types a URL, the browser automatically constructs a GET request:

```
GET / HTTP/1.1
Host: httpdemo.local:8080
```

Key fields visible in browser developer tools (F12 → Network tab):

| Field | Description | Example |
|---|---|---|
| Scheme | Protocol used | HTTP / HTTPS |
| Host | Target server name | httpdemo.local |
| Filename | Requested resource | `/` → index.html |
| Address | Server IP address | 127.0.0.1 |
| Status | Request result | 200 OK |

### HTTP Response structure

A server response is divided into two parts:

**Response Header** — metadata about the response:
* Status code
* Content type
* Date
* Cookies
* Cache directives

**Response Body** — the actual requested content:
* HTML for a webpage
* JSON for an API
* Binary data for a file download

### HTTP Status Codes

| Code | Meaning |
|---|---|
| 200 OK | Request successful |
| 301 / 302 | Redirect (permanent / temporary) |
| 400 Bad Request | Malformed request |
| 403 Forbidden | Access denied |
| 404 Not Found | Resource does not exist |
| 500 Internal Server Error | Server-side error |

---

## Full Request Flow Summary

```
User types URL in browser
        ↓
DNS resolves domain name → IP address
        ↓
Browser connects to server IP on correct port (80 or 443)
        ↓
Browser sends GET request
        ↓
Server processes request
        ↓
Server returns response (header + body)
        ↓
Browser renders the page
```

---

## Security Relevance

| Concept | Security consideration |
|---|---|
| HTTP vs HTTPS | HTTP transmits data in plaintext — credentials and session tokens exposed to interception. Always enforce HTTPS. |
| Stateless protocol | Session tokens become high-value targets — theft of a session cookie = account takeover without needing credentials |
| GET requests | Parameters in GET requests appear in URLs and server logs — never send sensitive data via GET |
| POST requests | Data sent in request body — less visible but still requires encryption (HTTPS) |
| Status codes | 403 and 404 responses can reveal application structure to attackers during reconnaissance |
| Ports | Exposed ports = exposed attack surface — only necessary ports should be open and reachable |
