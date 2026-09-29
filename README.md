**software architecture**, as a practical architecture playbook covering **standards → principles → practices → patterns → implementation → real-world examples**.

## 1. Architecture Standards

Standards define the **constraints and quality expectations** of the system.

| Area           | Recommended standards                                         |
| -------------- | ------------------------------------------------------------- |
| Architecture   | C4 Model, ISO/IEC/IEEE 42010                                  |
| API            | REST/OpenAPI, HTTP semantics                                  |
| Security       | OWASP ASVS, OWASP Top 10, OAuth 2.0/OIDC                      |
| Data           | PostgreSQL standards, normalization where appropriate         |
| Cloud          | 12-Factor principles, cloud Well-Architected Framework        |
| Observability  | OpenTelemetry, structured logging                             |
| Containers     | OCI/Docker standards                                          |
| Messaging      | CloudEvents where applicable                                  |
| Documentation  | ADRs + C4 diagrams                                            |
| Testing        | Unit, integration, contract, E2E                              |
| Accessibility  | WCAG                                                          |
| Quality        | ISO/IEC 25010 quality characteristics                         |
| Source control | Git + trunk-based development/GitFlow according to team needs |
| CI/CD          | Automated build, test, security scanning, deployment          |

The important distinction is:

> **Standards tell us what constraints we should respect. Principles tell us how we should make decisions within those constraints.**

---

# 2. Architecture Principles

These should drive actual design decisions.

### P1 — Separation of Concerns

Each component should have a clear responsibility.

Bad:

```text
OrderController
 ├── validates request
 ├── calculates price
 ├── queries database
 ├── sends email
 ├── charges payment
 └── writes audit log
```

Better:

```text
OrderController
      ↓
OrderApplicationService
      ↓
OrderDomain
      ↓
Repositories / External Services
```

The controller handles HTTP concerns.

The application layer coordinates use cases.

The domain contains business rules.

Infrastructure handles technical details.

---

### P2 — Dependency Inversion

Business logic shouldn't depend directly on infrastructure.

```text
Domain/Application
       ↓
   IRepository
       ↑
PostgresRepository
```

Instead of:

```csharp
OrderService
    ↓
PostgresOrderRepository
```

use:

```csharp
public interface IOrderRepository
{
    Task<Order?> GetAsync(OrderId id);
    Task SaveAsync(Order order);
}
```

Then:

```csharp
public class OrderService
{
    private readonly IOrderRepository repository;

    public OrderService(IOrderRepository repository)
    {
        this.repository = repository;
    }
}
```

PostgreSQL becomes an implementation detail.

---

# 3. SOLID

Architecture should apply SOLID at the appropriate level.

### Single Responsibility

Don't create giant services.

Instead of:

```text
UserService
 ├── registration
 ├── authentication
 ├── payment
 ├── email
 ├── reporting
 └── file management
```

split responsibilities:

```text
IdentityService
PaymentService
NotificationService
FileService
ReportingService
```

### Open/Closed

Support extension without continuously modifying stable code.

For example:

```csharp
public interface IPaymentProvider
{
    Task<PaymentResult> PayAsync(PaymentRequest request);
}
```

Implementations:

```text
BkashPaymentProvider
NagadPaymentProvider
StripePaymentProvider
```

Adding another provider doesn't require rewriting the payment domain.

---

# 4. Prefer Composition Over Inheritance

Instead of:

```text
Animal
 ├── Dog
 ├── Cat
 ├── Bird
 └── ...
```

where behavior becomes increasingly complicated, compose capabilities:

```text
Animal
 ├── MovementBehavior
 ├── FeedingBehavior
 └── NotificationBehavior
```

This becomes particularly useful in configurable enterprise applications.

---

# 5. Domain-Driven Design

For complex systems, organize around **business capabilities**, not technical layers alone.

For an ERP:

```text
ERP
│
├── Sales
│   ├── Orders
│   ├── Customers
│   └── Pricing
│
├── Inventory
│   ├── Products
│   ├── Warehouses
│   └── Stock
│
├── Procurement
│   ├── Suppliers
│   └── PurchaseOrders
│
├── Accounting
│   ├── Ledger
│   └── Payments
│
└── HR
    ├── Employees
    └── Payroll
```

This is generally more maintainable than:

```text
Controllers
Services
Repositories
Models
Utilities
```

containing everything from every business area.

---

# 6. Architecture Style

Don't automatically choose microservices.

A useful decision progression is:

```text
Modular Monolith
      │
      ├── Need independent deployment?
      │
      ├── Need independent scaling?
      │
      ├── Strong bounded contexts?
      │
      ├── Independent teams?
      │
      └── Operational maturity?
             │
             ↓
         Microservices
```

