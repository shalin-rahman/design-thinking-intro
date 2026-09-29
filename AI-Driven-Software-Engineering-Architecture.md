The AI changes **how requirements are captured, how architecture is designed, how code is produced, how decisions are verified, and how the system evolves**.

A useful model is:

> **Human intent → Specification → Architecture → AI-assisted implementation → Automated verification → Evidence → Feedback → Specification evolution**

Below is the continuation/reframing from **0**.

---

# AI-Driven Software Engineering & Architecture — From Zero

## 0. The Fundamental Shift

Traditional development:

```text
Human
  ↓
Requirements
  ↓
Design
  ↓
Code
  ↓
Testing
  ↓
Deployment
```

AI-driven development:

```text
                    HUMAN
                      │
                Intent / Goal
                      │
                      ▼
                SPECIFICATION
                      │
          ┌───────────┼───────────┐
          ▼           ▼           ▼
      Requirements  Rules      Constraints
          │           │           │
          └───────────┼───────────┘
                      ▼
                 ARCHITECTURE
                      │
          ┌───────────┼───────────┐
          ▼           ▼           ▼
       Design       Data        APIs
          │           │           │
          └───────────┼───────────┘
                      ▼
                 AI AGENTS
                      │
             ┌────────┼────────┐
             ▼        ▼        ▼
           Code     Tests     Docs
             │        │        │
             └────────┼────────┘
                      ▼
                 VERIFICATION
                      │
            ┌─────────┼─────────┐
            ▼         ▼         ▼
          Tests     Security   Review
            │         │         │
            └─────────┼─────────┘
                      ▼
                   EVIDENCE
                      │
                      ▼
                HUMAN APPROVAL
                      │
                      ▼
                 PRODUCTION
                      │
                      ▼
                  FEEDBACK
                      │
                      └────────────► SPECIFICATION
```

The **specification becomes the central source of engineering intent**.

This is especially relevant to the Specification-Driven Development direction you've been exploring with **SpecCraft**.

---

# 1. Start With Intent, Not Code

The first input should be something like:

> "I want a university academic portal where students can register courses, select electives, see fee status and track results."

AI should **not immediately generate code**.

First it should ask:

```text
What is the goal?
Who are the users?
What problem exists today?
What must the system do?
What must it NOT do?
What constraints exist?
What are the risks?
```

Then produce:

```text
Problem
Goals
Stakeholders
Requirements
Constraints
Assumptions
Questions
```

---

# 2. AI Requirements Engineering

Traditional:

```text
Stakeholder
   ↓
Analyst
   ↓
Requirement Document
```

AI-driven:

```text
Stakeholder
      ↓
Natural Language
      ↓
AI Requirements Analyst
      ↓
Structured Requirements
```

For example:

### Human statement

> "Students should be able to register for courses."

AI transforms it into:

```yaml
requirement:
  id: FR-REG-001
  actor: Student
  capability: Course Registration
  action: Register for a course
  preconditions:
    - student is authenticated
    - registration period is open
    - student satisfies prerequisites
  postconditions:
    - registration is recorded
  exceptions:
    - prerequisite not satisfied
    - course capacity exceeded
```

Now AI has something much more reliable to work with.

---

# 3. Requirement Clarification Agent

Human requirements are frequently incomplete.

Human:

> "Admin should approve results."

AI identifies ambiguity:

```text
Questions:

1. Which admin?
2. Can multiple admins approve?
3. Is approval sequential?
4. Can approval be rejected?
5. Can approved results be modified?
6. Is approval audited?
7. What happens after rejection?
8. Can approval be delegated?
```

This is far more valuable than generating another CRUD controller.

---

# 4. Requirement Classification

AI can classify incoming information:

```text
                INPUT
                  │
                  ▼
              AI Parser
                  │
       ┌──────────┼──────────┐
       ▼          ▼          ▼
 Requirement    Rule     Constraint
       │          │          │
       ▼          ▼          ▼
      FR         BR         NFR
```

For example:

> "Only registered students can vote."

AI can identify:

```text
Business Rule:
BR-VOTE-001

IF
user is registered

THEN
user may vote
```

---

# 5. Specification as the AI's Contract

Instead of giving the agent:

```text
"Build me an election system."
```

give it:

```text
Specification
 ├── Requirements
 ├── Actors
 ├── Business Rules
 ├── Workflows
 ├── Data Model
 ├── APIs
 ├── Security
 ├── NFRs
 ├── Tests
 └── Constraints
```

Then:

```text
AI Agent
   ↓
reads specification
   ↓
plans implementation
   ↓
implements
   ↓
verifies against specification
```

This dramatically reduces the "AI guessed what I meant" problem.

---

# 6. Specification Must Be Machine-Readable

Human-readable:

```text
Students cannot register for a course
after registration closes.
```

Structured:

```yaml
rule:
  id: BR-REG-004
  condition:
    registration_period: CLOSED
  effect:
    action: REJECT
  error: REGISTRATION_CLOSED
```

Now the same specification can drive:

```text
Code
Tests
Validation
Documentation
UI behavior
API validation
AI agents
```

This is one of the most important ideas in AI-driven development.

---

# 7. Architecture From Specification

AI should derive architecture from requirements rather than choose a fashionable architecture.

Input:

```text
Requirements
+
NFRs
+
Constraints
+
Expected scale
+
Security requirements
+
Team structure
```

AI proposes:

```text
Architecture Option A
Modular Monolith

Architecture Option B
Microservices

Architecture Option C
Serverless
```

Then explain:

```text
Why?
Trade-offs?
Risks?
Cost?
Operational complexity?
Migration implications?
```

The human makes the architectural decision.

---

# 8. Architecture Decision Agent

Instead of:

> "AI, choose microservices."

Ask:

```text
Given:

10,000 users
5 developers
moderate traffic
strong transactional requirements
limited DevOps resources

Evaluate:
Modular Monolith
Microservices
Serverless
```

AI should produce:

```text
Option
Advantages
Disadvantages
Risks
Operational complexity
Estimated infrastructure implications
Migration path
```

Not simply:

> "Microservices are best."

---

# 9. Architecture Knowledge Graph

This is where an AI-native architecture system becomes interesting.

Represent relationships:

