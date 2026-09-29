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



## 21. Quality Attributes / Non-Functional Requirements

Architecture should be driven heavily by quality attributes, not only functional requirements.

```text
Functional Requirements
        +
Quality Attributes
        ↓
Architecture Decisions
```

Key attributes:

* Performance
* Scalability
* Availability
* Reliability
* Security
* Maintainability
* Modifiability
* Testability
* Usability
* Accessibility
* Interoperability
* Portability
* Recoverability
* Observability
* Deployability
* Cost efficiency

Example:

> "System must support 10,000 concurrent users."

is not merely an NFR.

It affects:

```text
Load balancing
Caching
Database design
Connection pooling
Async processing
Infrastructure
Monitoring
Capacity planning
```

---

# 22. Architecture Trade-offs

There is rarely a universally "best" architecture.

For example:

```text
Microservices
   +
Independent deployment
   +
Independent scaling
   -
Operational complexity
   -
Distributed transactions
   -
Network failures
   -
Observability complexity
```

Versus:

```text
Modular Monolith
   +
Simple deployment
   +
Simple transactions
   +
Easy debugging
   -
Less independent scaling
   -
Stronger deployment coupling
```

Architecture decisions should explicitly record:

```text
Decision
Why
Alternatives
Benefits
Costs
Risks
Consequences
```

---

# 23. Architecture Decision Records — ADR

This deserves its own area rather than being just documentation.

Example:

```text
ADR-014

Decision:
Use asynchronous events for notifications.

Context:
Order creation should not wait for email delivery.

Options:
1. Synchronous email
2. Background job
3. Event-driven messaging

Decision:
Event-driven messaging.

Reason:
Notification processing should be independently scalable
and must not increase order API latency.

Consequences:
+ Faster API
+ Independent retry
+ Better scalability
- Requires message infrastructure
- Eventual consistency
```

This becomes the **memory of the architecture**.

---

# 24. Architecture Fitness Functions

This is an important advanced concept.

Instead of saying:

> "We should maintain modularity."

make the architecture **automatically enforce it**.

For example:

```text
Sales.Domain
      X
      ↓
Infrastructure.Database
```

A test can fail the build if the domain directly references infrastructure.

Similarly:

```text
Domain
  ❌ HTTP
  ❌ SQL
  ❌ Redis
  ❌ Framework-specific infrastructure
```

while:

```text
API
  ↓
Application
  ↓
Domain
```

is permitted.

Architecture becomes **executable governance**.

---

# 25. Evolutionary Architecture

Architecture shouldn't be treated as permanently frozen.

Real systems evolve:

```text
v1
Monolith
  ↓
v2
Modular Monolith
  ↓
v3
Extract Reporting
  ↓
v4
Extract Payments
  ↓
v5
Selected Microservices
```

Don't prematurely design the final architecture.

Design for **evolution**.

---

# 26. Coupling and Cohesion

These two deserve special emphasis.

### High cohesion

Things that change together should stay together.

```text
Order
OrderItem
OrderPricing
OrderValidation
```

are naturally related.

### Low coupling

Unrelated modules shouldn't know each other's internals.

Bad:

```text
Inventory
   ↓ directly manipulates
Order database tables
```

Better:

```text
Inventory
   ↑
StockReservation API / Event
   ↑
Order
```

A good architecture aims for:

> **High cohesion + low coupling.**

---

# 27. Bounded Contexts

Especially important with DDD.

Consider a university:

```text
University
│
├── Admissions
├── Academic
├── Examination
├── Finance
├── Library
└── Student Affairs
```

"Student" doesn't necessarily mean exactly the same thing everywhere.

```text
Admissions.Student
Academic.Student
Finance.Student
```

may have different models and rules.

Trying to create one gigantic universal `Student` model can create unnecessary coupling.

---

# 28. Consistency Models

Distributed systems introduce a fundamental architectural decision:

```text
Strong Consistency
        vs
Eventual Consistency
```

Example:

Payment:

```text
Payment
   ↓
Order status
```

might require strong transactional guarantees.

Analytics:

