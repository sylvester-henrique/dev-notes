# 📚 Table of Contents

- [Overview](#overview)
- [Architecture Patterns](#architecture-patterns)
- [Communication Between Microservices](#communication-between-microservices)
- [Data Management](#data-management)
- [Deployment](#deployment)
- [Monitoring and Observability](#monitoring-and-observability)
- [Security](#security)
- [Testing](#testing)
- [Best Practices](#best-practices)

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

## Database Patterns

### Database per Service

Each service has its own database to ensure data ownership and prevent tight coupling at data layer.

## Service Decomposition Patterns

Define services around business capabilities and responsibilities, ensure that services are cohesive and loosely coupled. Use the single responsibility principle.

### Bounded Contexts

Bounded contexts are a key concept in microservices architecture, originating from Domain-Driven Design (DDD). They define logical boundaries within which specific terms, entities, and operations have clear and consistent meanings. In microservices, each bounded context typically corresponds to a single service, ensuring that the service operates independently and avoids overlapping responsibilities. 

For example, in an e-commerce application, a "product" might mean different things in different contexts: as an item in a shopping cart or an inventory in a warehouse. Each of these interpretations would belong to separate bounded contexts. This approach promotes loose coupling, scalability, and maintainability by isolating models and ensuring clarity within each service.

## Event-Driven Architecture

To provide decoupled communication between services, it uses asynchronous tools for communication.

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

## Strangler Fig Pattern

During a migration from monolithic to microservices, this pattern suggests to gradually replace parts of the monolith by building microservices around it until all migration is complete.

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

## Event-Driven Architecture

### Message Queuing Systems

Messaging queue is an asynchronous form of communication between microservices. It has a producer that creates the message, and a consumer that reads the message. This type of system provides resilience, performance and scalability since it is asynchronous, so the producer does not need to wait for the consumer to receive the message.

#### Publish-Subscribe (Pub/Sub) System

When there is a producer and multiple consumers, a publish-subscribe system is used instead. The publish-subscribe (pub/sub) system has four key components:

- **Messages:** A message is communication data sent from sender to receiver. Message data types can be anything from strings to complex objects representing text, video, sensor data, audio, or other digital content.
- **Topics:** Every message has a topic associated with it. The topic acts like an intermediary channel between senders and receivers. It maintains a list of receivers who are interested in messages about that topic.
- **Subscribers:** A subscriber is the message recipient. Subscribers have to register (or subscribe) to topics of interest. They can perform different functions or do something different with the message in parallel.
- **Publishers:** The publisher is the component that sends messages. It creates messages about a topic and sends them once only to all subscribers of that topic. This interaction between the publisher and subscribers is a one-to-many relationship. The publisher doesn't need to know who is using the information it is broadcasting, and the subscribers don't need to know where the message comes from.

# Data Management

## Database per Service

## Saga Pattern

## CQRS (Command Query Responsibility Segregation)

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