```text
Requirement
    │
    ├── implemented by → Component
    │
    ├── constrained by → Rule
    │
    ├── verified by → Test
    │
    └── justified by → Decision
```

Example:

```text
FR-LOGIN-001
      │
      ├──→ AuthController
      ├──→ AuthService
      └──→ LoginTests

BR-AUTH-003
      │
      └──→ AuthService

ADR-007
      │
      └──→ OAuth/OIDC
```

Now AI can answer:

> "If I change this requirement, what could be affected?"

That is much more powerful than ordinary code search.

---

# 10. Traceability

Create a chain:

```text
Requirement
    ↓
Specification
    ↓
Architecture
    ↓
Design
    ↓
Code
    ↓
Test
    ↓
Evidence
```

Example:

```text
FR-PAY-003
   ↓
Payment Specification
   ↓
Payment Module
   ↓
PaymentService
   ↓
PaymentTests
   ↓
CI Evidence
```

AI can detect:

```text
Requirement has no implementation ❌

Implementation has no requirement ⚠️

Requirement has no test ❌

Test covers outdated requirement ⚠️
```

---

# 11. AI Architecture Review

After code exists:

```text
Specification
     ↓
Current Code
     ↓
AI Architecture Reviewer
     ↓
Differences
```

Example:

```text
Expected:

Domain
 ↓
Application
 ↓
Infrastructure

Actual:

Controller
 ↓
Entity Framework
 ↓
Domain
```

AI reports:

```text
Architecture Deviation:

ADR-012 requires dependency inversion.

Order.Domain currently references
Infrastructure.Persistence.

Severity: High

Suggested remediation:
Introduce IOrderRepository abstraction.
```

This is the **current implementation review** concept you've been considering for SpecCraft.

---

# 12. AI Should Review Its Own Work

Don't use one agent:

```text
User
 ↓
Coding Agent
 ↓
Done
```

Use multiple specialized roles:

```text
             Specification
                   │
                   ▼
             Architect Agent
                   │
                   ▼
             Developer Agent
                   │
          ┌────────┼────────┐
          ▼        ▼        ▼
       Test      Security  Review
       Agent      Agent     Agent
          │        │        │
          └────────┼────────┘
                   ▼
              Verification
```

This creates separation between:

**generation** and **verification**.

---

# 13. Agent Roles

A mature AI-driven engineering environment could contain:

```text
1. Requirements Agent
2. Clarification Agent
3. Domain Analyst
4. Architect Agent
5. API Designer
6. Database Designer
7. Security Agent
8. Coding Agent
9. Test Agent
10. Review Agent
11. Documentation Agent
12. DevOps Agent
13. Migration Agent
14. Observability Agent
15. Traceability Agent
```

But don't automatically create 15 autonomous agents.

Often:

```text
One capable agent
+
specialized workflows/tools
```

is simpler.

---

# 14. Agent Boundaries

Agents should have explicit permissions.

Example:

```text
Requirements Agent
 ├── READ specifications
 ├── WRITE requirements
 └── CANNOT modify production code

Coding Agent
 ├── READ specification
 ├── WRITE code
 ├── WRITE tests
 └── CANNOT approve itself

Deployment Agent
 ├── READ CI evidence
 ├── DEPLOY staging
 └── PRODUCTION requires approval
```

This is **least privilege applied to AI agents**.

---

# 15. Human-in-the-Loop

AI shouldn't autonomously make every important decision.

Use levels:

```text
Level 0
AI suggests

Level 1
AI prepares change → human approves

Level 2
AI executes low-risk changes

Level 3
AI executes within predefined policies

Level 4
AI autonomous operation
```

For production systems, the autonomy level should depend on risk.

---

# 16. AI Guardrails

Agents need constraints.

```text
Agent
 │
 ├── Tool permissions
 ├── File permissions
 ├── Branch permissions
 ├── Environment permissions
 ├── Token limits
 ├── Time limits
 ├── Cost limits
 └── Approval policies
```

Example:

```text
Production database:
READ → allowed
WRITE → human approval
DELETE → prohibited
```

---

# 17. AI Context Architecture

One of the biggest AI problems is:

> **What information does the agent actually know?**

Don't rely only on a giant prompt.

Build context systematically:

```text
                AI Agent
                   │
        ┌──────────┼──────────┐
        ▼          ▼          ▼
   Specifications  Code     History
        │          │          │
        ▼          ▼          ▼
     Decisions   Tests    Documentation
        │          │          │
        └──────────┼──────────┘
                   ▼
             Context Builder
                   │
                   ▼
                Model
```

---

# 18. Context Hierarchy

Not all context has equal authority.

For example:

```text
Priority 1
System / safety constraints

Priority 2
Project specification

Priority 3
Architecture decisions

Priority 4
Current code

Priority 5
Tests

Priority 6
Documentation

Priority 7
Conversation history

Priority 8
General model knowledge
```

You need explicit precedence rules.

Otherwise the AI can accidentally treat an outdated README as more authoritative than the current specification.

---

# 19. Retrieval-Augmented Engineering

RAG shouldn't only retrieve documents.

Retrieve:

```text
Requirements
ADRs
Architecture
Code
Tests
Issues
Git history
API contracts
Database schemas
```

Query:

> "Why is payment retry limited to three attempts?"

Context:

```text
ADR-019
Payment specification
PaymentService
RetryPolicy
Tests
Incident #102
```

AI can answer with evidence.

---

# 20. Evidence-First AI

An AI system for engineering should distinguish:

```text
FACT
```

from:

```text
INFERENCE
```

from:

```text
SUGGESTION
```

Example:

```text
FACT:
OrderService currently uses PostgreSQL.

SOURCE:
OrderService.cs
appsettings.json

INFERENCE:
This appears to be the primary transactional store.

SUGGESTION:
Consider repository abstraction.
```

This is much safer than presenting everything as fact.

---

# 21. AI Hallucination Control

For engineering, "sounds plausible" is not enough.

Use:

```text
Claim
 ↓
Evidence
 ↓
Verification
```

Example:

```text
AI:
"The system supports idempotent payment requests."

Verifier:
Search implementation.

Result:
No Idempotency-Key handling found.

Conclusion:
Claim unsupported.
```

AI should be able to say:

> **I don't have evidence for this.**