```text
Order
   ↓
Analytics
```

can usually tolerate:

```text
Order created at 10:00:00
Analytics updated at 10:00:02
```

Architecture should deliberately decide where each model applies.

---

# 29. Distributed Systems Principles

If you move beyond a monolith, you need another set of principles.

### Network calls can fail.

Always assume:

```text
Timeout
Retry
Duplicate request
Partial failure
Network partition
Service unavailable
Slow response
```

Therefore:

```text
Service A
   ↓
Service B
```

needs:

```text
Timeout
Retry policy
Circuit breaker
Idempotency
Fallback
Observability
```

---

# 30. Resilience Engineering

Example:

```text
Payment Service
      ↓
    Timeout
      ↓
Retry with backoff
      ↓
Circuit Breaker
      ↓
Fallback / Pending state
```

Don't blindly retry payment operations.

A retry could potentially charge a customer twice unless the operation is idempotent.

Therefore:

```text
Idempotency-Key: PAY-12345
```

can ensure repeated requests don't create multiple payments.

---

# 31. Reliability Engineering

Important concepts:

```text
Availability
Reliability
Durability
Recoverability
```

For example:

```text
99%
≈ 3.65 days downtime/year

99.9%
≈ 8.76 hours/year

99.99%
≈ 52.6 minutes/year
```

So saying:

> "We need high availability"

is insufficient.

Define the actual target.

---

# 32. Disaster Recovery

Architecture must answer:

> What happens if the entire primary environment disappears?

Design:

```text
Backup
  ↓
Replication
  ↓
Recovery
  ↓
Validation
```

Important concepts:

### RPO

**Recovery Point Objective**

How much data can we afford to lose?

Example:

```text
RPO = 5 minutes
```

### RTO

**Recovery Time Objective**

How quickly must the system recover?

```text
RTO = 30 minutes
```

These directly affect architecture and cost.

---

# 33. Caching Strategy

Caching isn't simply:

> "Put Redis everywhere."

Determine:

```text
What?
Why?
TTL?
Invalidation?
Consistency?
Eviction?
Fallback?
```

Common approaches:

```text
Cache Aside

Application
    ↓
Cache ── hit → return
    │
   miss
    ↓
Database
    ↓
Cache
```

Other strategies:

* Read-through
* Write-through
* Write-behind
* Distributed cache
* Local cache
* CDN cache

---

# 34. Data Ownership

This becomes critical with microservices.

Bad:

```text
Order Service ─────┐
                    ↓
                 Same DB
                    ↑
Inventory Service ──┘
```

Both services manipulating each other's tables creates hidden coupling.

Better:

```text
Order Service
    ↓
Order DB

Inventory Service
    ↓
Inventory DB
```

Communication occurs through APIs/events.

---

# 35. API Versioning

Architecture needs a strategy for evolution.

```text
/api/v1/orders
/api/v2/orders
```

But versioning shouldn't automatically mean creating a new version for every change.

First consider backward-compatible changes:

```text
Add optional field
Add endpoint
Add response metadata
```

rather than:

```text
Break existing contract
```

---

# 36. Backward Compatibility

A mature architecture asks:

> What happens to old clients when the server changes?

Example:

```text
Mobile App v1
Mobile App v2
Web v3
External Client
        ↓
     API
```

All may coexist.

Therefore API evolution becomes an architectural concern.

---

# 37. Security Architecture

Go deeper than authentication.

You need:

```text
Identity
Authentication
Authorization
Secrets
Encryption
Network security
Data security
Audit
Threat modeling
Secure SDLC
Supply-chain security
```

And particularly:

### Zero Trust

Don't assume:

```text
"Internal network = trusted"
```

Instead:

```text
Every request
    ↓
Authenticate
    ↓
Authorize
    ↓
Validate
    ↓
Audit
```

---

# 38. Threat Modeling

Before implementation:

```text
System
 ↓
Assets
 ↓
Threats
 ↓
Attack surfaces
 ↓
Controls
 ↓
Residual risks
```

For example, an e-voting system:

