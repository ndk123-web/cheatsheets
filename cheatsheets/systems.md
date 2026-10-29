# System Design Cheatsheet

> **Study log — 28 October 2026**
>
> Beginner-friendly notes on System Design fundamentals, using a productivity application (TaskPlexus) as the running example. Each concept covers what it means, why it matters, practical examples, and engineering trade-offs.

## Contents

1. [What Is System Design?](#1-what-is-system-design)
2. [Functional vs. Non-Functional Requirements](#2-functional-vs-non-functional-requirements)
3. [Scalability](#3-scalability)
4. [Availability](#4-availability)
5. [Reliability](#5-reliability)
6. [Maintainability](#6-maintainability)
7. [Latency](#7-latency)
8. [Throughput](#8-throughput)
9. [Performance](#9-performance)
10. [Performance Trade-Offs](#10-performance-trade-offs)
11. [Latency Percentiles: p95 and p99](#11-latency-percentiles-p95-and-p99)
12. [Quick Revision](#12-quick-revision)
13. [Networking & Web Communication](#13-networking--web-communication)
    - [HTTP Deep Dive](#131-http-deep-dive)
    - [TCP vs. UDP](#132-tcp-vs-udp)
    - [DNS](#133-dns-domain-name-system)
    - [TLS and HTTPS](#134-tls-and-https)
    - [Sockets and Keep-Alive](#135-sockets-and-keep-alive)
    - [Reverse Proxies](#136-reverse-proxies)
    - [Load Balancers](#137-load-balancers)
    - [Complete Request–Response Lifecycle](#138-complete-requestresponse-lifecycle)
14. [References](#14-references)
15. [Concurrency Control and Resilience Patterns](#15-concurrency-control-and-resilience-patterns)

---

## 1. What Is System Design?

**System design** is the process of deciding how software components are organized and interact to meet functional requirements and non-functional constraints under real-world workloads and failures.

It covers architecture, APIs, databases, networking, caching, scaling, failure handling, security, and operational trade-offs.

### Running example: TaskPlexus

Imagine a productivity app where users manage tasks, goals, analytics, and AI-generated plans.

A simplified architecture might be:

```text
React / Next.js Client
        |
     HTTPS
        |
   Go Backend API
      /      \
PostgreSQL  Redis
(persistent) (cache)
```

- **Client:** Displays the interface and sends requests.
- **Backend API:** Authenticates requests and applies business logic.
- **PostgreSQL:** Stores durable application data.
- **Redis:** May cache frequently accessed data or store temporary state.

This is illustrative, not a prescription. Add components when requirements justify their cost and complexity.

### The core questions

- What must the system do?
- How well must it do it?
- What workload must it support?
- What happens when a component fails?
- What trade-offs are acceptable?

---

## 2. Functional vs. Non-Functional Requirements

### 2.1 Functional requirements (FR)

**What:** Define the features and behaviors the system must provide.

**Plain English:** What should the application do?

Examples for TaskPlexus:

- A user can register and log in.
- A user can create, update, delete, and complete tasks.
- A user can organize tasks into projects.
- A user can generate an AI-powered plan.
- A user can view task-completion analytics.

A functional requirement often translates into API behavior, business logic, validation, and data operations.

Example endpoint:

```http
POST /api/tasks
Content-Type: application/json

{
  "title": "Study System Design"
}
```

A backend might authenticate the user, validate the title, save the task in PostgreSQL, and return the created resource.

### 2.2 Non-functional requirements (NFR)

**What:** Define quality attributes, measurable service targets, and constraints under which the system must operate.

**Plain English:** How well should the system work?

Examples:

- The task-list endpoint should meet a defined latency target.
- The service should remain available during expected failures.
- Users must not access another user's tasks.
- The system should support increasing traffic.
- Developers should be able to add features safely.

Security, privacy, and data integrity may involve both functional behavior and non-functional constraints; the distinction is useful rather than absolute.

### 2.3 Make requirements measurable

Vague: “The API should be fast.”

Better: “The task-list API should have p95 latency below 300 ms at 1,000 requests per second for a specified dataset and deployment configuration.”

A measurable requirement can be tested and monitored. Targets should be chosen based on user needs and realistic capacity tests.

**Design principle:** Establish requirements before choosing technologies or architecture.

---

## 3. Scalability

**What:** The ability of a system to handle increased workload by adding resources or changing the architecture while maintaining acceptable service levels.

### Plain-English example

A chai shop serving 10 customers per hour may need one worker. At 100 customers per hour, it might need additional staff and counters. A backend faces similar growth in requests, database queries, jobs, and memory use.

### 3.1 Vertical scaling (scale up)

Increase the capacity of one machine: more CPU, RAM, or faster storage.

Example: Upgrade a backend server from 2 CPU cores and 4 GB RAM to 8 CPU cores and 32 GB RAM.

**Advantages**
- Relatively simple architecture.
- Fewer distributed-system coordination problems.

**Limitations**
- Hardware and provider limits.
- Costs can rise.
- A single machine may remain a single point of failure.

### 3.2 Horizontal scaling (scale out)

Add more server instances and distribute incoming requests.

```text
              Clients
                 |
            Load Balancer
           /      |      \
       Server A Server B Server C
           \      |      /
            Shared Database
```

**Advantages**
- Can add capacity beyond one machine.
- Can improve resilience when health checks and failover are correctly configured.

**Limitations**
- More operational and network complexity.
- Shared database or other dependencies can become bottlenecks.
- Instances must be designed for distributed execution (for example, avoid relying on local in-memory session state).

Adding three servers does not guarantee three times the capacity. Measure the actual bottleneck.

### 3.3 When should we scale?

Monitor:
- CPU and memory utilization.
- Requests per second.
- Latency percentiles.
- Error rates.
- Database query latency and connection usage.
- Queue depth and processing delay.

Scale when measurements show the workload is threatening service objectives, not just because the user count increased.

---

## 4. Availability

**What:** The proportion of time a service is operational and able to deliver its intended function.

**Plain-English example:** A library scheduled to open for eight hours but unexpectedly closed for one hour has 87.5% availability during that period.

A simplified formula is:

```text
Availability = Uptime / Total Scheduled Time × 100%
```

In production, define availability using a service-level indicator (SLI). A server process might be running but still count as unavailable if requests fail.

### Common availability targets

Assuming a 365-day year:

| Availability | Approximate downtime per year |
|---|---:|
| 99% | 3 days, 15 hours, 36 minutes |
| 99.9% | 8 hours, 46 minutes |
| 99.99% | 52 minutes, 34 seconds |
| 99.999% | 5 minutes, 15 seconds |

These are mathematical estimates, not guarantees.

### How to improve availability

- Run multiple backend instances.
- Use health checks and route around unhealthy instances.
- Consider separate failure domains for important services.
- Configure timeouts and bounded retries.
- Back up data and test restoration.
- Monitor failures and alert on service-level objectives.

### Single point of failure (SPOF)

A SPOF is a component whose failure can make a larger service unavailable.

Two backend instances do not guarantee availability if both depend on one database that has no suitable recovery or failover strategy. Consider all critical dependencies, including databases, networks, DNS, identity providers, and third-party APIs.

**Key distinction:** Availability asks whether the service can be used when needed. It does not, on its own, prove that operations are correct.

---

## 5. Reliability

**What:** The ability of a system to perform its required functions correctly and dependably over time, including during expected operating conditions and failures.

### Example

A payment service may be reachable all day but occasionally debit a customer twice. It can have high availability while having a serious reliability problem.

For TaskPlexus, a reliable task update should:
- Save the correct data.
- Enforce authorization.
- Avoid losing committed changes.
- Handle retries safely.
- Return accurate outcomes.

### Reliability techniques

- Database transactions.
- Input validation and authorization.
- Automated tests and monitoring.
- Backups and tested recovery.
- Timeouts, bounded retries, and circuit breakers.
- Idempotency for operations that may be retried.

### Idempotency example

If a client sends a payment request and times out, it may not know whether the payment succeeded. A retry must not accidentally create a second charge.

An idempotency key uniquely identifies the intended operation. The service stores the result associated with that key and returns that result for subsequent requests using the same key.

Illustrative Go pseudocode:

```go
func ProcessPayment(idempotencyKey string) error {
    // 1. Begin a database transaction.
    // 2. Look up the idempotency key.
    // 3. If already processed, return its stored result.
    // 4. Otherwise, perform the operation.
    // 5. Persist the result with a unique constraint.
    // 6. Commit the transaction.
    return nil
}
```

This is not production-ready payment code. Real implementations must handle concurrent requests, transaction boundaries, provider-side idempotency, and uncertain outcomes.

**Memory aid:** Availability = accessible. Reliability = correct and dependable.

---

## 6. Maintainability

**What:** How easily engineers can understand, test, debug, repair, and change the system.

**Plain-English example:** A bike with clearly labeled parts and standard screws is easier to repair than one with tangled wiring and undocumented modifications.

### Why it matters

New TaskPlexus features—recurring tasks, reminders, analytics, or AI planning—should not require rewriting unrelated functionality.

Poor separation:

```go
func HandleRequest() {
    // Parse HTTP request
    // Authenticate user
    // Validate task
    // Query database
    // Call AI service
    // Generate analytics
    // Format response
}
```

A more maintainable structure might be:

```text
cmd/server/main.go

internal/
  handler/task_handler.go
  service/task_service.go
  repository/task_repository.go
  model/task.go
```

- **Handler:** HTTP request and response handling.
- **Service:** Business rules.
- **Repository:** Persistence operations.
- **Model:** Data structures.

This separation makes testing individual responsibilities easier.

### How to improve maintainability

- Use clear names and modular code.
- Separate responsibilities.
- Add automated tests.
- Maintain useful documentation and logs.
- Use consistent formatting and code review.
- Keep the architecture as simple as requirements allow.

More layers and more microservices do not automatically improve maintainability. Unnecessary abstraction can make a small system harder to understand.

---

## 7. Latency

**What:** The elapsed time required for an operation or request to complete, measured across a clearly defined boundary.

**Plain English:** How long does one request take?

Example: The TaskPlexus client requests a task list and receives the response 140 ms later. The measured request latency is 140 ms, assuming timing starts at request receipt and ends at response completion.

### Example breakdown

| Component | Time |
|---|---:|
| Network overhead | 30 ms |
| Backend processing | 10 ms |
| Database query | 80 ms |
| Response transfer | 20 ms |
| **Total (simplified sequential example)** | **140 ms** |

Actual timings depend on queuing, concurrency, network behavior, and overlapping work.

### How to reduce latency

- Add appropriate database indexes.
- Remove unnecessary queries and network round trips.
- Cache frequently read data when freshness rules permit.
- Optimize expensive computations.
- Use pagination for large datasets.
- Execute independent work concurrently when safe.

### Average latency vs. percentile latency

The average alone can hide slow requests. Percentiles help reveal the tail of the latency distribution. See [Section 11: Latency Percentiles (p95 and p99)](#11-latency-percentiles-p95-and-p99).

---

## 8. Throughput

**What:** The amount of work completed per unit of time.

Common units:
- Requests per second (RPS).
- Transactions per second (TPS).
- Messages processed per second.
- Megabytes per second.

**Plain-English example:** If a cashier serves 10 customers per minute, the service throughput is 10 customers per minute.

### Example

If TaskPlexus handles 2,000 requests per second, its observed throughput is 2,000 RPS under that workload.

If incoming traffic rises above what the system can process, requests may wait in queues, time out, or be rejected.

### Ways to improve throughput

- Scale backend instances when work can be parallelized.
- Optimize database queries.
- Batch operations when the workload permits.
- Use database connection pools efficiently.
- Cache repeated reads.
- Use durable queues for appropriate background work.

### Latency and throughput are different

- **Latency:** How long does one request take?
- **Throughput:** How many requests complete per second?

A restaurant can serve many customers per hour while individual customers still experience long waits.

### Little’s Law

For a stable system, Little’s Law relates averages:

```text
L = λ × W
```

Where:
- (L) = average number of jobs or requests in the system.
- (λ) = average completion/arrival rate.
- (W) = average time spent in the system.

Example: At 200 requests/second and an average time in the system of 0.1 seconds:

```text
L = 200 × 0.1 = 20 requests
```

On average, 20 requests are in the system, either waiting or being processed. The relationship assumes suitable stability conditions and uses consistent measurement boundaries.

---

## 9. Performance

**What:** How effectively a system operates under a defined workload, considering latency, throughput, resource usage, and behavior under load.

### Example measurements

Suppose two implementations are tested under the same workload:

| Metric | Implementation A | Implementation B |
|---|---:|---:|
| Average latency | 100 ms | 400 ms |
| Throughput | 500 RPS | 500 RPS |
| CPU utilization | 45% | 90% |
| Error rate | 0.1% | 2% |

In this illustrative test, implementation A performs better across the listed metrics. Meaningful comparisons require equivalent hardware, datasets, workloads, and test conditions.

### Performance optimization loop

1. **Measure:** Record latency percentiles, throughput, CPU, memory, and errors.
2. **Find the bottleneck:** Determine whether application code, database, network, or another dependency is responsible.
3. **Optimize:** Change the component causing the bottleneck.
4. **Measure again:** Compare under the same workload.

For example, if a task-list query performs a full table scan, adding more Go servers may not fix the root cause. An appropriate database index or query improvement may be more effective.

**Principle:** Measure and profile before optimizing.

---

## 10. Performance Trade-Offs

System design involves choices where improving one quality can make another quality harder or more expensive to achieve. There is rarely a universally best option.

### 10.1 Caching vs. data freshness

A cache can speed up repeated reads and reduce database load, but it may return stale data unless invalidation or consistency is handled correctly.

| Without cache | With cache |
|---|---|
| Often simpler freshness behavior | Faster repeated reads |
| More database queries | Fewer database queries |
| Database can become a bottleneck | Cache invalidation adds complexity |

**Decision:** Cache when the performance benefit justifies the complexity and the allowed staleness is explicit.

### 10.2 Availability vs. strong consistency

Suppose a distributed database replicates data between locations. During a network partition, the locations may be unable to communicate.

Depending on the system and operation, it may:
- Reject or delay writes to preserve stronger consistency guarantees.
- Accept certain operations locally and reconcile replicas later, allowing temporary stale reads or conflicts.

The CAP theorem concerns trade-offs between consistency and availability during a network partition. It does not mean every database must choose one property permanently in all circumstances; actual behavior depends on the operation and architecture.

### 10.3 Reliability vs. cost

Redundant servers, failover, monitoring, and tested recovery can improve resilience, but they cost money and introduce operational complexity.

Choose an appropriate level of resilience based on the consequences of downtime and data loss.

### 10.4 Latency vs. throughput

Batching 100 inserts may improve throughput by reducing per-record overhead, but an individual record may wait for the batch to fill. Batching may also complicate error handling and retries.

The appropriate design depends on workload, batch size, and latency targets.

### 10.5 Maintainability vs. premature complexity

A small application may only need one backend and one relational database. Introducing many microservices, a message broker, and a complex orchestration platform without a real need increases deployment, debugging, networking, and data-consistency costs.

**Core principle:** Choose the simplest architecture that meets the actual requirements, and evolve it when evidence justifies the change.

---

## 11. Latency Percentiles: p95 and p99

Percentiles describe the distribution of response times. They help reveal slow requests that averages can hide.

- **p95 latency:** Approximately 95% of requests completed at or below this response time; approximately 5% took longer.
- **p99 latency:** Approximately 99% of requests completed at or below this response time; approximately 1% took longer.

Exact values depend on the percentile convention, sample size, and measured population.

### Example: 100 task-list requests

Imagine the sorted response times are:
- 95 requests take 100 ms each.
- 4 requests take 200 ms each.
- 1 request takes 2,000 ms.

Using the nearest-rank method:
- p95 = 100 ms.
- p99 = 200 ms.
- Maximum = 2,000 ms.

Why isn't p99 2,000 ms? Because p99 is the 99th percentile, not necessarily the slowest request. The maximum is the slowest observed request.

| Metric | What it tells us | Example |
|---|---|---:|
| Average | Mean latency across requests | 123 ms |
| p95 | Threshold for the slower 5% | 100 ms |
| p99 | Threshold for the slower 1% | 200 ms |
| Maximum | Slowest observed request | 2,000 ms |

**Calculation check:** The average in this example is ((95×100 + 4×200 + 1×2000)/100 = 123) ms.

### Why monitor p95 and p99?

A low average can conceal tail latency. p95 is useful for tracking slower user experiences, while p99 highlights more extreme slowdowns.

**Interview answer:** “p95 is the response time at or below which approximately 95% of requests complete. p99 is the corresponding threshold for approximately 99% of requests. We monitor them to understand tail latency that an average can hide.”

---

## 12. Quick Revision

| Concept | Key question |
|---|---|
| Functional requirements | What must the system do? |
| Non-functional requirements | How well must it do it and under what constraints? |
| Scalability | Can it handle growth? |
| Availability | Is the service usable when needed? |
| Reliability | Does it work correctly and dependably? |
| Maintainability | How easily can it be changed and repaired? |
| Latency | How long does an operation take? |
| Throughput | How much work completes per unit of time? |
| Performance | How effectively does it operate under a defined workload? |
| Trade-offs | What might we sacrifice to improve something else? |
| p95 / p99 | What are the latency thresholds for the slower 5% / 1% of requests? |

### One API, multiple design questions

For a task-list API:
- **Functional:** Can a user retrieve their own tasks?
- **Non-functional:** What latency, error-rate, and security targets apply?
- **Scalability:** Can it handle significantly more users and requests?
- **Availability:** Can traffic be served if one backend instance fails?
- **Reliability:** Are tasks returned correctly and failures handled safely?
- **Maintainability:** Can task logic evolve without breaking unrelated features?
- **Latency:** How long does an individual request take?
- **Throughput:** How many requests can complete per second?
- **Trade-offs:** Can response time improve without unacceptable cost, staleness, or complexity?


---

## 13. Networking & Web Communication

This section connects the protocols and infrastructure components involved when a browser communicates with a backend. The running example is a client requesting `GET /posts` from a Node.js API.

### 13.1 HTTP Deep Dive

**What:** HTTP (Hypertext Transfer Protocol) is an application-layer protocol that defines request and response semantics, including methods, headers, status codes, and message framing.

**Why it matters:** TCP transports bytes but does not understand application concepts such as `GET`, `POST`, `404 Not Found`, or JSON. HTTP supplies those semantics.

**Typical HTTP request:**

```http
POST /posts HTTP/1.1
Host: localhost:3000
Content-Type: application/json
Accept: application/json

{"title":"Learning System Design"}
```

A request has a start line, headers, a blank line, and an optional body. `Content-Type` describes the media type of the body being sent; `Accept` describes response media types the client can accept.

HTTP message framing depends on the HTTP version. In HTTP/1.1, body length can be signaled by `Content-Length` or transfer coding such as chunked encoding. TCP is a byte stream: one read does not necessarily equal one HTTP message, and headers/body need not map to separate TCP packets.

**Common methods:**

| Method | Typical use | Idempotent by HTTP semantics? |
|---|---|---|
| `GET` | Retrieve a resource | Yes |
| `HEAD` | Retrieve headers without response content | Yes |
| `POST` | Submit data or create/process a resource | Not generally |
| `PUT` | Replace a resource representation | Yes |
| `PATCH` | Partially modify a resource | Depends on the operation |
| `DELETE` | Remove a resource | Yes |

Idempotency means repeating an operation has the same intended effect as performing it once. A repeated `DELETE` may return a different status code while the final resource state remains unchanged. For operations such as payments or order creation, idempotency keys can help clients safely retry after uncertain outcomes.

**Common status codes:**

- `200 OK`: request succeeded.
- `201 Created`: a resource was created.
- `204 No Content`: request succeeded without response content.
- `400 Bad Request`: request is invalid.
- `401 Unauthorized`: valid authentication is missing or was not accepted.
- `403 Forbidden`: the server refuses the operation.
- `404 Not Found`: the resource or route was not found.
- `500 Internal Server Error`: unexpected server failure.

**HTTP versions:**

| Version | Transport | Key behavior |
|---|---|---|
| HTTP/1.1 | TCP | Persistent connections; request/response exchange rules |
| HTTP/2 | TCP | Multiplexes multiple streams on one connection; uses HPACK header compression |
| HTTP/3 | QUIC over UDP | Multiplexed streams; avoids TCP-level cross-stream head-of-line blocking; uses QPACK |

HTTP/2 still runs over TCP, so loss of a TCP segment can temporarily block delivery of later bytes on that connection. QUIC's separate streams avoid that specific cross-stream blocking behavior, though network loss can still add latency.

**Node.js example:**

```javascript
const http = require("node:http");

const server = http.createServer((req, res) => {
    if (req.method === "GET" && req.url === "/posts") {
        res.writeHead(200, {
            "Content-Type": "application/json"
        });
        res.end(JSON.stringify({ posts: [] }));
        return;
    }

    res.writeHead(404, {
        "Content-Type": "application/json"
    });
    res.end(JSON.stringify({ error: "Route not found" }));
});

server.listen(3000);
```

The `node:http` module manages HTTP parsing and its underlying transport sockets. It is not the same as creating a raw TCP server with `node:net`.

**References:** [MDN: HTTP overview](https://developer.mozilla.org/en-US/docs/Web/HTTP/Overview), [RFC 9110: HTTP Semantics](https://www.rfc-editor.org/rfc/rfc9110), [RFC 9112: HTTP/1.1](https://www.rfc-editor.org/rfc/rfc9112), [RFC 9113: HTTP/2](https://www.rfc-editor.org/rfc/rfc9113), [RFC 9114: HTTP/3](https://www.rfc-editor.org/rfc/rfc9114), [Node.js HTTP documentation](https://nodejs.org/api/http.html).

### 13.2 TCP vs. UDP

**TCP (Transmission Control Protocol)** provides a reliable, ordered, full-duplex byte stream between endpoints. It uses connection state, acknowledgements, retransmissions, flow control, and congestion control.

**UDP (User Datagram Protocol)** sends datagrams without a TCP-style connection handshake. UDP does not guarantee delivery, ordering, or duplicate suppression. Applications can add their own reliability if required.

| Property | TCP | UDP |
|---|---|---|
| Connection setup | TCP handshake | No TCP-style handshake |
| Delivery | Reliable stream while the connection remains viable | Not guaranteed |
| Ordering | Preserved for bytes delivered to the application | Not guaranteed |
| Message boundaries | Not preserved | Datagram boundaries preserved |
| Common uses | HTTP/1.1, HTTP/2, database connections | DNS, real-time media, QUIC transport |

**TCP three-way handshake:** client sends SYN, server replies SYN-ACK, and client sends ACK. This synchronizes sequence numbers and establishes transport connection state.

**Closing and failures:** A graceful TCP close normally uses FIN/ACK exchanges. FIN closes one direction; the other direction may remain usable until it is closed. If the network disappears unexpectedly, a peer may not receive FIN and can temporarily retain half-open connection state. Retransmission timeouts, TCP keepalive probes, and application deadlines/heartbeats can help detect failures. An application timeout must be handled by the application; it does not automatically guarantee that every resource is released.

**Node.js raw TCP server:**

```javascript
const net = require("node:net");

const server = net.createServer((socket) => {
    socket.on("data", (buffer) => {
        console.log(buffer.toString());
        socket.write("Reply from server");
    });

    socket.on("end", () => console.log("Peer ended its sending direction"));
    socket.on("error", console.error);
});

server.listen(4000);
```

TCP is a byte stream, so a `data` event is not guaranteed to correspond to one application message. Custom protocols need framing, such as a length prefix or a well-defined delimiter.

**Node.js UDP uses `node:dgram`:**

```javascript
const dgram = require("node:dgram");
const socket = dgram.createSocket("udp4");

socket.on("message", (message, remote) => {
    console.log(message.toString(), remote.address, remote.port);
});

socket.bind(4000);
```

**References:** [RFC 9293: TCP](https://www.rfc-editor.org/rfc/rfc9293), [RFC 768: UDP](https://www.rfc-editor.org/rfc/rfc768), [Node.js Net documentation](https://nodejs.org/api/net.html), [Node.js Datagram (UDP) documentation](https://nodejs.org/api/dgram.html).

### 13.3 DNS (Domain Name System)

**What:** DNS is a distributed naming system that returns records associated with domain names. A and AAAA records commonly provide IPv4 and IPv6 addresses.

**Why:** People use hostnames instead of memorizing IP addresses, and service operators can change infrastructure without requiring users to change the hostname they enter.

**Simplified resolution flow:**

1. The browser and operating system check applicable caches and local configuration.
2. A recursive resolver checks its cache or performs lookups on the client's behalf.
3. If necessary, the resolver follows referrals through root and top-level-domain DNS infrastructure.
4. An authoritative nameserver provides the requested record or response.
5. The resolver returns the result, which may be cached according to its TTL.

This is not a mandatory sequence for every query: a cache may already contain the answer.

**Important record types:**

| Record | Purpose |
|---|---|
| `A` | IPv4 address |
| `AAAA` | IPv6 address |
| `CNAME` | Alias to another DNS name |
| `MX` | Mail-exchange servers |
| `TXT` | Text records used for verification and other policies |
| `NS` | Nameservers for a DNS zone |

**TTL (Time To Live):** A record's TTL tells caches how long the record may be reused before refreshing it. After a DNS change, some clients may continue using cached answers until those answers expire.

**Try it:** On Windows, run `nslookup example.com` or `nslookup -type=MX gmail.com`.

DNS resolution does not establish a TCP connection and does not encrypt HTTP traffic; those are separate steps.

**References:** [RFC 1034: DNS Concepts and Facilities](https://www.rfc-editor.org/rfc/rfc1034), [RFC 1035: DNS Implementation and Specification](https://www.rfc-editor.org/rfc/rfc1035), [Cloudflare: What is DNS?](https://www.cloudflare.com/learning/dns/what-is-dns/).

### 13.4 TLS and HTTPS

**What:** TLS (Transport Layer Security) protects data exchanged between endpoints. HTTPS is HTTP carried over TLS.

**Why:** TCP reliability does not provide confidentiality or cryptographic integrity. HTTPS helps protect application data against eavesdropping and tampering and authenticates the server when certificate validation succeeds.

TLS provides:
- **Confidentiality:** protected application data is encrypted.
- **Integrity:** unauthorized changes to protected records can be detected.
- **Authentication:** certificate validation helps the client verify the server's identity.

A certificate binds a hostname to a public key through a certificate chain. The browser validates the chain and hostname, along with relevant validity and trust conditions. A valid certificate does not prove that the website itself is honest or free of vulnerabilities.

**Simplified TLS 1.3 flow:**

1. Client sends a ClientHello with supported cryptographic parameters and key-share information.
2. Server responds with selected parameters, key-share information, and its certificate chain.
3. Client validates the certificate and hostname; the parties derive shared session keys.
4. Subsequent application data is protected using the negotiated keys.

For HTTP/1.1 and HTTP/2 over TLS, a typical new connection involves DNS resolution, a TCP handshake, a TLS handshake, and then HTTP. HTTP/3 uses QUIC over UDP and integrates TLS 1.3 into QUIC's connection setup.

**References:** [RFC 8446: TLS 1.3](https://www.rfc-editor.org/rfc/rfc8446), [MDN: Transport Layer Security (TLS)](https://developer.mozilla.org/en-US/docs/Web/Security/Transport_Layer_Security), [MDN: HTTPS](https://developer.mozilla.org/en-US/docs/Glossary/HTTPS).

### 13.5 Sockets and Keep-Alive

**What is a socket?** A socket is an operating-system communication endpoint exposed through an application API. A connected TCP socket is associated with connection state, including local/remote addresses and ports, sequence numbers, buffers, and retransmission state. It is a logical endpoint, not a dedicated physical cable.

**HTTP connection reuse:** Persistent HTTP connections allow multiple exchanges to reuse a connection when supported by the protocol and client/server configuration. Reuse reduces the overhead of repeatedly establishing connections.

**Do not confuse these concepts:**
- **HTTP persistence / connection reuse:** use a connection for multiple HTTP exchanges.
- **TCP keepalive probes:** probe an idle TCP connection to help detect an unreachable peer.
- **Application heartbeat:** application-level messages used to check whether a peer or service is responsive.

**Timeouts and cleanup:** Request/header timeouts limit how long clients may take to send a request. Keep-alive timeouts govern idle HTTP connections. Application deadlines bound work. TCP keepalive can help detect some unreachable peers. Behavior and defaults depend on the runtime and configuration; code must handle errors and release resources.

**References:** [RFC 9112: HTTP/1.1 connection management](https://www.rfc-editor.org/rfc/rfc9112), [Node.js HTTP documentation](https://nodejs.org/api/http.html), [Node.js Net documentation](https://nodejs.org/api/net.html).

### 13.6 Reverse Proxies

**What:** A reverse proxy accepts requests on behalf of backend servers and forwards them to an appropriate destination.

**Why:** It provides a stable public entry point and can handle routing, TLS termination, request limits, access logs, caching, and forwarding to internal services.

```text
Browser
   |
 HTTPS
   |
Reverse Proxy (e.g., Nginx)
   |
Internal API service
   |
Database / other dependencies
```

**Simplified Nginx example:**

```nginx
server {
    listen 80;
    server_name api.example.com;

    location / {
        proxy_pass http://127.0.0.1:3000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

This illustrative example listens for HTTP on port 80; a public deployment should configure HTTPS and certificate management. Forwarded headers must be trusted only when they come through trusted proxy infrastructure, because clients can otherwise spoof them.

A reverse proxy can also perform load balancing, but the terms describe different responsibilities.

**References:** [NGINX: Reverse Proxy](https://docs.nginx.com/nginx/admin-guide/web-server/reverse-proxy/), [NGINX: HTTPS server configuration](https://nginx.org/en/docs/http/configuring_https_servers.html).

### 13.7 Load Balancers

**What:** A load balancer distributes incoming requests or connections among eligible backend instances.

**Why:** It helps use multiple instances for horizontal scaling and can stop routing new traffic to instances that fail health checks.

**Common algorithms:**

| Algorithm | Behavior |
|---|---|
| Round robin | Cycles through eligible instances |
| Weighted round robin | Gives instances traffic according to configured weights |
| Least connections | Prefers an instance with fewer active connections |
| IP hash | Selects an instance using client IP information; NAT can concentrate clients on one instance |

**Health checks:** A health check evaluates whether an instance should receive traffic. A basic `/health` endpoint may show that the process responds, but it does not necessarily prove all dependencies are healthy. Liveness and readiness checks can have different purposes.

If one backend fails, the balancer may route new requests to healthy instances. In-flight requests are not automatically rescued; graceful draining, retries, and partial-failure handling need deliberate design.

**Nginx upstream example:**

```nginx
upstream taskplexus_api {
    server 127.0.0.1:3001;
    server 127.0.0.1:3002;
    server 127.0.0.1:3003;
}

server {
    listen 80;

    location /api/ {
        proxy_pass http://taskplexus_api;
    }
}
```

A load balancer can itself become a bottleneck or single point of failure if its own deployment and dependencies are not designed for availability.

**References:** [NGINX: HTTP Load Balancing](https://docs.nginx.com/nginx/admin-guide/load-balancer/http-load-balancer/), [AWS: What is a load balancer?](https://docs.aws.amazon.com/elasticloadbalancing/latest/userguide/what-is-load-balancing.html).

### 13.8 Complete Request–Response Lifecycle

For a new request to `https://api.taskplexus.app/posts`, a simplified flow is:

1. **URL parsing:** The browser identifies the scheme, hostname, port, and path.
2. **DNS:** The hostname is resolved to an address, unless a suitable cached answer is available.
3. **Transport setup:** For HTTP/1.1 or HTTP/2, TCP establishes a connection to the endpoint.
4. **TLS:** The browser validates the server certificate and establishes cryptographic keys.
5. **HTTP request:** The browser sends the method, path, headers, and optional body.
6. **Reverse proxy:** The public entry point receives the request and forwards it to a selected backend.
7. **Backend execution:** The application authenticates and authorizes the request, applies business logic, and may query a database.
8. **HTTP response:** The backend returns a status, headers, and body through the proxy.
9. **Client processing:** The browser reads the response and JavaScript can process the JSON.

This is a conceptual flow for a new HTTPS connection. Existing connections, caches, TLS session resumption, and protocol negotiation can change or skip some setup steps. HTTP/3 uses QUIC rather than TCP.

**Latency can come from** DNS lookup, connection setup, network round trips, proxy processing, backend queues, database queries, response transfer, or browser work. Measure each part before assuming the database is the bottleneck.

**Core distinctions to remember:**
- TCP transports bytes; HTTP defines application request/response semantics.
- DNS resolves names; it does not establish the transport connection.
- TLS provides security; TCP reliability alone does not.
- HTTP connection reuse and TCP keepalive probes are different mechanisms.
- Reverse proxies front services; load balancers distribute traffic. One product can do both.
- Horizontal scaling adds instances; a load balancer distributes traffic among eligible instances.

**References:** [MDN: How the web works](https://developer.mozilla.org/en-US/docs/Learn_web_development/Getting_started/Web_standards/How_the_web_works), [MDN: HTTP overview](https://developer.mozilla.org/en-US/docs/Web/HTTP/Overview), [RFC 9110: HTTP Semantics](https://www.rfc-editor.org/rfc/rfc9110).

## 14. References

These references provide additional detail on the concepts covered above.

1. **Google SRE Book — Service Level Objectives**  
   https://sre.google/sre-book/service-level-objectives/

2. **Google SRE Book — Monitoring Distributed Systems**  
   https://sre.google/sre-book/monitoring-distributed-systems/

3. **Google SRE Book — Handling Overload**  
   https://sre.google/sre-book/handling-overload/

4. **AWS Well-Architected Framework — Reliability Pillar**  
   https://docs.aws.amazon.com/wellarchitected/latest/reliability-pillar/welcome.html

5. **AWS Well-Architected Framework — Performance Efficiency Pillar**  
   https://docs.aws.amazon.com/wellarchitected/latest/performance-efficiency-pillar/welcome.html

6. **Martin Kleppmann — Designing Data-Intensive Applications**  
   https://dataintensive.net/

7. **CAP theorem discussion — Gilbert and Lynch (2002)**  
   https://doi.org/10.1145/564585.564601

---

**Next topic:** Capacity estimation — estimating users, requests per second, storage, and bandwidth before choosing an architecture.


---

## 15. Concurrency Control and Resilience Patterns

This section covers two different kinds of problems:

- **Concurrency control:** Preventing simultaneous operations from corrupting shared data.
- **Resilience:** Preventing a temporary failure in one service from spreading through the system.

### 15.1 Pessimistic vs. Optimistic Locking

Imagine an account has a balance of ₹1,000. Two requests arrive at nearly the same time: one tries to withdraw ₹800 and the other tries to withdraw ₹700. Both must not be allowed to spend the same starting balance.

#### Pessimistic locking: lock before modifying

The application assumes a conflicting update may occur and locks the relevant database row before reading or changing it. A competing transaction may have to wait.

PostgreSQL example:

```sql
BEGIN;

SELECT balance
FROM accounts
WHERE id = 101
FOR UPDATE;

-- Application validates that the balance is sufficient.
UPDATE accounts
SET balance = balance - 800
WHERE id = 101;

COMMIT;
```

`SELECT ... FOR UPDATE` locks the selected row until the transaction ends. The application must validate the balance and perform the update in the same transaction. In production, use a conditional update or appropriate isolation and validation so concurrent requests cannot produce an invalid balance.

**Use when:** Conflicting writes are likely and correctness requires serialized access, such as inventory allocation or account updates.

#### Optimistic locking: detect a conflict when saving

The application assumes conflicts are relatively uncommon. A version number is read with the data and checked when updating it.

Initial row: `balance = 1000, version = 1`.

```sql
UPDATE accounts
SET balance = balance - 800,
    version = version + 1
WHERE id = 101
  AND version = 1
  AND balance >= 800;
```

- **One row updated:** the update succeeded.
- **Zero rows updated:** the row was changed, the version was different, or the balance condition failed. Read the current state and revalidate before deciding whether to retry.

The conditional update is atomic. Do not blindly repeat a stale update; re-check business rules using the latest data.

**Use when:** Concurrent conflicts are relatively rare, such as editing a profile or document where a version conflict can be shown or safely resolved.

| Property | Pessimistic locking | Optimistic locking |
|---|---|---|
| Strategy | Acquire a lock before modifying | Check a version or condition at update time |
| Conflict behavior | A competing transaction may wait | An update may affect zero rows |
| Main cost | Lock contention and possible deadlocks | Conflict handling and possible retries |
| Good fit | Frequent conflicts / serialized updates | Infrequent conflicts |

### 15.2 Retry with Backoff

A retry repeats an operation after a failure that may be temporary, such as a transient network error or a selected service-unavailable response. Retrying permanent failures (for example, invalid input) is generally pointless.

Use **bounded retries** and exponential backoff instead of retrying continuously:

```text
Attempt 1 → fails
Wait ~1 second
Attempt 2 → fails
Wait ~2 seconds
Attempt 3 → fails
Wait ~4 seconds
Attempt 4 → stop if the retry limit is reached
```

The delays are illustrative. Real systems should configure maximum attempts, a maximum delay, an overall deadline, and often **jitter** (random variation in the delay) to prevent many clients from retrying simultaneously.

**Important:** A timeout does not prove the operation failed. For example, a payment provider may have charged the customer but the response may have been lost. Use an idempotency key or another deduplication strategy before retrying operations that create side effects.

### 15.3 Circuit Breaker Pattern

A circuit breaker protects a caller from repeatedly invoking a dependency that is failing. It does not repair the dependency; it limits repeated calls and gives the dependency time to recover.

#### Example: Order Service calling Payment Service

```text
Customer → Order Service → Circuit Breaker → Payment Service
```

Suppose Payment Service starts timing out. A configured circuit breaker observes failures within a time window. When its failure threshold is exceeded, it opens the circuit.

**State lifecycle:**

```text
CLOSED
  │ Requests flow normally; failures are monitored
  │ Failure threshold exceeded
  ▼
OPEN
  │ Calls fail fast / are blocked for a cooldown period
  │ Cooldown expires
  ▼
HALF-OPEN
  ├── Test request(s) succeed → CLOSED
  └── Test request(s) fail    → OPEN
```

- **CLOSED:** Calls are allowed; the breaker monitors outcomes.
- **OPEN:** Calls are short-circuited, avoiding repeated slow or failing calls.
- **HALF-OPEN:** A limited number of test calls are allowed to check whether the dependency recovered.

Thresholds, cooldowns, and the number of test calls depend on the implementation. Recovery is normally automatic: after the cooldown, the breaker enters half-open and returns to closed if test calls succeed. Engineers do not usually need to manually close it. Restarting the failed service and changing the circuit breaker's state are separate things.

#### What if the payment service is back online?

1. The circuit may remain open until its cooldown expires.
2. It transitions to half-open and allows limited test traffic.
3. If the tests succeed, normal traffic resumes in the closed state.
4. If they fail, it opens again and waits before another test.

A successful health check alone may not prove that the actual operation is working. The half-open test should be representative enough for the dependency and the configured breaker.

### 15.4 Retry vs. Circuit Breaker

| Retry | Circuit breaker |
|---|---|
| Repeats an individual failed operation | Stops repeated calls to a dependency after failures cross a threshold |
| Helps when a failure is transient | Helps prevent cascading failures and wasted work |
| Needs attempt limits, deadlines, and backoff | Uses states, failure criteria, and a recovery interval |
| Can increase load if poorly configured | Can fail fast while the dependency recovers |

They can be used together. Keep retries bounded, use backoff and jitter, respect the overall request deadline, and avoid retrying at multiple layers without a clear budget. Otherwise, retries can multiply traffic during an outage.

**Payment safety:** If a payment request times out, its outcome may be unknown. A retry should reuse the same idempotency key where supported; do not assume a timeout means no charge occurred. When a circuit is open, return a clear temporary failure or use an explicitly designed pending/fallback workflow—never report an unconfirmed payment as successful.

### 15.5 Quick Revision

- **Pessimistic locking:** Lock first; conflicting transactions may wait.
- **Optimistic locking:** Check a version/condition at write time; handle conflicts.
- **Retry:** Try a transiently failed operation again, with strict limits and backoff.
- **Circuit breaker:** Stop calling a repeatedly failing dependency temporarily.
- **Automatic recovery:** OPEN → HALF-OPEN after cooldown; success closes the circuit, failure opens it again.
- **Idempotency:** Makes repeating a logical operation safe against duplicate side effects when correctly implemented.

### References

- [PostgreSQL: Explicit Locking](https://www.postgresql.org/docs/current/explicit-locking.html)
- [PostgreSQL: UPDATE](https://www.postgresql.org/docs/current/sql-update.html)
- [Martin Fowler: Circuit Breaker](https://martinfowler.com/bliki/CircuitBreaker.html)
- [Microsoft Azure Architecture Center: Circuit Breaker pattern](https://learn.microsoft.com/en-us/azure/architecture/patterns/circuit-breaker)
- [AWS Builders' Library: Timeouts, retries, and backoff with jitter](https://aws.amazon.com/builders-library/timeouts-retries-and-backoff-with-jitter/)