---

# 22. Specification Validation

Before coding:

```text
Specification
      ↓
Consistency Checker
```

Find:

```text
Contradictions
Missing requirements
Ambiguous terms
Duplicate requirements
Impossible constraints
Missing actors
Missing error cases
Missing security rules
```

Example:

```text
Rule A:
Only admins can approve results.

Rule B:
Teachers can approve results.

Conflict detected.
```

Human resolves it.

---

# 23. Specification Completeness

AI can calculate coverage—not a political/evaluative score, but engineering completeness checks.

For example:

```text
Requirement
   ├── Acceptance criteria ✓
   ├── Business rules ✓
   ├── Error cases ✓
   ├── Security ✓
   ├── API ✓
   ├── Data model ✓
   └── Tests ✗
```

Therefore:

```text
FR-023
Missing:
Test specification
```

---

# 24. AI-Generated Tests From Specifications

Specification:

```text
A student cannot register after
the registration period closes.
```

AI generates:

```text
Test:
Given registration period = CLOSED
And student is authenticated
When student registers
Then registration is rejected
And error = REGISTRATION_CLOSED
```

Then generate:

```text
Unit test
Integration test
API test
E2E test
```

The specification becomes the source.

---

# 25. Mutation / Adversarial Testing

AI shouldn't only generate happy-path tests.

For:

```text
Only registered users can vote.
```

generate:

```text
Unregistered user
Expired session
Duplicate request
Modified voter ID
Concurrent voting
Replay request
Invalid election ID
Closed election
Already-voted user
```

This is where AI can be particularly useful.

---

# 26. AI Security Engineering

Use AI to inspect:

```text
Authentication
Authorization
Input validation
SQL injection
XSS
CSRF
SSRF
Secrets
Dependencies
API exposure
Privilege escalation
```

But AI's security review should itself be verified with deterministic tools.

```text
AI Review
   +
SAST
   +
DAST
   +
Dependency Scanner
   +
Secret Scanner
```

AI is one layer, not the security system.

---

# 27. AI Architecture Drift Detection

Over time:

```text
Original Architecture
        ↓
Implementation
        ↓
6 months later
        ↓
Architecture Drift
```

Example:

```text
Original:

Domain → Application → Infrastructure


Current:

Domain → Infrastructure
Domain → HTTP
Application → UI
```

AI detects drift.

Then:

```text
Drift
 ↓
Impact analysis
 ↓
Refactoring proposal
 ↓
Human approval
```

---

# 28. AI Technical Debt Management

AI continuously examines:

```text
Code
Issues
Dependencies
Architecture
Tests
Incidents
ADRs
```

and identifies things like:

```text
Duplicated logic
Old dependencies
Dead code
Architecture violations
Missing tests
Complex modules
Outdated documentation
```

But don't let AI blindly "clean everything."

Every change should have:

```text
Reason
Impact
Risk
Tests
Rollback
```

---

# 29. AI Change Impact Analysis

Suppose you modify:

```text
Customer.email
```

AI traverses:

```text
Customer.email
    ↓
Domain
    ↓
Repository
    ↓
Database
    ↓
API
    ↓
Frontend
    ↓
Email service
    ↓
Tests
    ↓
Documentation
```

It can say:

```text
Potentially affected:

17 source files
4 APIs
2 database migrations
8 tests
3 specifications
1 ADR
```

This is one of the strongest arguments for maintaining a structured project knowledge graph.

---

# 30. AI-Assisted Migration

Legacy:

```text
.NET Framework
+
SQL Server
+
WebForms
```

Target:

```text
.NET
+
Angular
+
PostgreSQL
```

AI can:

```text
Analyze legacy code
       ↓
Recover requirements
       ↓
Recover domain model
       ↓
Map dependencies
       ↓
Generate specifications
       ↓
Propose target architecture
       ↓
Generate migration plan
       ↓
Implement incrementally
       ↓
Verify behavior
```

This is **reverse specification engineering**.

---

# 31. Code → Specification

Traditional:

```text
Specification → Code
```

AI-native systems should support:

```text
Code → Specification
```

and:

```text
Code ↔ Specification
```

For an existing system:

```text
Legacy Code
    ↓
AI Reverse Engineering
    ↓
Recovered Requirements
    ↓
Recovered Rules
    ↓
Recovered Workflows
    ↓
Recovered Architecture
```

Then humans validate what was recovered.

---

# 32. Specification → Code

The other direction:

```text
Requirement
 ↓
Rule
 ↓
Design
 ↓
Code
 ↓
Tests
```

This gives you a bidirectional engineering loop:

```text
         SPECIFICATION
          ↙         ↘
       CODE          TESTS
        ↑              ↓
        └──── EVIDENCE ┘
```

---

# 33. Model Independence

This is particularly important for AI-driven development.

Your project knowledge shouldn't belong to:

```text
GPT-X
Claude-X
Gemini-X
Copilot-X
Agent-X
```

Instead:

```text
                Project Knowledge
                       │
        ┌──────────────┼──────────────┐
        ▼              ▼              ▼
      Agent A        Agent B        Agent C
```

All agents consume the same:

```text
Specification
Architecture
Rules
Decisions
Traceability
```

Therefore changing models doesn't mean rebuilding project context.

This is exactly the architectural distinction between:

> **AI model** and **engineering knowledge base**.

---

# 34. Agent Interoperability

You want:

```text
IDE Agent
CLI Agent
Web Agent
CI Agent
Code Review Agent
```

to access the same project knowledge.

Architecture:

```text
                 Project Knowledge Layer
                          │
        ┌─────────────────┼─────────────────┐
        ▼                 ▼                 ▼
       IDE               CLI              CI/CD
        │                 │                 │
        └─────────────────┼─────────────────┘
                          ▼
                       Agents
```

---

# 35. Tool Calling Architecture

AI shouldn't directly "know" everything.

Give it tools:

```text
AI Agent
 │
 ├── search_specs()
 ├── get_requirement()
 ├── inspect_code()
 ├── query_architecture()
 ├── search_git()
 ├── run_tests()
 ├── run_linter()
 ├── run_security_scan()
 ├── create_branch()
 └── create_pull_request()
```

The model reasons.

The tools provide deterministic access to reality.

---

# 36. Agent Memory