```text
Assets
 ├── voter identity
 ├── ballot
 ├── election configuration
 └── results

Threats
 ├── unauthorized voting
 ├── voter impersonation
 ├── vote manipulation
 ├── double voting
 └── result tampering
```

Then architectural controls are designed around those threats.

---

# 39. Supply-Chain Architecture

Modern applications depend heavily on external packages.

```text
Your Application
      ↓
NuGet / npm / PyPI
      ↓
Third-party libraries
      ↓
Transitive dependencies
```

Therefore include:

```text
Dependency scanning
SBOM
Package pinning
Vulnerability scanning
License checking
Container scanning
Secret scanning
```

---

# 40. Deployment Architecture

Application architecture and deployment architecture are different.

For example:

```text
Application:

Angular
   ↓
.NET API
   ↓
PostgreSQL
```

Deployment:

```text
Internet
   ↓
CDN
   ↓
Load Balancer
   ↓
Kubernetes
 ┌────┼────┐
API  API   API
 └────┼────┘
      ↓
 PostgreSQL
```

Both need separate design consideration.

---

# 41. Infrastructure as Code

Infrastructure should be reproducible.

```text
Git
 ↓
Terraform
 ↓
Cloud infrastructure
```

Instead of manually configuring:

```text
Server
Database
Network
Firewall
Load balancer
```

and hoping someone remembers what they changed.

---

# 42. Configuration Management

Separate:

```text
Code
Configuration
Secrets
Environment
```

For example:

```text
Application
    ↓
Configuration
 ├── Development
 ├── Staging
 └── Production
```

Secrets should not live in:

```text
Git
appsettings.json
source code
Docker image
```

Use appropriate secret management.

---

# 43. Twelve-Factor / Cloud-Native Practices

For cloud-oriented applications:

```text
Stateless application
Externalized configuration
Disposable processes
Logs as streams
Explicit dependencies
Environment parity
Automated deployment
```

This makes applications easier to deploy and scale.

---

# 44. Performance Architecture

Performance must be designed rather than discovered after production.

Consider:

```text
Latency
Throughput
Concurrency
Database performance
Network latency
Serialization
Caching
Memory
CPU
I/O
```

Example:

```text
Request
 ↓
Cache?
 ↓
Database?
 ↓
External API?
```

Every unnecessary network/database call contributes latency.

---

# 45. Capacity Planning

Architecture should answer:

```text
Current:
1,000 users

Expected:
100,000 users

Peak:
20,000 concurrent users
```

Then estimate:

```text
Requests/sec
Database connections
CPU
Memory
Storage
Network
Queue depth
```

This prevents architecture from being based purely on guesswork.

---

# 46. Cost Architecture

A technically excellent system can still be commercially bad.

Evaluate:

```text
Infrastructure cost
Development cost
Operational cost
Monitoring cost
Licensing
Database cost
Data transfer
Engineering complexity
```

For example:

```text
10 microservices
+
Kafka
+
Kubernetes
+
Service Mesh
+
Multiple databases
```

might technically work while being completely unjustified for a small SaaS.

---

# 47. Architecture Patterns Catalogue

You should eventually master patterns in groups:

### Creational

```text
Singleton
Factory Method
Abstract Factory
Builder
Prototype
```

### Structural

```text
Adapter
Bridge
Composite
Decorator
Facade
Flyweight
Proxy
```

### Behavioral

```text
Chain of Responsibility
Command
Interpreter
Iterator
Mediator
Memento
Observer
State
Strategy
Template Method
Visitor
```

Then enterprise/distributed patterns:

```text
Repository
Unit of Work
Specification
CQRS
Event Sourcing
Saga
Outbox
Inbox
Circuit Breaker
Retry
Bulkhead
API Gateway
Backend-for-Frontend
Strangler Fig
Anti-Corruption Layer
```

---

# 48. Architecture Anti-Patterns

This is equally important.

Know what **not** to build.

Examples:

```text
God Object
God Service
Big Ball of Mud
Distributed Monolith
Shared Database
Circular Dependencies
Premature Microservices
Over-engineering
Chatty APIs
Anemic Domain Model
Spaghetti Architecture
Shared Mutable State
Magic Configuration
```

