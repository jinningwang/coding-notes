# Micrservices

Microservice is an architectural style that structures a large-scale software
application as a collection of small, loosely coupled, independently deployable
services.
Each microservice focuses on a specific business capability and communicates with
other services through lightweight protocols, typically HTTP or messaging queues.

## 🧩 Key Characteristics of Microservices

1. Single Responsibility: Each service does one thing well.
1. Independent Deployment: Services can be updated or deployed without affecting others.
1. Technology Agnostic: Different services can be built using different programming
  languages or databases.
1. Scalability: Individual services can be scaled independently based on demand.
1. Resilience: Failure in one service doesn’t necessarily bring down the whole system.

## 🏗️ Example:

Imagine an e-commerce platform:

- User Service: Handles user registration and authentication.
- Product Service: Manages product listings.
- Order Service: Processes orders.
- Payment Service: Handles transactions.
- Each of these can be developed, deployed, and scaled separately.

## 🔍 Is "decoupling" enough to fix monolithic architecture?

Not quite.
Decoupling (i.e., separating modules logically) is necessary, but not sufficient to
transform a monolith into a microservices architecture.

Here's why:

### 1. Deployment Independence

- In a monolith, even if modules are decoupled in code, they are still **deployed**
  **together**.
- Microservices require **independent deployment**—each service runs as its own
  process, often in its own container.

### 2. Runtime Isolation

- Monolithic modules share the same memory space and runtime.
- Microservices run in **separate environments**, which improves fault isolation and
  scalability.

### 3. Technology Flexibility

- Monoliths usually use a **single tech stack**.
- Microservices allow each service to use the **best-fit technology** (e.g., Python
  for ML, Go for networking, etc.).

### 4. Scalability Granularity

- In a monolith, you scale the **entire app**, even if only one module needs it.
- Microservices let you scale **just the service** that’s under load.

### 5. Team Autonomy

- Monolithic teams often have to coordinate tightly.
- Microservices enable **independent teams** to own and manage their services end-to-end.

## ☸️ What is Kubernetes?

Kubernetes (often abbreviated as **K8s**) is a container orchestration platform developed
by Google.

### 🔧 Key Concepts

- It manages **deployment**, **scaling**, and **operation** of containers across a cluster
  of machines.
- Automatically handles **load balancing**, **failover**, **rolling updates**, and **resource**
  **allocation**.
- Works well with Docker containers (and others).

### 🧠 Why it's useful for microservices

- Microservices often run as many containers—Kubernetes helps **coordinate and manage** them.
- Ensures high **availability and scalability**.
- Provides tools for **monitoring, self-healing, and service discovery**.

## 🧭 What is Kafka Architecture?

**Apache Kafka** is a **distributed event streaming platform** used to build
**real-time data pipelines and streaming applications**.
It’s designed to handle **high-throughput, fault-tolerant, and scalable messaging**
between systems.

### 🔧 Key Concepts in Kafka Architecture:

1. **Producer**: Sends data (messages/events) into Kafka.
1. **Consumer**: Reads data from Kafka.
1. **Topic**: A category or feed name to which records are sent by producers.
1. **Broker**: A Kafka server that stores and serves messages.
1. **Partition**: Topics are split into partitions for scalability and parallelism.
1. **Zookeeper**: Used for managing Kafka cluster metadata (though newer versions are moving
  away from it).

### 🧠 Why Kafka is Important in Microservices:

- Microservices often need to **communicate asynchronously**.
- Kafka enables **event-driven architecture**, where services react to events (e.g.,
  "OrderPlaced", "PaymentReceived").
- It decouples services: producers don’t need to know who consumes the data.
- It supports real-time analytics, logging, monitoring, and data integration.

### 📘 What You Should Learn About Kafka:

If you're starting out, here’s a roadmap:

1. **Basic Concepts**: Topics, producers, consumers, brokers.
1. **Kafka CLI & APIs**: How to produce and consume messages.
1. **Kafka Streams**: For real-time data processing.
1. **Kafka Connect**: For integrating with databases, files, etc.
1. **Event-Driven Design Patterns**: Like CQRS, Saga, etc.
1. **Monitoring & Scaling Kafka**: Tools like Prometheus, Grafana.