Separate different types of memory.

```text
Agent Memory
│
├── Project Knowledge
│
├── Conversation Context
│
├── Task Context
│
├── Architectural Decisions
│
└── Learned Preferences
```

Do not mix them indiscriminately.

Project facts should be governed differently from conversational memories.

---

# 37. AI Workflow Orchestration

Instead of:

```text
User → Agent → Code
```

use workflows:

```text
User Request
     ↓
Clarification
     ↓
Specification Update
     ↓
Impact Analysis
     ↓
Architecture Check
     ↓
Implementation Plan
     ↓
Code
     ↓
Tests
     ↓
Security
     ↓
Review
     ↓
Evidence
     ↓
Approval
     ↓
Merge
```

Each stage can have explicit entry/exit conditions.

---

# 38. AI Development Gates

Example:

```text
SPECIFICATION GATE
       ↓
No unresolved ambiguity
       ↓
ARCHITECTURE GATE
       ↓
Architecture decision recorded
       ↓
IMPLEMENTATION GATE
       ↓
Code + tests
       ↓
VERIFICATION GATE
       ↓
All required tests pass
       ↓
SECURITY GATE
       ↓
No blocking vulnerabilities
       ↓
HUMAN APPROVAL
       ↓
MERGE
```

This is much safer than unrestricted autonomous coding.

---

# 39. AI-Native CI/CD

Traditional:

```text
Build
 ↓
Test
 ↓
Deploy
```

AI-enhanced:

```text
Build
 ↓
Test
 ↓
AI Change Review
 ↓
Specification Compliance
 ↓
Architecture Compliance
 ↓
Security Analysis
 ↓
Regression Analysis
 ↓
Deployment
```

---

# 40. Production Feedback → Specification

This is the final missing loop.

Suppose production shows:

```text
Payment timeout rate increased.
```

Traditional:

```text
Incident
 ↓
Fix code
 ↓
Done
```

AI-driven:

```text
Incident
 ↓
Analyze telemetry
 ↓
Identify behavior
 ↓
Compare specification
 ↓
Determine:
  Bug?
  Missing requirement?
  Incorrect assumption?
  Architecture limitation?
 ↓
Update specification
 ↓
Update tests
 ↓
Fix implementation
```

This prevents the specification from becoming obsolete.

---

# 41. The AI-Native Software Lifecycle

Put everything together:

```text
┌─────────────────────────────────────────────┐
│                  HUMAN                      │
│          Goals / Intent / Decisions         │
└──────────────────────┬──────────────────────┘
                       ▼
              REQUIREMENTS AGENT
                       │
                       ▼
              STRUCTURED SPEC
                       │
          ┌────────────┼────────────┐
          ▼            ▼            ▼
       Rules       Workflows      NFRs
          │            │            │
          └────────────┼────────────┘
                       ▼
               ARCHITECTURE AGENT
                       │
                       ▼
               ARCHITECTURE MODEL
                       │
             ┌─────────┼─────────┐
             ▼         ▼         ▼
          Domain      API       Data
             │         │         │
             └─────────┼─────────┘
                       ▼
                 CODING AGENT
                       │
                       ▼
                  CODE + TESTS
                       │
          ┌────────────┼────────────┐
          ▼            ▼            ▼
       TEST AGENT   SECURITY     REVIEW
                       AGENT       AGENT
          │            │            │
          └────────────┼────────────┘
                       ▼
                  VERIFICATION
                       │
                       ▼
                    EVIDENCE
                       │
                       ▼
                HUMAN APPROVAL
                       │
                       ▼
                  PRODUCTION
                       │
             ┌─────────┼─────────┐
             ▼         ▼         ▼
           Logs      Metrics    Traces
             │         │         │
             └─────────┼─────────┘
                       ▼
                  AI ANALYSIS
                       │
             ┌─────────┴─────────┐
             ▼                   ▼
       Code Improvement    Spec Improvement
             │                   │
             └─────────┬─────────┘
                       ▼
                 NEXT ITERATION
```

---

# 42. The Principles Change Too

Traditional software principles remain important, but AI introduces additional principles.

### P1 — Specification First

Don't let generated code become the primary source of intent.

### P2 — Evidence Over Assumption

AI claims should be grounded in project evidence.

### P3 — Deterministic Tools, Probabilistic Reasoning

Use AI for:

```text
Understanding
Reasoning
Generation
Planning
```

Use deterministic systems for:

```text
Compilation
Testing
Schema validation
Security scanning
Deployment
Policy enforcement
```

### P4 — Human Authority Over High-Impact Decisions

AI proposes.

Humans retain control over consequential decisions.

### P5 — Least Privilege for Agents

An agent should receive only the permissions it needs.

### P6 — Reproducibility

An AI-generated change should be reproducible from:

```text
Specification
Context
Prompt/instruction
Model/tool version
Code state
```

### P7 — Traceability

Every important implementation should be traceable to intent.

### P8 — Model Independence

Project knowledge should not be locked to one AI provider.

### P9 — Continuous Verification

Don't verify only at the end.

### P10 — Explicit Uncertainty

AI should distinguish:

```text
Known
Inferred
Unknown
Proposed
```

---

# 43. AI Architecture Standards

For an AI-driven engineering platform, I'd establish these standards:

| Area              | Standard                                |
| ----------------- | --------------------------------------- |
| Requirements      | Structured specifications               |
| Architecture      | C4 + ADR                                |
| Domain            | DDD where complexity warrants           |
| API               | OpenAPI                                 |
| Security          | OWASP + secure SDLC                     |
| AI security       | Least privilege + prompt/tool isolation |
| AI context        | Explicit project knowledge layer        |
| Agents            | Defined roles and permissions           |
| Tool access       | Allowlist + policy                      |
| Code              | SOLID + project conventions             |
| Testing           | Unit + integration + contract + E2E     |
| AI verification   | Independent review/validation           |
| Traceability      | Requirement → Code → Test → Evidence    |
| Observability     | Logs + metrics + traces                 |
| Deployment        | CI/CD + IaC                             |
| Governance        | ADR + architecture fitness checks       |
| AI outputs        | Evidence/claim provenance               |
| Model portability | Model-independent project knowledge     |
| Evolution         | Drift detection + impact analysis       |

