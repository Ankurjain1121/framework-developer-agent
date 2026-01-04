---
description: Use this skill during Phase 2 (Structure) to select appropriate architecture patterns. Covers MVC, Hexagonal, Clean Architecture, Microservices, and provides decision criteria for each.
---

# Architecture Patterns for Framework Development

This skill guides the selection of appropriate architecture patterns during Phase 2, providing decision criteria, trade-offs, and implementation guidance.

---

## Pattern Decision Matrix

| Pattern | Best For | Team Size | Complexity | Scalability |
|---------|----------|-----------|------------|-------------|
| **MVC** | Simple CRUD apps | 1-3 | Low | Medium |
| **Layered** | Traditional enterprise | 3-10 | Medium | Medium |
| **Hexagonal** | Domain-focused apps | 3-15 | Medium-High | High |
| **Clean** | Complex business logic | 5-20 | High | High |
| **Microservices** | Large distributed systems | 10+ | Very High | Very High |
| **Modular Monolith** | Growing apps, unclear boundaries | 3-10 | Medium | Medium-High |

---

## Pattern Details

### MVC (Model-View-Controller)

```
┌─────────────────────────────────────┐
│            Controller               │
│    (Handles requests, routes)       │
└────────────┬───────────┬────────────┘
             │           │
             ▼           ▼
      ┌──────────┐ ┌──────────┐
      │  Model   │ │   View   │
      │ (Data)   │ │   (UI)   │
      └──────────┘ └──────────┘
```

**When to Use:**
- Simple CRUD applications
- Quick prototypes
- Small team (1-3 developers)
- Short-lived projects

**Pros:**
- Simple to understand
- Fast to implement
- Well-documented pattern

**Cons:**
- Can become messy as app grows
- Tight coupling between layers
- Hard to test in isolation

**Example Structure:**
```
src/
├── controllers/
│   ├── userController.ts
│   └── taskController.ts
├── models/
│   ├── User.ts
│   └── Task.ts
└── views/
    └── (templates or React components)
```

---

### Layered Architecture

```
┌─────────────────────────────────────┐
│       Presentation Layer            │
│    (Controllers, Views, DTOs)       │
└─────────────────┬───────────────────┘
                  │
┌─────────────────▼───────────────────┐
│        Business Layer               │
│    (Services, Domain Logic)         │
└─────────────────┬───────────────────┘
                  │
┌─────────────────▼───────────────────┐
│         Data Access Layer           │
│    (Repositories, ORM)              │
└─────────────────┬───────────────────┘
                  │
┌─────────────────▼───────────────────┐
│           Database                  │
└─────────────────────────────────────┘
```

**When to Use:**
- Traditional enterprise apps
- Clear separation needed
- Team familiar with layers

**Pros:**
- Clear responsibilities
- Each layer independently deployable
- Easy to understand

**Cons:**
- Changes ripple through layers
- Can lead to anemic domain model
- Tight vertical coupling

---

### Hexagonal Architecture (Ports & Adapters)

```
                    ┌─────────────────┐
                    │   REST API      │
                    │   (Adapter)     │
                    └────────┬────────┘
                             │
┌─────────────┐    ┌─────────▼─────────┐    ┌─────────────┐
│   CLI       │◄───│                   │───►│  PostgreSQL │
│ (Adapter)   │    │   DOMAIN CORE     │    │  (Adapter)  │
└─────────────┘    │                   │    └─────────────┘
                   │   (Business       │
┌─────────────┐    │    Logic)         │    ┌─────────────┐
│  Message    │◄───│                   │───►│   Redis     │
│  Queue      │    │   [Ports define   │    │  (Adapter)  │
│ (Adapter)   │    │    interfaces]    │    └─────────────┘
└─────────────┘    └───────────────────┘
```

**When to Use:**
- Domain logic is complex
- Need to swap infrastructure
- Long-lived applications
- Multiple input/output channels

**Pros:**
- Domain isolated from infrastructure
- Easy to test (mock adapters)
- Swap databases/frameworks easily
- Clear boundaries

**Cons:**
- More initial setup
- Can feel over-engineered for simple apps
- Learning curve

**Example Structure:**
```
src/
├── domain/                 # Core business logic
│   ├── entities/
│   │   └── User.ts
│   ├── services/
│   │   └── UserService.ts
│   └── ports/              # Interfaces
│       ├── UserRepository.ts
│       └── NotificationService.ts
├── adapters/
│   ├── primary/            # Driving adapters
│   │   ├── rest/
│   │   │   └── UserController.ts
│   │   └── cli/
│   └── secondary/          # Driven adapters
│       ├── persistence/
│       │   └── PostgresUserRepository.ts
│       └── notification/
│           └── EmailNotificationService.ts
└── config/
    └── dependencies.ts     # Dependency injection
```

