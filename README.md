# Services, Microservices, Netflix, and Industry Reversals (Research Notes)

This document summarizes what *services* are in software engineering, the major types of services, what *microservices* are and why they emerged, how Netflix adopted microservices, and examples of well-known companies that later **consolidated away from microservices** (often toward a **modular monolith** or fewer, larger services). 

A **service** is a software component that provides a **capability** to other components through a **well-defined interface** (a “contract”), usually over a network.

**Core characteristics**
- **Encapsulation:** consumers do not need to know internal implementation details.
- **Stable interface/contract:** e.g., REST/HTTP endpoints, gRPC methods, message topics.
- **Separation of concerns:** a service typically focuses on a specific responsibility.
- **Reusability:** multiple applications or modules can consume the same service.

---

## 2) Types of services (common classifications)

### A) By architecture and deployment style
- **Monolith (baseline comparison):** one deployable application that contains multiple features/modules.
- **SOA (Service-Oriented Architecture):** services are often larger-grained and historically integrated with middleware (e.g., ESB). Governance and centralized integration were common.
- **Microservices:** smaller services designed for independent deployment and scaling.

### B) By responsibility
- **Domain (business) services:** implement business capabilities (orders, payments, catalog).
- **Platform services:** authentication, service discovery, configuration, feature flags.
- **Data-oriented services:** data access and data APIs (use carefully to avoid creating “database wrapper” anti-patterns).
- **Integration services:** connectors/adapters to external providers and partner APIs.

### C) By communication pattern
- **Synchronous request/response:** REST, gRPC.
- **Asynchronous/event-driven:** queues and streams (pub/sub; Kafka-style systems).

---

## 3) What microservices are

**Microservices** are an architectural style where a system is composed of **many small, independently deployable services**, each aligned to a specific business capability.

**What typically distinguishes microservices**
- **Independent deployment:** each service can be released without redeploying the entire system.
- **Independent scaling:** scale only what needs it (e.g., search vs. billing).
- **Team ownership:** “you build it, you run it” is common in mature setups.
- **Decentralized data ownership:** services often own their schemas/datastores.
- **Operational requirements:** resilience, observability, and automated delivery are not optional at scale.

**Trade-off to recognize:**  
Microservices can improve organizational and scaling flexibility, but they also introduce **distributed-systems complexity** (network latency, partial failures, debugging across services, data consistency challenges).

---

## 4) How microservices emerged 

Microservices evolved from earlier distributed and service-based approaches:

- **UNIX modularity:** small composable components.
- **Distributed systems & components:** earlier middleware and RPC-style systems.
- **SOA (2000s):** normalized “service thinking,” but often became heavyweight (centralized governance, ESBs).
- **Cloud + DevOps (late 2000s–2010s):** made it practical to operate many deployables:
  - elastic infrastructure (public cloud)
  - containerization (Docker) and orchestration (Kubernetes)
  - CI/CD and progressive delivery
  - modern monitoring/logging/tracing stacks

The term **“microservices”** gained broad popularity in the early 2010s as large-scale companies publicly shared architectural lessons and tooling patterns.

---

## 5) Netflix background 

- **Founded:** 1997 (Reed Hastings and Marc Randolph)  
- **Initial model:** DVD-by-mail  
- **Streaming:** launched in 2007  
- **Scaling challenge:** rapid growth in streaming traffic, device diversity, and global availability expectations

Netflix had both the **business pressure** (global streaming demand) and the **engineering investment** (cloud migration + platform tooling) needed to make microservices work at scale.

---

## 6) How Netflix moved toward microservices

A widely cited catalyst was a major outage period (late 2000s) and growing scaling demands, which motivated Netflix to:
- reduce single points of failure,
- improve deployment independence across teams,
- move toward more fault-tolerant, distributed designs,
- and migrate significant infrastructure to **AWS**.

Netflix did not just “split a monolith into services.” They also built a platform ecosystem to handle service discovery, resilience, routing, and observability—capabilities that become mandatory when you operate many services.

---

## 7) How Netflix uses microservices (high-level)

Netflix’s architecture has evolved over time, but common microservice-related patterns include:

