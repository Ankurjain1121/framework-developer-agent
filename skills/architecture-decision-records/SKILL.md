---
description: Use this skill when documenting architectural decisions during framework planning. Provides ADR templates, numbering schemes, and integration with the decisions log.
---

# Architecture Decision Records (ADRs)

This skill provides guidance on documenting architectural decisions using the ADR format, ensuring all significant choices are recorded with context, rationale, and consequences.

---

## What is an ADR?

An Architecture Decision Record captures:
- **Context**: Why was this decision needed?
- **Decision**: What was decided?
- **Consequences**: What are the implications?

ADRs create a decision history that:
- Explains why the system is the way it is
- Helps new team members understand choices
- Prevents revisiting already-decided topics
- Provides audit trail for compliance

---

## ADR Template

```markdown
# ADR-{NNN}: {Title}

**Date:** {YYYY-MM-DD}
**Status:** PROPOSED | ACCEPTED | DEPRECATED | SUPERSEDED
**Deciders:** {Who made this decision}

## Context

{Describe the situation that requires a decision. What is the problem?
What forces are at play? What constraints exist?}

## Decision

{State the decision clearly. Use active voice: "We will..."}

## Alternatives Considered

### Alternative 1: {Name}
- **Description:** {What is this option?}
- **Pros:**
  - {Pro 1}
  - {Pro 2}
- **Cons:**
  - {Con 1}
  - {Con 2}
- **Source:** {URL to official documentation}

### Alternative 2: {Name}
- **Description:** {What is this option?}
- **Pros:**
  - {Pro 1}
- **Cons:**
  - {Con 1}
- **Source:** {URL}

## Rationale

{Why was this option chosen over others? What tipped the balance?}

## Consequences

### Positive
- {Positive consequence 1}
- {Positive consequence 2}

### Negative
- {Negative consequence 1}
- {Trade-off accepted}

### Neutral
- {Other implications}

## Related Decisions
- ADR-{NNN}: {Related decision}

## References
- {URL 1}
- {URL 2}
```

---

## ADR Lifecycle

```
PROPOSED → ACCEPTED → [DEPRECATED | SUPERSEDED]
    ↓          ↓              ↓
 Rejected   In effect    Replaced by new ADR
```

### Status Definitions

| Status | Meaning |
|--------|---------|
| PROPOSED | Under discussion, not yet decided |
| ACCEPTED | Decision made, in effect |
| DEPRECATED | No longer relevant (system changed) |
| SUPERSEDED | Replaced by a newer ADR |

---

## Numbering Scheme

```
ADR-{Category}{Sequence}

Categories:
  0XX - Project Setup (tooling, CI/CD)
  1XX - Architecture (patterns, structure)
  2XX - Technology (frameworks, libraries)
  3XX - Data (database, storage)
  4XX - Security (auth, encryption)
  5XX - Integration (APIs, third-party)
  6XX - Operations (deployment, monitoring)

Examples:
  ADR-001 - Use TypeScript
  ADR-101 - Adopt Hexagonal Architecture
  ADR-201 - Choose React for Frontend
  ADR-301 - Use PostgreSQL for Primary Database
  ADR-401 - Implement JWT Authentication
```

---

## When to Write an ADR

Write an ADR when:

| Scenario | Example |
|----------|---------|
| Choosing technology | Database, framework, library |
| Architecture pattern | Microservices vs monolith |
| Significant trade-off | Performance vs maintainability |
| Breaking change | Major API redesign |
| Security decision | Authentication method |
| Reversing previous decision | Changing database |

Don't write ADRs for:
- Trivial choices (variable naming)
- Temporary solutions (hotfixes)
- Already-standard practices

---

## ADR Examples

### ADR-301: Use PostgreSQL for Primary Database

**Date:** 2025-01-03
**Status:** ACCEPTED
**Deciders:** Framework Orchestrator, User

## Context

The application needs a primary database for storing user data, tasks, and relationships. The data is highly relational with complex queries needed. Expected scale is 100K users initially, growing to 1M.

## Decision

We will use PostgreSQL as the primary database.

## Alternatives Considered

### Alternative 1: PostgreSQL
- **Description:** Open-source relational database
- **Pros:**
  - Excellent for relational data
  - Strong ACID compliance
  - Mature ecosystem
  - JSON support for flexibility
- **Cons:**
  - Requires schema management
  - Horizontal scaling more complex
- **Source:** https://www.postgresql.org/docs/current/intro-whatis.html

### Alternative 2: MongoDB
- **Description:** Document-oriented NoSQL database
- **Pros:**
  - Flexible schema
  - Easy horizontal scaling
  - Good for unstructured data
- **Cons:**
  - Weaker for relational data
  - ACID only at document level
  - Query complexity for joins
- **Source:** https://www.mongodb.com/docs/

## Rationale

Our data model is inherently relational (users have tasks, tasks have subtasks, etc.). PostgreSQL's strong ACID compliance and mature tooling outweigh MongoDB's flexibility benefits for this use case. The JSON column type provides flexibility where needed.

## Consequences

### Positive
- Strong data integrity
- Complex queries are efficient
- Well-understood by developers

### Negative
- Need to manage migrations
- More upfront schema design required

### Neutral
- Will use Prisma ORM for type-safe access

## References
- https://www.postgresql.org/docs/
- https://www.prisma.io/docs/

---

## Integration with Framework Developer

### During Phase 1 (Discovery)

Record decisions about:
- Target platform
- Core technology choices
- Development approach

### During Phase 2 (Structure)

Record decisions about:
- Architecture pattern
- Module boundaries
- Key abstractions

### During Phase 3 (Planning)

Record decisions about:
- API design approach
- Data models
- Security strategy

### Storage Location

All ADRs go in: `.framework-blueprints/01-discovery/decisions-log.md`

Or as individual files:
```
.framework-blueprints/
└── decisions/
    ├── ADR-001-typescript.md
    ├── ADR-101-hexagonal-architecture.md
    └── ADR-301-postgresql.md
```

---

## Quick ADR Template

For rapid documentation during sessions:

```markdown
### D{NNN}: {Title}

**Chose:** {Option}
**Over:** {Rejected options}
**Because:** {One-line rationale}
**Source:** {URL}
**Trade-off:** {What we accept}
```

---

## ADR Review Checklist

Before finalizing an ADR:

- [ ] Context clearly explains the problem
- [ ] All reasonable alternatives listed
- [ ] Each alternative has pros/cons
- [ ] Sources are cited
- [ ] Decision is clearly stated
- [ ] Rationale explains why
- [ ] Consequences are realistic
- [ ] Related ADRs are linked

---

## Updating ADRs

### Superseding a Decision

When a decision needs to change:

1. Create new ADR with new decision
2. Update old ADR status to SUPERSEDED
3. Add link to new ADR

```markdown
**Status:** SUPERSEDED by ADR-302
```

### Deprecating a Decision

When a decision is no longer relevant:

1. Update status to DEPRECATED
2. Add note explaining why

```markdown
**Status:** DEPRECATED
**Note:** Feature was removed in v2.0
```