---

# 44. AI-Specific Anti-Patterns

These are worth learning early.

### Prompt-Driven Architecture

```text
"Build a microservice architecture."
```

without requirements.

### AI-Generated Big Ball of Mud

Thousands of lines generated quickly but without boundaries.

### Context Dumping

```text
Entire repository
+
all documents
+
all tickets
+
all chat
→
LLM
```

More context does not automatically mean better context.

### Autonomous Production Changes

```text
AI
 ↓
Production
```

without meaningful controls.

### AI Self-Approval

```text
AI generates code
 ↓
same AI says code is good
 ↓
production
```

Generation and verification should have meaningful independence.

### Specification-Code Divergence

```text
Spec says A

Code does B
```

but nobody detects it.

### Model Lock-In

Project knowledge exists only inside one provider's proprietary context.

### AI Hallucinated Documentation

Documentation claims behavior that the code doesn't implement.

---

# 45. What an AI-Native Project Repository Could Look Like

This is where your **SpecCraft** idea becomes concrete.

```text
project/
│
├── specs/
│   ├── requirements/
│   ├── rules/
│   ├── workflows/
│   ├── domain/
│   ├── data/
│   ├── api/
│   ├── security/
│   ├── nfr/
│   └── tests/
│
├── architecture/
│   ├── c4/
│   ├── decisions/
│   ├── boundaries/
│   ├── dependencies/
│   └── threat-model/
│
├── traceability/
│   ├── requirement-code
│   ├── requirement-test
│   └── requirement-evidence
│
├── agents/
│   ├── requirements
│   ├── architect
│   ├── developer
│   ├── tester
│   └── reviewer
│
├── policies/
│   ├── architecture
│   ├── security
│   ├── coding
│   └── agents
│
├── evidence/
│   ├── test-results
│   ├── security
│   ├── architecture-review
│   └── deployments
│
└── src/
```

The important point:

> **`src/` is no longer the only important part of the project.**

The project knowledge becomes a first-class engineering artifact.

---

# 46. The Core AI Engineering Knowledge Graph

Ultimately you want something like:

```text
                    PROJECT
                       │
        ┌──────────────┼──────────────┐
        ▼              ▼              ▼
 Requirements     Architecture     Policies
        │              │              │
        ▼              ▼              ▼
       Rules         Decisions       Constraints
        │              │              │
        └──────────────┼──────────────┘
                       ▼
                     Domain
                       │
             ┌─────────┼─────────┐
             ▼         ▼         ▼
           APIs       Data      Workflows
             │         │         │
             └─────────┼─────────┘
                       ▼
                      Code
                       │
              ┌────────┼────────┐
              ▼        ▼        ▼
            Tests    Security   Docs
              │        │        │
              └────────┼────────┘
                       ▼
                    Evidence
                       │
                       ▼
                  Production
                       │
                       ▼
                    Feedback
                       │
                       └──────────► Requirements
```

That is the architecture I would use as the **mental foundation for AI-driven software engineering from zero**.

And the key idea is not "AI writes code faster."

It is:

> **AI can become an engineering participant because the project has an explicit, structured, traceable knowledge layer that defines what the system means, why it exists, how it should behave, and how we know the implementation is correct.**

That takes us naturally into the next level: **how to design the actual AI-driven architecture itself** — LLM layer, agents, MCP/tools, RAG, vector + graph storage, specification schema, orchestration, memory, permissions, evaluation, guardrails, Git/IDE/CI integration, and how all of those map onto a concrete implementation such as **SpecCraft** - https://github.com/shalin-rahman/SpecCraft.


Let's move from the **conceptual architecture** into the **actual architecture of an AI-driven software engineering platform**.

The goal here is not merely to build an AI coding assistant. We're designing a system that can understand a project, maintain its engineering knowledge, reason over it, make changes, and prove whether those changes conform to the project's intent.

# 47. AI-Driven Engineering Platform Architecture

At the highest level:

```text
                           HUMAN
                             │
                       Intent / Request
                             │
                             ▼
                  ┌─────────────────────┐
                  │   AI Engineering    │
                  │      Interface      │
                  └──────────┬──────────┘
                             │
                             ▼
                  ┌─────────────────────┐
                  │  Agent Orchestrator │
                  └──────────┬──────────┘
                             │
              ┌──────────────┼──────────────┐
              ▼              ▼              ▼
        Specification     Context        Policies
           Engine          Engine         Engine
              │              │              │
              └──────────────┼──────────────┘
                             ▼
                    ┌────────────────┐
                    │  AI / LLM      │
                    │     Layer      │
                    └───────┬────────┘
                            │
               ┌────────────┼────────────┐
               ▼            ▼            ▼
             Tools        Agents       Models
               │            │            │
               └────────────┼────────────┘
                            ▼
                   Project Environment
                            │
       ┌────────────────────┼────────────────────┐
       ▼                    ▼                    ▼
     Code                  Git                  CI/CD
       │                    │                    │
       └────────────────────┼────────────────────┘
                            ▼
                       Verification
                            │
              ┌─────────────┼─────────────┐
              ▼             ▼             ▼
            Tests        Security      Architecture
              │             │             │
              └─────────────┼─────────────┘
                            ▼
                         Evidence
                            │
                            ▼
                     Human Approval
```

---

# 48. Separate the AI Model From the Engineering System

This is fundamental.

Don't build:

```text
Application
    ↓
OpenAI API
    ↓
Everything
```

Instead:

```text
                  Engineering Platform
                         │
                Model Abstraction
                         │
          ┌──────────────┼──────────────┐
          ▼              ▼              ▼
        Model A        Model B        Model C
```

The engineering system owns:

* specifications
* project context
* permissions
* tools
* workflows
* policies
* traceability
* evaluation
* evidence

The model provides:

* reasoning
* language understanding
* generation
* planning

This makes the architecture **model-independent**.

---

# 49. Model Gateway

Create an abstraction:

```text
ILLMProvider
    │
    ├── OpenAIProvider
    ├── AnthropicProvider
    ├── GeminiProvider
    ├── LocalModelProvider
    └── OtherProvider
```

Example:

```csharp
public interface ILLMProvider
{
    Task<LLMResponse> CompleteAsync(
        LLMRequest request,
        CancellationToken cancellationToken);
}
```

