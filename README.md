
# 🧾 Billing platform — Enterprise (Java Spring Boot)

- **Enterprise-grade multi-tenant Billing service** built with **DDD**, Kafka, PostgreSQL, MongoDB, Redis, and Spring Boot.
Money-critical, scalable, and resilient. Nothing optional — all features included.



## 🚀 Features

* **Domain-Driven Design (DDD)** structure
* Multi-tenant support 🏢
* Billing: subscriptions, invoices, payments 💳
* Strong ACID transactions for financial data 💰
* Async event-driven architecture using Kafka 🛠️
* Notifications via email/SMS/push 🔔
* Read/Write separation: PostgreSQL write, MongoDB read 📊
* Caching with Redis ⚡
* Authentication & RBAC (JWT, OAuth2) 🔑
* Rate limiting per tenant ⏱️
* Load-balanced, stateless replicas ⚖️
* Resilience & retry: circuit breakers, DLQ, backoff 🔄
* Full observability: metrics, structured logs, health endpoints 📈
* Audit logs for all critical operations 📜

---

## 🏗️ Architecture Overview

```
[ Clients (Web/Mobile/API) ]
               │
        [ Load Balancer ]
               │
        [ API Gateway ] 🔐
      (Auth, Rate Limit, Circuit Breaker)
               │
        [ Billing Service (Spring Boot) ] 🧱
               │
   ┌───────────┼───────────┐
   │           │           │
[PostgreSQL] [MongoDB]   [Redis]
   │           │           │
   └───────────┴───────────┘
               │
             [ Kafka ]
               │
        [ Notification Service ]
```

* Stateless replicas behind load balancer
* Async events decouple billing core from notifications
* Multi-tenancy enforced at the domain layer

---

## 📂 Domain Layers

### Subscription

* Manages plans, billing cycles, statuses
* Authorizes payments, does not execute them

### Invoice

* Immutable after creation
* Tracks amount, taxes, status (OPEN, PAID, FAILED)

### Payment

* Handles payment attempts, provider integration
* Idempotent & async processing

### Authentication

* Users, tenants, roles stored in PostgreSQL
* JWT short-lived + refresh tokens
* RBAC enforced in domain

---

## ⚡ Event-driven Workflow

```
Scheduler/Event triggers Billing Cycle
   ↓
CreateInvoiceUseCase
   ↓
InvoiceCreated Event
   ↓
RequestPaymentUseCase
   ↓
PaymentProvider (External)
   ↓
PaymentSucceeded / PaymentFailed
   ↓
InvoicePaid or Retry / PastDue
   ↓
SubscriptionContinues or Suspended
```

* Async where possible
* Domain events = source of truth

---

## 🗂️ Multi-Tenancy

* tenant_id column in all tables
* validated in domain logic
* queries tenant-aware
* scalable: tenant column, schema per tenant, or DB per tenant

---

## 💡 Read / Write Separation

* **Write**: PostgreSQL (financial, critical)
* **Read**: MongoDB (dashboards, aggregated queries)
* Eventual consistency accepted for reads

---

## 🔧 Resilience

* Retry with exponential backoff
* Dead-letter queues (DLQ)
* Circuit breakers
* Timeout handling
* Failures isolated from billing core

---

## 🏎️ Caching (Redis)

* Plans, permissions, tenant configs
* Never cache financial state
* Improves latency & reduces DB load

---

## 📊 Observability

* Structured logs (correlation per charge/transaction)
* Metrics: success_rate, retry_count, billing_latency, kafka_lag
* Health checks & alerts for failures

---

## 🔐 Security

* OAuth2 / OIDC for API access
* JWT tokens + refresh
* RBAC + tenant isolation enforced in domain
* Audit logs for all critical operations

---

## 📦 Replication & Availability

* PostgreSQL: primary + read replicas
* Kafka: replication factor ≥3
* Stateless billing service replicas
* Rolling deploys allowed

---

## ⚖️ Trade-offs

| Decision             | Cost                    | Benefit                          |
| -------------------- | ----------------------- | -------------------------------- |
| Kafka                | Complexity              | Decoupling, replay, auditing     |
| PostgreSQL + MongoDB | Data duplication        | Performance, scalable reads      |
| API Gateway          | Added latency           | Security, rate limiting, auth    |
| Cache                | Invalidation complexity | Speed, reduced DB load           |
| Eventual consistency | Intermediate state      | Resilience, fault tolerance      |
| Replicas             | Infra & hardware        | High availability, zero downtime |

---

## 🛠️ Tech Stack

* Java 17+ / Spring Boot 3.x
* PostgreSQL (write model & authentication)
* MongoDB (read model, analytics)
* Redis (cache)
* Kafka (events, async communication)
* Docker / Docker Compose
* OAuth2 / JWT auth

---

## 🏃 Running Locally

1. Clone the repository

```bash
git clone https://your-repo.git
cd billing-saas
```

2. Start services via Docker Compose

```bash
docker-compose up -d
```

3. Access APIs: `http://localhost:8080/api`
4. Health endpoint: `http://localhost:8080/actuator/health`

---

## 🔮 Next Steps

* Implement UI for subscription & invoice management
* Add reporting & analytics microservices
* Integrate monitoring dashboards (Prometheus/Grafana)
* Scale horizontally as tenants grow

---

## 🎯 Notes

* Everything is designed for **real SaaS scale**
* Money-critical operations are protected by ACID + events
* Multi-tenancy & RBAC enforced in domain layer
* Observability & resilience baked in from day 1


## 👥 Authors

- **edsonwade** - Initial work

## 🙏 Acknowledgments

- Spring Boot team for excellent documentation
- Domain-Driven Design community
- Microservices patterns community

---

**Built with ❤️ using Spring Boot and Microservices Architecture**


## License ⚖️
This project is licensed under the [MIT License](https://opensource.org/licenses/MIT).