For many systems:

```text
Angular
   ↓
API
   ↓
Modular Monolith
   ├── Identity
   ├── Sales
   ├── Inventory
   ├── Procurement
   └── Reporting
        ↓
    PostgreSQL
```

is preferable initially to:

```text
Angular
 ↓
API Gateway
 ↓
12 microservices
 ↓
Kafka
 ↓
multiple databases
 ↓
Redis
 ↓
service mesh
```

The second architecture introduces significant operational complexity.

---

# 7. Hexagonal / Clean Architecture

A strong default for business applications:

```text
              ┌──────────────────────┐
              │      Web / API        │
              └──────────┬───────────┘
                         │
                         ▼
┌──────────────────────────────────────────┐
│             APPLICATION                  │
│                                          │
│   Commands / Queries / Use Cases         │
└───────────────────┬──────────────────────┘
                    │
                    ▼
┌──────────────────────────────────────────┐
│                DOMAIN                    │
│                                          │
│ Entities │ Value Objects │ Rules         │
└───────────────────┬──────────────────────┘
                    │
             abstractions
                    │
                    ▼
┌──────────────────────────────────────────┐
│             INFRASTRUCTURE               │
│                                          │
│ PostgreSQL │ Redis │ Email │ Payment     │
└──────────────────────────────────────────┘
```

The dependency direction points inward.

---

# 8. Real-World Example — E-Commerce

Suppose we're building:

**Amazon-like order management.**

A customer places an order.

```text
Customer
   ↓
POST /orders
   ↓
OrderController
   ↓
CreateOrderCommand
   ↓
OrderApplicationService
   ↓
Order Domain
   ├── validate items
   ├── calculate total
   ├── apply discount
   └── create Order
   ↓
OrderRepository
   ↓
PostgreSQL
```

After successful creation:

```text
OrderCreated
     ↓
Event Bus
 ┌───┼─────────────┐
 ↓   ↓             ↓
Email Inventory  Analytics
```

The order service doesn't need to know how email or analytics works.

That's **loose coupling**.

---

# 9. Design Patterns

Patterns should solve identifiable problems.

### Factory

When object creation varies:

```csharp
IPaymentProvider provider =
    PaymentProviderFactory.Create(paymentMethod);
```

### Strategy

When an algorithm varies:

```text
PricingStrategy
 ├── RegularPricing
 ├── MemberPricing
 └── PromotionalPricing
```

### Adapter

When integrating external APIs:

```text
Application
    ↓
IPaymentGateway
    ↓
BkashAdapter
    ↓
bKash API
```

### Repository

When domain/application code shouldn't depend on persistence implementation:

```text
IOrderRepository
      ↑
PostgresOrderRepository
```

### Observer / Pub-Sub

When multiple components react to an event:

```text
OrderCreated
   ├── Email
   ├── Inventory
   ├── Loyalty
   └── Analytics
```

---

# 10. API Standards

A production API should have predictable conventions.

```http
POST /api/v1/orders
GET  /api/v1/orders/{id}
GET  /api/v1/orders
PUT  /api/v1/orders/{id}
DELETE /api/v1/orders/{id}
```

Use consistent response structures:

```json
{
  "data": {
    "id": "ORD-1001",
    "status": "CONFIRMED"
  },
  "errors": []
}
```

Validation:

```json
{
  "errors": [
    {
      "field": "customerId",
      "code": "CUSTOMER_NOT_FOUND",
      "message": "Customer does not exist."
    }
  ]
}
```

Document APIs using OpenAPI.

---

# 11. Security Architecture

Security should be architectural, not something added at the end.

```text
Client
  ↓
TLS
  ↓
API Gateway
  ↓
Authentication
  ↓
Authorization
  ↓
Application
  ↓
Domain
  ↓
Database
```

Use:

```text
Authentication
     +
Authorization
     +
Input Validation
     +
Least Privilege
     +
Encryption
     +
Audit Logging
     +
Secrets Management
```

For example:

```text
User
 ↓
OIDC
 ↓
Access Token
 ↓
API
 ↓
RBAC/Policy
 ↓
Order operation
```

---

# 12. Multi-Tenant Architecture

For a SaaS application:

```text
                    SaaS Platform
                         │
              ┌──────────┴──────────┐
              │                     │
           Tenant A              Tenant B
              │                     │
        ┌─────┴─────┐         ┌─────┴─────┐
        │           │         │           │
      Sales      Inventory   Sales     Inventory
```

Tenant identity must flow through the entire request:

```text
JWT
 ↓
TenantId
 ↓
Application Context
 ↓
Repository
 ↓
Tenant Data
```