A particularly dangerous one:

### Distributed Monolith

Looks like microservices:

```text
Service A
Service B
Service C
Service D
```

but:

```text
A → B → C → D
```

must all deploy together.

You get:

> Microservice complexity without microservice independence.

---

# 49. Architecture Refactoring

You also need techniques for fixing existing systems.

Important patterns:

### Strangler Fig

```text
Old System
    │
    ├── Old functionality
    │
    └── New functionality
           ↓
       New System
```

Gradually replace the old system.

### Anti-Corruption Layer

```text
New Domain
    ↓
ACL
    ↓
Legacy System
```

Prevents legacy concepts from contaminating the new domain.

---

# 50. Architecture Review

Before implementation:

```text
Requirements
 ↓
Architecture
 ↓
Threat Model
 ↓
Trade-offs
 ↓
ADR
 ↓
Implementation
```

After implementation:

```text
Production
 ↓
Metrics
 ↓
Incidents
 ↓
Architecture Review
 ↓
Evolution
```

Architecture is therefore a **continuous engineering activity**, not a document created at project start.

---

# 51. The Full Architecture Learning Map

If you're building this as a serious **Software Architecture + Design** knowledge base, I'd organize everything we've discussed into this hierarchy:

```text
SOFTWARE ARCHITECTURE
│
├── 1. Requirements
│   ├── Functional
│   ├── NFR
│   └── Quality Attributes
│
├── 2. Principles
│   ├── SOLID
│   ├── DRY
│   ├── KISS
│   ├── YAGNI
│   ├── Separation of Concerns
│   ├── High Cohesion
│   └── Low Coupling
│
├── 3. Architecture Styles
│   ├── Layered
│   ├── Modular Monolith
│   ├── Clean
│   ├── Hexagonal
│   ├── Onion
│   ├── Microservices
│   ├── Event-Driven
│   └── Serverless
│
├── 4. Domain Architecture
│   ├── DDD
│   ├── Bounded Context
│   ├── Aggregates
│   ├── Entities
│   ├── Value Objects
│   └── Domain Events
│
├── 5. Design Patterns
│   ├── GoF
│   ├── Enterprise Patterns
│   └── Distributed Patterns
│
├── 6. Data Architecture
│   ├── Relational
│   ├── NoSQL
│   ├── Caching
│   ├── Data Ownership
│   ├── CQRS
│   └── Event Sourcing
│
├── 7. Integration
│   ├── REST
│   ├── GraphQL
│   ├── Messaging
│   ├── Events
│   └── Webhooks
│
├── 8. Security
│   ├── IAM
│   ├── OAuth/OIDC
│   ├── RBAC/ABAC
│   ├── Threat Modeling
│   └── Zero Trust
│
├── 9. Resilience
│   ├── Retry
│   ├── Timeout
│   ├── Circuit Breaker
│   ├── Bulkhead
│   ├── Idempotency
│   └── Disaster Recovery
│
├── 10. Scalability
│   ├── Horizontal
│   ├── Vertical
│   ├── Caching
│   ├── Queues
│   └── Partitioning
│
├── 11. Observability
│   ├── Logs
│   ├── Metrics
│   ├── Traces
│   └── Alerts
│
├── 12. DevOps
│   ├── CI/CD
│   ├── Containers
│   ├── IaC
│   └── Kubernetes
│
├── 13. Governance
│   ├── ADR
│   ├── Architecture Review
│   ├── Fitness Functions
│   └── Standards
│
├── 14. Evolution
│   ├── Refactoring
│   ├── Strangler Fig
│   ├── Migration
│   └── Legacy Modernization
│
└── 15. Economics
    ├── Cost
    ├── Complexity
    ├── Build vs Buy
    └── Operational Cost
```


**The next useful step is to turn this into a systematic course:** for each item, explain **why it exists → problem → bad design → principle → pattern → architecture → real-world scenario → C# implementation → when NOT to use it → trade-offs → interview/design-review questions**. That would give something substantially more useful than a list of definitions.
