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
13. [References](#13-references)

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

## 13. References

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