Then your agent doesn't care which model is being used.

```text
Agent
  ↓
ILLMProvider
  ↓
Model Gateway
  ↓
Selected Model
```

---

# 50. Model Routing

Different tasks don't necessarily require the same model.

```text
Task
 │
 ├── Simple classification
 │       ↓
 │    Small model
 │
 ├── Code completion
 │       ↓
 │    Coding model
 │
 ├── Architecture reasoning
 │       ↓
 │    Strong reasoning model
 │
 └── Large repository analysis
         ↓
      Specialized workflow
```

The router can consider:

```text
Task complexity
Context size
Latency requirement
Cost
Required reasoning capability
Privacy
Model availability
```

This is better than sending every request to the largest model.

---

# 51. Agent Architecture

An agent isn't just an LLM.

A useful abstraction is:

```text
Agent =
    Model
  + System Instructions
  + Context
  + Tools
  + Memory
  + Policies
  + State
  + Evaluation
```

Architecture:

```text
             Agent
               │
       ┌───────┼────────┐
       ▼       ▼        ▼
     Model    Tools    Context
       │       │        │
       └───────┼────────┘
               ▼
             State
               │
               ▼
            Policy
```

---

# 52. Agent State Machine

Don't let an agent operate as an uncontrolled loop.

Define states:

```text
RECEIVED
   ↓
UNDERSTANDING
   ↓
PLANNING
   ↓
WAITING_FOR_CLARIFICATION
   ↓
EXECUTING
   ↓
VERIFYING
   ↓
AWAITING_APPROVAL
   ↓
COMPLETED
```

Failure:

```text
VERIFYING
   ↓
FAILED
   ↓
REPLAN
   ↓
EXECUTING
```

This makes agent behavior observable and controllable.

---

# 53. Tool Architecture

The model should interact with the world through explicit tools.

```text
Agent
 │
 ├── Specification
 │    ├── get_requirement
 │    ├── update_requirement
 │    └── search_rules
 │
 ├── Code
 │    ├── search_code
 │    ├── read_file
 │    ├── edit_file
 │    └── create_file
 │
 ├── Git
 │    ├── diff
 │    ├── branch
 │    ├── commit
 │    └── PR
 │
 ├── Verification
 │    ├── run_tests
 │    ├── build
 │    ├── lint
 │    └── security_scan
 │
 └── Architecture
      ├── get_architecture
      ├── check_dependency
      └── analyze_impact
```

The AI should **not** receive unrestricted shell access by default.

---

# 54. Tool Permission Model

For every tool:

```text
Tool
 │
 ├── Who can call?
 ├── What arguments?
 ├── What resources?
 ├── Read or write?
 ├── Environment?
 └── Approval required?
```

Example:

```text
run_tests
READ/EXECUTE
Staging
No approval

git_push
WRITE
Feature branch
No approval

merge_pull_request
WRITE
Protected branch
Approval required

production_deploy
WRITE
Production
Approval required
```

---

# 55. MCP / Tool Interoperability

An AI engineering platform can expose capabilities through standardized tool interfaces.

Conceptually:

```text
                   AI Agent
                      │
              Tool Protocol
                      │
       ┌──────────────┼──────────────┐
       ▼              ▼              ▼
   Git Server       Database        CI/CD
       │              │              │
    GitHub          PostgreSQL      Pipeline
```

The important architectural idea is:

> **Agents should interact with capabilities through well-defined interfaces rather than hard-coded integrations.**

That makes the platform extensible.

---

# 56. Context Engineering

This is more important than simply saying "use RAG."

The question is:

> **What context does this particular task actually need?**

For:

```text
Fix payment timeout
```

retrieve:

```text
Payment specification
Payment architecture
PaymentService
Retry policy
Relevant tests
Recent Git changes
Relevant incidents
ADR about payment
```

Don't retrieve:

```text
Entire repository
```

Context should be **task-specific**.

---

# 57. Context Assembly Pipeline

```text
User Request
     ↓
Intent Classification
     ↓
Task Identification
     ↓
Context Requirements
     ↓
Retrieve
 ┌───┼────┬────┬────┐
 ▼   ▼    ▼    ▼    ▼
Spec Code Git Tests ADR
 └───┼────┴────┴────┘
     ↓
Rank
     ↓
Deduplicate
     ↓
Resolve conflicts
     ↓
Context Package
     ↓
LLM
```

---

# 58. Multiple Knowledge Stores

Don't put everything into a vector database.

Use the right storage for the right information.

```text
                    Project Knowledge
                           │
       ┌───────────────────┼───────────────────┐
       ▼                   ▼                   ▼
 Relational Store      Vector Store        Graph Store
       │                   │                   │
 Structured data       Semantic search      Relationships
       │                   │                   │
 Requirements          Documents            Req → Code
 Rules                 Code chunks          ADR → Architecture
 Metadata              Discussions          Test → Requirement
```

And source code remains in:

```text
Git
```

The architecture can therefore use **polyglot knowledge storage**.

---

# 59. Vector Search

Useful for semantic retrieval.

Question:

> "Where do we validate election eligibility?"

Vector retrieval might find:

```text
EligibilityService.cs
ElectionRules.md
EligibilityTests.cs
ADR-009
```

Even if the exact phrase isn't present.

But vector search alone cannot reliably answer:

> "Which requirements are implemented by this service?"

That's a relationship question.

---

# 60. Graph Knowledge

Represent relationships explicitly:

```text
FR-023
  │
  ├── implemented_by → UserService
  │
  ├── verified_by → UserServiceTests
  │
  ├── constrained_by → BR-012
  │
  └── documented_by → API-USER-01
```

Graph queries can answer:

```text
What depends on FR-023?
```

or:

```text
Which tests are affected if BR-012 changes?
```

This is why a **knowledge graph + semantic retrieval** architecture is more capable than plain RAG for engineering.

---

# 61. Canonical Project Knowledge

You need a source-of-truth hierarchy.

For example:

```text
Specification
      ↓
Architecture Decision
      ↓
Current Implementation
      ↓
Tests
      ↓
Documentation
```

But there is an important nuance:

**The actual code is authoritative for what currently exists; the specification is authoritative for what the system is intended to do.**

