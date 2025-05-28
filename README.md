# Emporium Platform

- Refer to [**Lab1.pdf**](https://github.com/Emporium-Platform/.github/blob/main/documents/Lab1.pdf)
  and [**Lab2.pdf**](https://github.com/Emporium-Platform/.github/blob/main/documents/Lab2.pdf)
- Refer to [**projectReport.pdf**](https://github.com/Emporium-Platform/.github/blob/main/documents/EmporiumPlatformReport.pdf)
- Each service includes its own README with usage and deployment instructions.



---

## Overview

The Emporium Platform is a multi-tier online bookstore system developed as part of a distributed systems course. It demonstrates concepts such as microservices, REST APIs, replication, caching, and containerization.

Initially designed with a minimal book catalog, the system has been extended with performance and reliability features across two labs.

---

## Repository Structure

- **Emporium-gateway-service**: Handles user-facing API requests and distributes them to internal services.
- **Emporium-catalog-service**: Manages book information and inventory.
- **Emporium-order-service**: Processes purchases and logs transactions.
- **.github**: Documentation and reports.

Each service is containerized and can run independently or via Docker Compose.

---

## Architecture

### High-Level Design

The platform follows a microservices architecture with independent services that communicate over HTTP using REST. Advanced features include load balancing, replication, and caching to improve fault tolerance and performance.


![System Design](https://github.com/Emporium-Platform/.github/blob/main/assets/systemdesign.png?raw=true)

### Components

1. **Gateway Service**:
   - Acts as the API gateway and load balancer.
   - Performs in-memory caching using the LRU strategy.
   - Forwards read and write requests to the appropriate service using round-robin load balancing.

2. **Catalog Service**:
   - Manages book inventory, titles, prices, and topics.
   - Implements primary-backup replication for consistency.
   - Informs the Gateway to invalidate relevant cache entries upon update.

3. **Order Service**:
   - Handles purchase requests and interacts with the Catalog to verify and update stock.
   - Writes order logs to a CSV file.
   - Supports replication between order replicas to maintain log consistency.

---

## Lab 1: Building the Foundation

### Objectives

- Implement a basic microservices-based online bookstore.
- Create REST APIs for book querying and purchases.
- Containerize services using Docker.

### Implemented Features

- Gateway routing to Catalog/Order
- Catalog service with hardcoded book data
- Order service with purchase and CSV logging
- Docker-based deployment

---

## Lab 2: Scaling Emporium

### Objectives

- Add replication to Order and Catalog services.
- Implement in-memory caching for queries.
- Introduce load balancing and container orchestration.

### Enhancements

- **Replication**:
  - Catalog: primary-backup sync.
  - Order: log replication between instances.

- **Caching**:
  - LRU cache in Gateway for search/info requests.
  - Invalidation on updates and purchases.

- **Load Balancing**:
  - Gateway uses round-robin algorithm to distribute traffic across replicas.

- **Containerization**:
  - All services run in Docker containers.
  - Docker Compose configures inter-service networking.

---

## REST API Endpoints

### Gateway Service

- `GET /search/:topic` – Search books by topic.
- `GET /info/:itemNumber` – Get details about a specific book.
- `POST /purchase/:itemNumber` – Process a purchase request.
- `GET /health` – Health check.
- `GET /cache/stats` – (Lab 2) Get cache metrics.
- `POST /cache/invalidate` – (Lab 2) Invalidate specific cached key.

### Catalog Service

- `GET /search/:topic` – Find books by topic.
- `GET /info/:item_number` – Retrieve book details.
- `PUT /update/:item_number` – Update price/quantity (primary only).
- `PUT /replica_sync/:item_number` – Internal endpoint for syncing replicas.
- `GET /health` – Health and role info.

### Order Service

- `GET /health` – Health check.
- `POST /purchase/:itemId` – Process purchase.
- `POST /replicate` – Sync purchase log with replica (Lab 2).

---

## Development & Deployment

1. **Clone all repositories**:
   - `Emporium-gateway-service`
   - `Emporium-catalog-service`
   - `Emporium-order-service`

2. **Start all services together**:
```bash
docker-compose up --build