- **Edge/API gateway layer:** client traffic is mediated through an edge tier rather than having clients call many internal services directly (Netflix historically used Zuul for this pattern).
- **Resilience engineering:** timeouts, retries (carefully), circuit breakers, bulkheads, and graceful degradation to handle partial failures.
- **Chaos engineering:** Netflix popularized fault injection (e.g., Chaos Monkey) to validate that systems tolerate failures.
- **Progressive delivery:** canary releases and automated rollbacks to reduce deployment risk.
- **Observability:** strong metrics, tracing, and logging culture to debug distributed behavior.

**key takeaway:**  
Microservices succeed at Netflix-scale because of **tooling + culture + operational maturity**, not merely because services are “small.”

---

## 8) Companies that adopted microservices and later consolidated (microservices → fewer services / modular monolith)

> **Clarification:** “Switched back to monolith” is often oversimplified. In many cases, companies **consolidate** parts of their system (or rebuild core paths as a **modular monolith**) to reduce operational and coordination overhead.

Below are well-known examples;

### A) Uber — microservices experience and “macroservices” consolidation
**Background:** Uber is a global ride-sharing and delivery company operating at very high scale. Uber became famous for adopting microservices early and running a large number of services. Over time, they discussed moving toward **domain-oriented consolidation** (often referred to as “macroservices”) to reduce complexity and improve developer productivity in parts of the stack.

**Why consolidation happened (typical themes):**
- service sprawl and difficult ownership boundaries
- cross-service changes requiring heavy coordination
- higher operational burden (debugging, reliability, deployments)

**Links**
- Company: https://www.uber.com/  
- Engineering blog: https://www.uber.com/blog/engineering/  

> Note: Uber’s consolidation is generally described as moving from *very fine-grained microservices* to *coarser-grained services*, not a single monolith for everything.

---

### B) Shopify — preference for a modular monolith (selective services, cautious microservices)
**Background:** Shopify is a large e-commerce platform powering many online stores. Shopify is widely referenced for showing that a **well-structured monolith** can scale very far when paired with strong engineering practices. While Shopify uses services where appropriate, they have repeatedly emphasized careful use of microservices and the advantages of a modular monolith for core product development.

**Why this approach is notable:**
- simplifies local development and testing
- reduces distributed failure modes
- keeps many changes within a single deployable boundary (faster coordination for some teams)

**Links**
- Company: https://www.shopify.com/  
- Engineering blog: https://shopify.engineering/  

---

### C) Amazon Prime Video — The "1/20th the Cost" Consolidation
**Background:** In 2023, the Prime Video Video Quality Analysis (VQA) team published a widely discussed engineering case study detailing their transition from a distributed, serverless microservices architecture (utilizing AWS Step Functions and AWS Lambda) to a monolithic architecture.

**Why consolidation happened (cost and performance drivers):**

- Cost: The orchestration overhead and state transitions required to pass large video frames between multiple serverless functions were prohibitively expensive.
- Performance: Moving massive amounts of video data over the network between services created severe latency bottlenecks.
- Result: By consolidating the video analysis workflow into a single process (a monolith), the team reduced their infrastructure costs by 90% and bypassed the scaling limits previously imposed by the orchestration layer.

**Links**
- Company: https://www.primevideo.com/offers/nonprimehomepage/ref=dv_web_force_root

---

## key details 

### When microservices are a strong fit
- You have **multiple teams** that need **independent deployment**.
- Parts of the system have **very different scaling requirements**.
- You can invest in:
  - CI/CD automation,
  - observability (metrics/logs/tracing),
  - operational readiness (on-call, runbooks),
  - platform capabilities (service discovery, config management).

### Common microservices pitfalls
- **“Distributed monolith”**: many services, but changes require coordinated deployments.
- **Data consistency complexity**: cross-service transactions are hard; eventual consistency becomes common.
- **Latency and reliability risks**: every network call can fail; tail latency matters.
- **Debugging difficulty**: requires tracing and strong logging discipline.
- **Service sprawl**: too many small services with unclear ownership.

### Practical learning guidance
A good progression is:
1. Build a **modular monolith** first (clean boundaries, good testing, strong interfaces).
2. Extract a service only when you have a clear driver (scaling, deployment independence, security boundary, ownership boundary).
3. Treat “microservices” as an **organizational + operational strategy**, not just a code structure.

---