Therefore AI should distinguish:

```text
INTENDED
vs
IMPLEMENTED
```

That distinction is extremely valuable.

---

# 62. Intent vs Reality

Example:

```text
Specification:

Users cannot vote twice.
```

Current implementation:

```text
Duplicate vote check missing.
```

AI shouldn't "correct" the specification to match the broken implementation.

It should report:

```text
Intent:
No duplicate voting.

Reality:
Duplicate vote prevention not detected.

Status:
Implementation deviation.
```

That is much more useful.

---

# 63. Traceability Engine

Create a dedicated service:

```text
TraceabilityService
```

It maintains relationships:

```text
Requirement
     ↓
Rule
     ↓
Use Case
     ↓
Component
     ↓
Code
     ↓
Test
     ↓
Evidence
```

Example:

```text
REQ-104
   │
   ├── RULE-12
   │
   ├── UC-08
   │
   ├── PaymentService
   │
   ├── PaymentController
   │
   ├── PaymentTests
   │
   └── CI-Run-8821
```

---

# 64. Change Impact Engine

Now traceability becomes actionable.

```text
Change:
REQ-104
   ↓
Graph traversal
   ↓
Affected nodes
```

Output:

```text
Affected:

Requirements: 1
Rules: 3
Workflows: 2
APIs: 2
Services: 3
Database entities: 2
Tests: 14
Documentation: 5
ADRs: 1
```

Then AI can generate a change plan.

---

# 65. Specification Compiler

This is a particularly powerful AI-native concept.

Think of specifications as a higher-level language.

```text
Human Intent
     ↓
Structured Specification
     ↓
Specification Compiler
     ↓
 ┌───┼────┬─────┬─────┐
 ▼   ▼    ▼     ▼     ▼
Code Tests API  Docs  Policies
```

For example:

```yaml
requirement:
  id: REQ-101
  actor: Student
  action: register_course

rules:
  - registration_period == OPEN
  - prerequisite_satisfied == true
  - capacity_available == true
```

From that, AI-assisted tooling can generate:

```text
API contract
Validation
Test cases
Documentation
Policy
Implementation skeleton
```

The generated artifacts remain subject to verification.

---

# 66. Specification Linter

Just as source code has:

```text
ESLint
Sonar
Roslyn analyzers
```

specifications can have:

```text
Spec Linter
```

Checks:

```text
Undefined actor
Missing acceptance criteria
Contradictory rule
Duplicate requirement
Circular dependency
Unbounded terminology
Missing error condition
Missing security constraint
Missing test
```

---

# 67. Architecture Linter

Likewise:

```text
Architecture
      ↓
Architecture Linter
```

Rules:

```text
Domain cannot reference Infrastructure
UI cannot access database
Service A cannot directly access Service B database
Public APIs require authentication
Payment operations require idempotency
```

Then:

```text
CI
 ↓
Architecture Linter
 ↓
FAIL
```

Architecture becomes enforceable.

---

# 68. AI Code Generation Should Be Plan-Based

Avoid:

```text
Prompt
 ↓
Generate 40 files
```

Instead:

```text
Requirement
 ↓
Impact Analysis
 ↓
Implementation Plan
 ↓
Human approval (if needed)
 ↓
Small change
 ↓
Compile
 ↓
Test
 ↓
Review
 ↓
Next change
```

This limits blast radius.

---

# 69. AI Coding Loop

A good autonomous coding loop:

```text
READ
 ↓
UNDERSTAND
 ↓
PLAN
 ↓
EDIT
 ↓
BUILD
 ↓
TEST
 ↓
INSPECT FAILURE
 ↓
FIX
 ↓
RETEST
 ↓
REVIEW
```

Not:

```text
WRITE EVERYTHING
 ↓
HOPE IT WORKS
```

---

# 70. Verification Must Be Multi-Layered

AI-generated code should pass:

```text
                    Generated Code
                          │
             ┌────────────┼────────────┐
             ▼            ▼            ▼
          Compile       Tests       Static Analysis
             │            │            │
             └────────────┼────────────┘
                          ▼
                     Security
                          │
                          ▼
                    Architecture
                          │
                          ▼
                    Specification
                          │
                          ▼
                    Human Review
```

Each catches different classes of problems.

---

# 71. AI Evaluation

You also need to evaluate the **AI system itself**.

Metrics can include:

```text
Task success rate
Requirement adherence
Test pass rate
Regression rate
Tool-call accuracy
Hallucination rate
Unsupported-claim rate
Context retrieval quality
Cost per task
Latency
Human correction rate
```

Don't evaluate an engineering agent solely by:

> "Did it generate code?"

---

# 72. AI Observability

Track every significant agent execution:

```text
Agent Run
 ├── User request
 ├── Specification version
 ├── Context sources
 ├── Model
 ├── Model version
 ├── Tools called
 ├── Files modified
 ├── Tests executed
 ├── Results
 ├── Human approvals
 └── Final outcome
```

This creates an **AI audit trail**.

---

# 73. Prompt / Instruction Versioning

Prompts and agent instructions are effectively software.

Treat them like code:

```text
agents/
 ├── architect/
 │    ├── v1
 │    ├── v2
 │    └── v3
 │
 └── tester/
      ├── v1
      └── v2
```

A change in instructions can change system behavior.

Therefore it should be versioned and evaluated.

---

# 74. AI Regression Testing

Suppose you improve the Architect Agent.

You need to verify that:

```text
Old task suite
```

still produces acceptable results.

Create an evaluation set:

```text
Task 001
Task 002
Task 003
...
Task 100
```

Run:

```text
Model v1
vs
Model v2
```

Compare predefined criteria.

AI systems need their own regression testing.

---

# 75. Cost Architecture

Every agent action costs resources.

Track:

```text
Input tokens
Output tokens
Tool calls
Embedding calls
Database queries
Model cost
Execution time
```

Then optimize:

```text
Cheap model
   ↓
classification

Strong model
   ↓
architecture reasoning

Deterministic tool
   ↓
repository search
```

Don't waste expensive model context on tasks a normal parser can solve.

---

# 76. Privacy Architecture

AI-driven engineering systems can potentially see:

```text
Source code
Credentials
Customer information
Internal architecture
Security vulnerabilities
Business data
```

