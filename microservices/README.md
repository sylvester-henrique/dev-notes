# 📚 Table of Contents

- [Overview](#overview)
  - [Pros](#pros)
  - [Cons](#cons)
  - [Core Architectural Building Blocks](#core-architectural-building-blocks)
  - [Example Domain Decomposition (E-commerce)](#example-domain-decomposition-e-commerce)
- [Architecture Patterns](#architecture-patterns)
  - [Service Decomposition Patterns](#service-decomposition-patterns)
  - [Load Balancing](#load-balancing)
  - [API Gateway Pattern](#api-gateway-pattern)
  - [Backends for Frontends (BFF)](#backends-for-frontends-bff)
  - [Event-Driven Architecture](#event-driven-architecture)
  - [Strangler Fig Pattern](#strangler-fig-pattern)
  - [Circuit Breaker Pattern](#circuit-breaker-pattern)
- [Communication Between Microservices](#communication-between-microservices)
  - [Main Patterns of Communication](#main-patterns-of-communication)
  - [Additional Considerations](#additional-considerations)
  - [When to Use Asynchronous Communication vs. Synchronous Communication in Microservices](#when-to-use-asynchronous-communication-vs-synchronous-communication-in-microservices)
  - [Summary Table](#summary-table)
  - [Event-Driven Architecture](#event-driven-architecture-1)
- [Data Management](#data-management)
  - [Database per Service](#database-per-service)
  - [Shared Database (Anti-Pattern)](#shared-database-anti-pattern)
- [Deployment](#deployment)
  - [Containerization](#containerization)
  - [Orchestration](#orchestration)
  - [CI/CD](#ci-cd)
- [Monitoring and Observability](#monitoring-and-observability)
  - [Distributed Tracing](#distributed-tracing)
  - [Logging](#logging)
  - [Metrics](#metrics)
- [Security](#security)
  - [Authentication and Authorization](#authentication-and-authorization)
  - [Service-to-Service Security](#service-to-service-security)
  - [API Security](#api-security)
- [Testing](#testing)
  - [Unit Testing](#unit-testing)
  - [Integration Testing](#integration-testing)
  - [Contract Testing](#contract-testing)
  - [End-to-End Testing](#end-to-end-testing)
- [Best Practices](#best-practices)
  - [Design Principles](#design-principles)
  - [Common Pitfalls](#common-pitfalls)
  - [Performance Considerations](#performance-considerations)

# Overview

Microservices are a software development technique, a variant of the service-oriented architecture (SOA) architectural style, that structures an application as a collection of small, independent, and deployable services that communicate with each other. This architectural approach divides complex applications into loosely coupled services, each focused on specific business capabilities.

## Pros

- **Deliver time:** delivery time for a feature is shorter, compared to a monolithic architecture, because it has smaller scope, with less changes to develop and test. Also, parallel development becomes more efficient, with each team working on services independently.
- **Extensible:** add a new functionality to the whole system can be easier, this can be done by adding another microservice
- **Smooth rollout of features:** when a new feature is released, it is only necessary to deploy one application rather than the entire system.
- **Scalability:** each service can be scaled independently and horizontally, this optimizes resource usage.
- **Resilience:** a failure or performance issues does not necessarily impacts the entire application, potentially improving resilience

## Cons

- **Overall complexity:** microservices are more complex to design, build and operate compared to monolith
- **Data management:** enforcing data consistency across services is challenging, often requiring eventual consistency. Data synchronization may require a distributed event system, such as Kafka and RabbitMQ
- **Communication overhead:** communication between services have more latency compared to in-memory calls in monolith applications
- **Debugging:** troubleshooting is harder due to the distributed nature of microservices, requiring tools like distributed tracing
- **Network dependency:** the communication is done over standard network protocols, so a network issue can interfere with the whole system

## Core Architectural Building Blocks

- **API Gateway:** Unified entry point (routing, auth, rate limiting, aggregation).
- **Service Discovery:** Dynamic registry of service instances (Consul, Eureka, Kubernetes DNS).
- **Load Balancing:** Distributes traffic (sidecar proxy, gateway, ingress).
- **Configuration Management:** Centralized, versioned config (Spring Cloud Config, Vault, SSM).
- **Service Mesh (optional):** Sidecars handling mTLS, retries, circuit breaking, telemetry (Istio, Linkerd).
- **Messaging/Event Backbone:** Kafka, RabbitMQ, Pulsar, NATS for async decoupling.
- **Observability Stack:** 
  - Logging (ELK, Loki)
  - Metrics (Prometheus, OpenTelemetry metrics)
  - Tracing (Jaeger, Tempo, Zipkin)
- **Secrets Management:** Vault, AWS KMS, SSM Parameter Store.
- **CI/CD Pipeline:** Automated build/test/deploy per service.

## Example Domain Decomposition (E-commerce)

Services might include:
- User / Identity Service
- Product Catalog Service
- Inventory Service
- Cart Service
- Order Service
- Payment Service
- Shipping Service
- Notification Service (email/SMS)
- Review/Ratings Service

# Architecture Patterns

## Service Decomposition Patterns

One of the biggest challenges in microservices is defining the boundaries of each service. If boundaries are wrong, you end up with a distributed monolith (tight coupling).

### Decompose by Business Capability
Define services corresponding to business capabilities. A business capability is something that a business does in order to generate value.
- **Example:** Order Management, Inventory Management, Shipping, Customer Management.
- **Pros:** Stable architecture (business capabilities don't change often), easy to understand for business stakeholders.
- **Cons:** Can lead to "God Services" if a capability is too broad.

### Decompose by Subdomain (DDD)
Uses Domain-Driven Design (DDD) concepts. The domain is decomposed into subdomains based on the complexity and value of the business logic.
- **Core Subdomain:** The key differentiator of the business (most valuable). This is where the best developers should work.
- **Supporting Subdomain:** Necessary for the business but not a competitive advantage (e.g., Catalog in a standard e-commerce).
- **Generic Subdomain:** Common functionality (e.g., Authentication, Notification) that is not specific to the business domain.
- **Mapping to Microservices:**
  - **1-to-1:** Ideally, one Subdomain corresponds to one Bounded Context, which is implemented as one Microservice. This is the cleanest approach.
  - **1-to-Many:** A complex Subdomain might be split into multiple Bounded Contexts (and thus multiple services).
  - **Many-to-1:** Several simple Subdomains (especially Generic or Supporting ones) might be grouped into a single service to avoid infrastructure overhead.

### Bounded Contexts
Bounded Context is the central pattern in Domain-Driven Design (DDD) for defining the boundaries of a model. To understand it, it helps to distinguish between the "Problem Space" and the "Solution Space":

- **Problem Space (Subdomains):** This is the raw business reality (e.g., "We need to ship items" or "We need to manage inventory"). It exists whether software exists or not.
- **Solution Space (Bounded Contexts):** This is the strategic architectural decision we make to solve those problems (e.g., "We will build a Shipping Service with a specific model for packages").

Ideally, a Bounded Context aligns with a Subdomain, but they are distinct concepts.

- **Definition:** A specific boundary within which a domain model is defined and applicable. Inside this boundary, all terms, entities, and logic have a specific, unambiguous meaning (Ubiquitous Language).
- **Ubiquitous Language:** A common language shared by developers and domain experts within that specific context. For example, "User" in the *Identity Context* might mean credentials and roles, while "User" in the *Shipping Context* might mean a name and address.
- **Relationship to Microservices:** Ideally, one Bounded Context maps to one Microservice. This ensures high cohesion (everything related to that context is in one place) and loose coupling (internal changes don't leak out).
- **Example:**
  - **Catalog Context:** "Product" = Title, Description, Images, Specs.
  - **Inventory Context:** "Product" = SKU, Quantity, Location, Reorder Level.
  - **Sales Context:** "Product" = Price, Tax Rate, Discount.
  
  Instead of a single giant "Product" class, we have three smaller, specialized models in three different services.

## Load Balancing

Load balancing is the process of distributing network traffic across multiple servers to ensure that no single server bears too much demand. In microservices, where services are often replicated across multiple instances for scale and availability, load balancing is critical.

### Types of Load Balancing

#### 1. Server-Side Load Balancing
The client sends a request to a load balancer (e.g., Nginx, HAProxy, AWS ELB), which acts as a proxy. The load balancer then forwards the request to one of the available service instances.
- **Pros:** Simple for the client (it only needs to know the load balancer's address).
- **Cons:** The load balancer can become a bottleneck and a single point of failure; adds an extra network hop.

#### 2. Client-Side Load Balancing
The client is responsible for determining which service instance to call. It queries a Service Registry (like Eureka or Consul) to get a list of available instances and uses a library (like Netflix Ribbon or gRPC client) to choose one.
- **Pros:** No extra hop (better latency), eliminates the central bottleneck.
- **Cons:** Client logic is more complex; load balancing logic must be implemented in every client language/framework used.

### Common Algorithms
- **Round Robin:** Requests are distributed sequentially to each server.
- **Least Connections:** Sends the request to the instance with the fewest active connections.
- **IP Hash:** Uses the client's IP address to determine which server receives the request (useful for session stickiness).
- **Weighted:** Assigns more requests to more powerful servers.   

## API Gateway Pattern

An API Gateway is a dedicated service that acts as the single entry point for external (and sometimes internal) clients to a system composed of multiple microservices. It receives client requests, orchestrates or proxies calls to underlying services, applies cross-cutting concerns (authentication, rate limiting, observability), and returns aggregated or transformed responses.

It implements the Facade pattern for distributed systems, tailoring the external interface while hiding internal service topology and evolution.

### Why Do We Need It?

Direct client-to-microservice communication creates several challenges:

- **Coupling:** Clients need to know the location and API of each individual service. If the internal architecture changes (e.g., splitting a service), the client code must be updated.
- **Too Many Round Trips:** A single page might require data from multiple services, leading to multiple network requests. This increases latency and consumes more battery on mobile devices.
- **Security Risks:** Exposing all microservices directly to the internet increases the attack surface. It is harder to secure hundreds of endpoints than a single entry point.
- **Cross-Cutting Concerns:** Logic for authentication, SSL termination, rate limiting, and logging would need to be implemented in every single microservice or in the client.
- **Protocol Issues:** Some services might use protocols that are not web-friendly (e.g., AMQP or binary gRPC), making it difficult for browsers to consume them directly.

The API Gateway solves these issues by acting as an intermediary that abstracts the backend complexity from the client.

### Core Responsibilities

The API Gateway handles several key responsibilities that would otherwise need to be implemented in each microservice or the client:

- **Request Routing & Mediation:** Routes incoming requests to the appropriate microservice. It can also handle protocol translation (e.g., HTTP to gRPC) and payload transformation.
- **Security:** Acts as the first line of defense. Handles authentication (verifying identity), authorization (checking permissions), SSL/TLS termination, and protection against threats like SQL injection or DDoS attacks (WAF).
- **Traffic Management:** Controls the flow of traffic to backend services. This includes rate limiting (limiting requests per user), throttling, and load balancing.
- **Aggregation:** Combines results from multiple microservices into a single response to reduce the number of network calls the client needs to make.
- **Resilience:** Implements patterns to prevent cascading failures, such as circuit breakers, retries with exponential backoff, and timeouts.
- **Observability:** Centralizes logging, metrics (latency, error rates), and distributed tracing to provide visibility into traffic and system health.
- **Performance Optimization:** Improves performance through response caching, compression (e.g., Gzip), and connection pooling.
- **Release Strategies:** Supports advanced deployment strategies like Canary releases (routing a small % of traffic to new version) and Blue/Green deployments.

### Migration Path (Monolith → Gateway → Microservices)

1. Introduce gateway in front of monolith for auth, rate limiting.
2. Extract first microservice (e.g., static assets, notifications).
3. Add routes mapping: some paths go to monolith, others to new services.
4. Iteratively shift endpoints as you carve out domains.
5. Introduce aggregation for composite pages to reduce client changes.
6. Optimize with caching, circuit breaking, observability upgrades.

### Pros

- **Encapsulation:** Hides the internal structure of the application from the client. Clients interact with the gateway, not individual services.
- **Simplified Client Code:** Clients don't need to handle cross-cutting concerns like authentication, SSL termination, or service discovery.
- **Protocol Translation:** Can translate between web-friendly protocols (HTTP/JSON) and internal protocols (gRPC, AMQP).
- **Reduced Latency:** Can aggregate multiple requests into a single request, reducing round trips for the client.
- **Security:** Centralized place to implement security policies (authentication, authorization, rate limiting).

### Cons

- **Single Point of Failure:** If the gateway goes down, the entire application becomes inaccessible. High availability setup is crucial.
- **Performance Bottleneck:** All traffic goes through the gateway, so it can become a bottleneck if not scaled properly.
- **Complexity:** Adds another moving part to the infrastructure that needs to be managed, deployed, and monitored.
- **Development Bottleneck:** Can become a bottleneck for development if multiple teams need to update the gateway configuration frequently.

### API Gateway vs. Load Balancer
While both distribute traffic, they serve different purposes and often work together.

- **Load Balancer:** Operates at a lower level (Transport Layer L4 or Application Layer L7). Its primary job is to distribute traffic across healthy instances of a *single* service (or the Gateway itself) to ensure availability and performance.
- **API Gateway:** Operates at the Application Layer (L7). It understands the *business* logic of the request (routing based on path, headers, user identity) and performs orchestration.

### Typical Request Flow
1. **Client** sends a request.
2. **External Load Balancer** (e.g., AWS ALB) receives the request and forwards it to one of the **API Gateway** instances.
3. **API Gateway** processes the request (auth, rate limiting), looks up the destination service in the **Service Registry**.
4. **API Gateway** (acting as an internal load balancer) forwards the request to a specific instance of the **Microservice**.

## Backends for Frontends (BFF)

Create a separate backend for each type of client, such as desktop and mobile.

**Benefits:**
- **Avoid bloated API:** that serves multiple clients and become overcomplicated
- **Meet requirements for specific needs:** For example, a mobile client app might need smaller and optimized payloads
- **Group requests:** it can have endpoints that make requests to different microservices, so the client will need to make just one request to the specific backend

### Choosing between API Gateway vs BFF

When deciding between a single API Gateway and the BFF pattern, consider the diversity of your clients:

- **Use a Single API Gateway when:**
  - You have a single client type (e.g., a web application).
  - Multiple clients have very similar requirements and data needs.
  - You want to minimize infrastructure complexity and maintenance overhead.
  - The team is small, and managing multiple gateways would be overkill.

- **Use BFF (Backend for Frontend) when:**
  - You have multiple client types with significantly different requirements (e.g., Mobile App vs. Web App vs. IoT device).
  - Mobile clients need minimized payloads to save bandwidth and battery.
  - Different teams manage different client applications and want autonomy to change their backend logic without coordinating with others.
  - You want to optimize the API experience specifically for each client interface. 

## Event-Driven Architecture

In an Event-Driven Architecture (EDA), services communicate by emitting and reacting to events. An **event** is a significant change in state (e.g., "OrderPlaced", "PaymentFailed").

### Core Concepts
- **Producer:** The service that detects the event and publishes it. It doesn't know who (if anyone) is listening.
- **Consumer:** The service that subscribes to events and reacts to them.
- **Event Channel:** The infrastructure (Message Broker) that transmits events (e.g., Kafka, RabbitMQ).

### Messaging Models

#### 1. Message Queuing (Point-to-Point)
A message is sent to a specific queue and is processed by **only one** consumer.
- **Behavior:** If multiple consumers listen to the queue, they compete for messages (load balancing).
- **Use Case:** Distributing background jobs (e.g., image processing) among worker instances.

#### 2. Publish-Subscribe (Pub/Sub)
A message is sent to a **topic** and is delivered to **all** subscribers interested in that topic.
- **Topics:** Logical channels that categorize messages.
- **Subscribers:** Services that register interest in a topic. They process the same event independently and in parallel.
- **Use Case:** notifying multiple services about a business event (e.g., "Order Placed" triggers Inventory, Shipping, and Email services).

### Two Main Variations

#### 1. Event Notification
The event contains minimal information (e.g., `{"orderId": "123", "status": "shipped"}`).
- **Behavior:** The consumer receives the event but often needs to call the producer's API to fetch more details (e.g., shipping address).
- **Pros:** Simple payloads, low coupling regarding data structure.
- **Cons:** Can lead to "API chatter" (consumers calling back producers).

#### 2. Event-Carried State Transfer
The event contains all the data the consumer needs (e.g., the full Order object).
- **Behavior:** The consumer updates its own local copy of the data without calling the producer.
- **Pros:** Decouples availability (consumer works even if producer is down), better performance (no callback).
- **Cons:** Data duplication, eventual consistency complexity, larger message payloads.

### Pros
- **Decoupling:** Producers and consumers don't know about each other.
- **Scalability:** Consumers can process events at their own pace (buffering).
- **Resilience:** If a consumer is down, events are queued until it recovers.

### Cons
- **Complexity:** Harder to debug and trace flows (requires distributed tracing).
- **Eventual Consistency:** Data is not consistent immediately across the system.

## Strangler Fig Pattern

The Strangler Fig Pattern is the most popular strategy for migrating a monolithic application to microservices. It is named after the strangler fig tree, which grows around a host tree and eventually replaces it.

### How It Works
Instead of rewriting the entire monolith from scratch ("Big Bang" rewrite), you gradually replace specific functionalities with new microservices.

1.  **Identify:** Select a specific functionality (Bounded Context) within the monolith to migrate (e.g., "User Profile").
2.  **Transform:** Build a new microservice that implements this functionality.
3.  **Coexist:** Place an API Gateway or Proxy in front of the system. Configure it to route requests for the new functionality to the new microservice, while all other requests still go to the monolith.
4.  **Eliminate:** Once the new service is stable and feature-complete, remove the old code from the monolith.
5.  **Repeat:** Continue this process for other functionalities until the monolith is gone or reduced to a manageable size.

### Pros
- **Low Risk:** Migration happens in small, manageable steps. If something breaks, it's easy to revert.
- **Incremental Value:** You can deliver new features in the new architecture immediately, without waiting for the whole migration to finish.
- **No "Code Freeze":** The business can continue to add features to the monolith while migration is happening (though it's better to add them to new services).

### Cons
- **Complexity:** You have to manage two distinct systems (monolith + microservices) simultaneously for a long time.
- **Data Synchronization:** If the monolith and the new service need to share data, you might need temporary synchronization mechanisms (e.g., double writes or CDC) until the data is fully migrated.

## Circuit Breaker Pattern

TODO

# Communication Between Microservices

Microservices communicate with each other primarily through APIs over network protocols. The most common method is via HTTP RESTful APIs, but other protocols like gRPC, GraphQL, WebSockets, or message brokers (e.g., Kafka, RabbitMQ) are also used depending on requirements.

## Main Patterns of Communication

### 1. Synchronous Communication
- One service directly calls another and waits for a response.
- **Common protocols:** HTTP/REST, gRPC.
- **Pros:** Simpler, easier to debug.
- **Cons:** Tighter coupling, can affect performance and reliability if services are down or slow.

### 2. Asynchronous Communication
- Services communicate via messaging systems (like Kafka, RabbitMQ, or AWS SQS).
- Messages are sent to a queue/topic and processed independently.
- **Pros:** Looser coupling, scalable, fault-tolerant.
- **Cons:** More complex, eventual consistency.

## Additional Considerations

- **Service Discovery:** Microservices need to locate each other dynamically, often using tools like Consul, Eureka, or Kubernetes DNS.
- **API Gateway:** Handles routing, authentication, rate limiting, and monitoring for service-to-service and external communication.
- **Security:** Services must authenticate and authorize requests, often using OAuth, JWT, or mTLS.
- **Resilience:** Patterns like circuit breakers, retries, and timeouts help manage network failures.

## When to Use Asynchronous Communication vs. Synchronous Communication in Microservices

Understanding when to use asynchronous communication over synchronous communication is crucial to designing scalable, resilient, and maintainable microservices architectures.

### Asynchronous Communication

**Asynchronous communication** is preferred over synchronous communication in the following scenarios:

1. **Decoupling Services**  
   When microservices should operate independently and not be tightly coupled. Asynchronous methods (using message brokers like Kafka, RabbitMQ) allow a service to send messages without waiting for a response.

2. **Scalability and High Throughput**  
   For systems that need to handle a large volume of requests or events, asynchronous messaging enables better load distribution and parallel processing.

3. **Reliability and Fault Tolerance**  
   If a service is temporarily unavailable, messages can be queued and processed later, increasing system resilience.

4. **Long-Running or Time-Consuming Tasks**  
   For tasks that take a long time to complete (e.g., video processing, batch jobs), asynchronous communication lets the user continue without waiting for a response.

5. **Event-Driven Architectures**  
   When your system reacts to events (e.g., "order placed", "user registered"), asynchronous messaging is ideal for propagating such events.

6. **Loose Coupling and Flexibility**  
   Asynchronous communication allows services to evolve independently, making the system more maintainable and adaptable.


### Synchronous Communication

**Synchronous communication** is better when:
- Immediate feedback or response is required (e.g., querying user details, authentication).
- Operations are simple, fast, and require confirmation before proceeding.

### Example

When a user places an order, the order service can asynchronously notify the inventory, payment, and shipping services via events/messages, allowing each service to process the order independently.

## Summary Table

| Scenario                      | Recommended Communication   |
|-------------------------------|----------------------------|
| Immediate response needed     | Synchronous (REST/gRPC)    |
| Decoupled, resilient system   | Asynchronous (Queue/Event) |
| High throughput, scalability  | Asynchronous               |
| Long-running processes        | Asynchronous               |
| Event-driven actions          | Asynchronous               |

# Data Management

Managing data in microservices is challenging because of the distributed nature of the architecture. Unlike monolithic applications where a single database handles transactions and consistency, microservices often require patterns to handle distributed data.

## Database per Service

This is the default pattern for microservices. Each service has its own private database. Other services cannot access this database directly; they must communicate via the service's API.

- **Polyglot Persistence:** Allows each service to choose the database technology that best suits its needs (e.g., Relational for accounting, NoSQL for product catalog, Graph for recommendations).
- **Pros:**
  - **Loose Coupling:** Changes to one service's database schema do not impact other services.
  - **Independent Scaling:** Each database can be scaled according to the service's specific load.
- **Cons:**
  - **Cross-Service Queries:** Joining data from multiple services is difficult and often requires API composition or data replication.
  - **Distributed Transactions:** Maintaining consistency across multiple services is complex (requires Sagas).

## Shared Database (Anti-Pattern)

Multiple services share the same database schema.

- **Pros:** Simple to implement, supports ACID transactions easily.
- **Cons:**
  - **Tight Coupling:** Services become coupled to the database schema. Changing the schema requires coordinating all services.
  - **Performance:** The database becomes a bottleneck and a single point of failure.
  - **Scalability:** Hard to scale individual services' data needs independently.
- **Use Case:** Sometimes acceptable for very small internal applications or during the initial migration from a monolith.

# Deployment

## Containerization

## Orchestration

## CI/CD

# Monitoring and Observability

## Distributed Tracing

## Logging

## Metrics

# Security

## Authentication and Authorization

## Service-to-Service Security

## API Security

# Testing

## Unit Testing

## Integration Testing

## Contract Testing

## End-to-End Testing

# Best Practices

## Design Principles

- **Define clear responsibilities:** Define services around business capabilities and responsibilities, ensure that services are cohesive and loosely coupled. Finally use the single responsibility principle.
- **Implement observability:** Incorporate logging, monitoring and distributed tracing for effective debugging and performance analysis.
- **Asynchronous communication:** Use asynchronous communication for scalability in scenarios where synchronous communication is not required. This is done through messaging systems.
- **Use containers and orchestration framework:** Containers ensure that services get isolated, and allow to constraint CPU and memory usage. Also, orchestration tools make it easier to manage containers.

## Common Pitfalls

## Performance Considerations