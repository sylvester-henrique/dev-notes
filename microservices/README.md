# 📚 Table of Contents

- [Introduction](#introduction)
- [Architecture Patterns](#architecture-patterns)
- [Communication Between Microservices](#communication-between-microservices)
- [Data Management](#data-management)
- [Deployment](#deployment)
- [Monitoring and Observability](#monitoring-and-observability)
- [Security](#security)
- [Testing](#testing)
- [Best Practices](#best-practices)

# Introduction

Microservices are a software development technique—a variant of the service-oriented architecture (SOA) architectural style that structures an application as a collection of loosely coupled services. It is an architecture that divides an application into small, independent and deployable services that communicate with each other.

## Key characteristics of microservices

- Independently deployable services
- Business-focused services
- Decentralized governance
- Failure isolation
- Technology diversity

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

# Architecture Patterns

## API Gateway Pattern

A single entry point for clients, that handles authentication, authorization and routing.

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