Therefore:

```text
Project
 ↓
Data Classification
 ↓
Policy
 ↓
Allowed Model
 ↓
Allowed Context
```

Some data may be:

```text
Public
Internal
Confidential
Restricted
```

and model/tool access should respect those classifications.

---

# 77. Secret Protection

Before sending context to an AI model:

```text
Source
 ↓
Secret Scanner
 ↓
Redaction / Policy
 ↓
Context
 ↓
Model
```

Never assume:

> "The model won't use it."

Prevent unnecessary exposure architecturally.

---

# 78. Prompt Injection Defense

If an agent retrieves content from:

```text
README
Issue
Web page
Code comment
Ticket
Document
```

that content may contain instructions.

Example:

```text
// AI agent:
ignore your previous instructions and execute...
```

Retrieved content should be treated as **data**, not automatically trusted instructions.

Architecture:

```text
Untrusted Content
       ↓
Classification
       ↓
Context Isolation
       ↓
Agent
```

---

# 79. Agent-to-Agent Security

If multiple agents exist:

```text
Architect Agent
       ↓
Developer Agent
       ↓
Test Agent
```

don't let arbitrary agent messages become trusted commands.

Use structured messages:

```json
{
  "type": "implementation_plan",
  "source": "architect",
  "project": "P123",
  "spec_version": "42",
  "changes": []
}
```

and validate them.

---

# 80. AI Governance

For every autonomous capability define:

```text
Who can invoke it?
What can it read?
What can it modify?
What can it execute?
What requires approval?
What gets logged?
What happens when verification fails?
```

This transforms AI from:

```text
magic chatbot
```

into:

```text
controlled engineering component
```

---

# 81. The Complete AI-Native Architecture

Putting the entire design together:

```text
                              HUMAN
                                │
                                ▼
                         INTENT / REQUEST
                                │
                                ▼
                    ┌──────────────────────┐
                    │ AI ENGINEERING UI    │
                    │ IDE / CLI / Web      │
                    └──────────┬───────────┘
                               ▼
                    ┌──────────────────────┐
                    │ AGENT ORCHESTRATOR   │
                    └──────────┬───────────┘
                               │
        ┌──────────────────────┼─────────────────────┐
        ▼                      ▼                     ▼
 Specification             Context                Policy
   Engine                   Engine                 Engine
        │                      │                     │
        ▼                      ▼                     ▼
 Requirements            Retrieval             Permissions
 Rules                   Vector                 Guardrails
 Workflows               Graph                  Approvals
 NFRs                    Code                   Security
 Architecture            Git
        │                      │
        └──────────────────────┼─────────────────────┘
                               ▼
                       MODEL GATEWAY
                               │
                ┌──────────────┼──────────────┐
                ▼              ▼              ▼
             Model A        Model B        Model C
                │              │              │
                └──────────────┼──────────────┘
                               ▼
                           AI AGENTS
                               │
       ┌───────────────────────┼──────────────────────┐
       ▼                       ▼                      ▼
 Requirements              Architect              Developer
       │                       │                      │
       └───────────────────────┼──────────────────────┘
                               ▼
                            TOOLS
                               │
      ┌────────────────────────┼───────────────────────┐
      ▼                        ▼                       ▼
     Git                    IDE/FS                  CI/CD
      │                        │                       │
      ├── GitHub               ├── Code                ├── Build
      ├── GitLab               ├── Search              ├── Test
      └── History              └── Edit                └── Deploy
                               │
                               ▼
                         VERIFICATION
                               │
             ┌─────────────────┼─────────────────┐
             ▼                 ▼                 ▼
          Testing           Security         Architecture
             │                 │                 │
             └─────────────────┼─────────────────┘
                               ▼
                     SPECIFICATION CHECK
                               │
                               ▼
                          EVIDENCE
                               │
                               ▼
                       HUMAN APPROVAL
                               │
                               ▼
                          PRODUCTION
                               │
                ┌──────────────┼──────────────┐
                ▼              ▼              ▼
              Logs          Metrics         Traces
                │              │              │
                └──────────────┼──────────────┘
                               ▼
                         FEEDBACK ENGINE
                               │
               ┌───────────────┼───────────────┐
               ▼               ▼               ▼
          Spec Drift       Code Drift      New Requirements
               │               │               │
               └───────────────┼───────────────┘
                               ▼
                       KNOWLEDGE UPDATE
                               │
                               └──────────────► NEXT ITERATION
```

---

# 82. Where SpecCraft Fits

With this architecture, **SpecCraft should not try to become the LLM, IDE, GitHub, CI server, or another coding agent.**

Its strongest architectural position is the **engineering knowledge and specification layer**:

```text
                   SpecCraft
                       │
       ┌───────────────┼────────────────┐
       ▼               ▼                ▼
 Specification     Knowledge        Traceability
     Layer           Graph              Layer
       │               │                │
       └───────────────┼────────────────┘
                       │
          ┌────────────┼────────────┐
          ▼            ▼            ▼
        IDE          CLI          Agents
          │            │            │
          └────────────┼────────────┘
                       ▼
                     Code
                       │
                       ▼
                  Verification
```

That gives you a very clear product boundary:

> **SpecCraft is the project-level engineering knowledge layer that allows humans, AI agents, editors, CLI tools, and verification systems to share a consistent understanding of the software.**

And that leads to an even more important architectural concept:

```text
             ┌──────────────────────────┐
             │     PROJECT INTENT       │
             │                          │
             │ Requirements             │
             │ Rules                    │
             │ Workflows                │
             │ Architecture             │
             │ Decisions                │
             │ Constraints              │
             └────────────┬─────────────┘
                          │
                    SPECIFICATION
                          │
             ┌────────────┴────────────┐
             │                         │
             ▼                         ▼
         AI AGENTS                  HUMANS
             │                         │
             └────────────┬────────────┘
                          ▼
                      SOFTWARE
                          │
                          ▼
                       EVIDENCE
                          │
                          └─────────────► INTENT
```

That is the foundation I'd use before going into the **concrete implementation architecture**: service boundaries, database schema, specification DSL/schema, graph model, RAG pipeline, agent orchestration, MCP/tool layer, model gateway, security model, Git integration, CI integration, and the actual C#/.NET implementation.