---

### Clean Architecture

```
┌─────────────────────────────────────────────┐
│              Frameworks & Drivers           │
│   (Web, DB, External Interfaces)            │
│  ┌───────────────────────────────────────┐  │
│  │         Interface Adapters            │  │
│  │   (Controllers, Presenters, Gateways) │  │
│  │  ┌─────────────────────────────────┐  │  │
│  │  │      Application Business       │  │  │
│  │  │         (Use Cases)             │  │  │
│  │  │  ┌───────────────────────────┐  │  │  │
│  │  │  │   Enterprise Business     │  │  │  │
│  │  │  │      (Entities)           │  │  │  │
│  │  │  └───────────────────────────┘  │  │  │
│  │  └─────────────────────────────────┘  │  │
│  └───────────────────────────────────────┘  │
└─────────────────────────────────────────────┘

Dependencies point INWARD only.
```

**When to Use:**
- Complex business rules
- Need extreme testability
- Long-term maintainability critical
- Large team

**Pros:**
- Highly testable
- Independent of frameworks
- Independent of UI
- Independent of database

**Cons:**
- Significant boilerplate
- Steep learning curve
- Over-engineering risk

---

### Microservices

```
┌──────────┐  ┌──────────┐  ┌──────────┐
│  User    │  │  Order   │  │ Payment  │
│ Service  │  │ Service  │  │ Service  │
└────┬─────┘  └────┬─────┘  └────┬─────┘
     │             │             │
     └──────┬──────┴──────┬──────┘
            │             │
       ┌────▼────┐   ┌────▼────┐
       │API GW   │   │Event Bus│
       └─────────┘   └─────────┘
```

**When to Use:**
- Large organization (10+ developers)
- Independent deployments needed
- Different scaling requirements
- Polyglot technology requirements

**Pros:**
- Independent deployment
- Technology flexibility
- Team autonomy
- Fault isolation

**Cons:**
- Network complexity
- Distributed transactions
- Operational overhead
- Hard to debug

**When NOT to Use:**
- Small team
- Simple application
- Unclear domain boundaries
- Limited DevOps capability

---

### Modular Monolith

```
┌────────────────────────────────────────┐
│           Single Deployable            │
│  ┌──────────┐ ┌──────────┐ ┌────────┐  │
│  │  Users   │ │  Orders  │ │Payment │  │
│  │  Module  │ │  Module  │ │ Module │  │
│  │          │ │          │ │        │  │
│  │ ┌──────┐ │ │ ┌──────┐ │ │┌──────┐│  │
│  │ │Domain│ │ │ │Domain│ │ ││Domain││  │
│  │ └──────┘ │ │ └──────┘ │ │└──────┘│  │
│  │ ┌──────┐ │ │ ┌──────┐ │ │┌──────┐│  │
│  │ │ API  │ │ │ │ API  │ │ ││ API  ││  │
│  │ └──────┘ │ │ └──────┘ │ │└──────┘│  │
│  └──────────┘ └──────────┘ └────────┘  │
│                                        │
│           Shared Infrastructure        │
└────────────────────────────────────────┘
```

**When to Use:**
- Growing application
- Unclear if microservices needed
- Want future flexibility
- Medium team (3-10)

**Pros:**
- Simpler than microservices
- Module boundaries enforced
- Easy to extract to microservice later
- Single deployment

**Cons:**
- Discipline needed to maintain boundaries
- Shared database can cause issues
- Not independently scalable

---

## Decision Flowchart

```
START
  │
  ▼
Is the domain complex?
  │
  ├─ NO → Is it a simple CRUD app?
  │         │
  │         ├─ YES → MVC
  │         └─ NO → Layered Architecture
  │
  └─ YES → Do you need independent deployments?
            │
            ├─ YES → Do you have 10+ developers?
            │         │
            │         ├─ YES → Microservices
            │         └─ NO → Modular Monolith
            │
            └─ NO → Need to swap infrastructure easily?
                     │
                     ├─ YES → Hexagonal
                     └─ NO → Clean Architecture
```

---

## Research Before Deciding

For each pattern considered:

```markdown
### Pattern Research: [Name]

**Official Sources:**
- [URL to authoritative source]

**Pros for Our Project:**
- [Specific benefit]

**Cons for Our Project:**
- [Specific drawback]

**Team Familiarity:** Low | Medium | High

**Verdict:** SUITABLE | NOT SUITABLE | NEEDS DISCUSSION
```

---

## Integration with Framework Developer

During Phase 2:
1. Analyze project complexity
2. Consider team size and experience
3. Research pattern options
4. Document decision as ADR
5. Create module hierarchy based on pattern
6. Update dependency graph