Never trust a client-provided `tenantId` blindly.

---

# 13. Data Architecture

Separate:

```text
Transactional Data
        ↓
PostgreSQL
```

from:

```text
Caching
   ↓
Redis

Search
   ↓
OpenSearch/Elasticsearch

Analytics
   ↓
Data Warehouse
```

Don't put everything into one database simply because it's convenient.

But don't introduce five databases without a real requirement either.

---

# 14. Event-Driven Architecture

Example:

```text
Order Service
     │
     │ OrderCreated
     ▼
 Message Broker
     │
 ┌───┼──────────────┐
 ▼   ▼              ▼
Email Inventory   Analytics
```

Important principle:

> Events represent facts that have happened.

Good:

```text
OrderCreated
PaymentCompleted
ShipmentDispatched
```

Less useful:

```text
DoSomething
ProcessOrder
HandleStuff
```

---

# 15. Observability

Every production service should make it possible to answer:

**What happened?**

```text
Logs
  +
Metrics
  +
Traces
```

Example trace:

```text
HTTP Request
    │
    ├── Order Service 120ms
    │      ├── PostgreSQL 30ms
    │      ├── Pricing 20ms
    │      └── Payment 50ms
    │
    └── Event Publish 10ms
```

Use correlation/trace IDs:

```text
Request ID: 8f73...
Trace ID:   a91c...
```

---

# 16. Testing Architecture

Don't rely exclusively on unit tests.

```text
             E2E
             ▲
            / \
       Integration
          ▲
         / \
     Contract
        ▲
       / \
     Unit
```

For example:

### Unit

```text
DiscountCalculator
```

### Integration

```text
OrderService + PostgreSQL
```

### Contract

```text
Order API ↔ Payment Service
```

### E2E

```text
Login
 ↓
Add product
 ↓
Checkout
 ↓
Payment
 ↓
Order confirmation
```

---

# 17. Architecture Documentation

Every significant architectural decision should be documented.

Use an ADR:

```text
ADR-001

Title:
Use PostgreSQL as primary transactional database

Context:
The system requires relational transactions,
constraints and reporting.

Decision:
PostgreSQL will be the primary transactional store.

Alternatives:
MongoDB
MySQL

Consequences:
+ Strong transactions
+ Rich relational capabilities
- Horizontal scaling requires additional planning
```

This prevents architecture from existing only inside developers' heads.

---

# 18. CI/CD Architecture

A production pipeline could be:

```text
Developer
   ↓
Git
   ↓
Pull Request
   ↓
Build
   ↓
Unit Tests
   ↓
Integration Tests
   ↓
Security Scan
   ↓
Container Build
   ↓
Deploy Staging
   ↓
Smoke Tests
   ↓
Production
```

Deployment should be reproducible.

---

# 19. Architecture Governance

For larger teams:

```text
Architecture Principles
        ↓
Reference Architecture
        ↓
Design Review
        ↓
ADR
        ↓
Implementation
        ↓
Architecture Fitness Checks
```

Don't turn architecture governance into bureaucracy.

The purpose is to prevent expensive architectural mistakes.

---

# 20. The Complete Picture

A mature architecture can therefore look like:

```text
                         USERS
                           │
                           ▼
                    Web / Mobile
                           │
                           ▼
                     API Gateway
                           │
             ┌─────────────┴─────────────┐
             │                           │
       Authentication              Authorization
             │                           │
             └─────────────┬─────────────┘
                           ▼
                 APPLICATION LAYER
                           │
              ┌────────────┼────────────┐
              ▼            ▼            ▼
            Sales      Inventory    Procurement
              │            │            │
              └────────────┼────────────┘
                           ▼
                    DOMAIN LAYER
                           │
                    Domain Events
                           │
                           ▼
                     MESSAGE BUS
                 ┌─────────┼─────────┐
                 ▼         ▼         ▼
              Email     Analytics  Notification
                           
                           │
              ┌────────────┼────────────┐
              ▼            ▼            ▼
          PostgreSQL      Redis      Object Storage
              
                           │
                           ▼
                    OBSERVABILITY
             Logs + Metrics + Traces
```

And the **engineering principles underneath it** are:

```text
SOLID
  +
DDD
  +
Separation of Concerns
  +
Dependency Inversion
  +
Loose Coupling
  +
High Cohesion
  +
Explicit Boundaries
  +
Security by Design
  +
Observability
  +
Automated Testing
  +
Automated Delivery
  +
Documented Decisions
```

### A practical rule

Don't start with:

> "Which architecture pattern should I use?"

Start with:

> **What are the business boundaries, quality requirements, constraints, failure modes, and change patterns?**

Then select the architecture.

