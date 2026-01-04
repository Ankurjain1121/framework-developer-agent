---
description: Use this skill during Phase 4 (Agent Assignment) to match development tasks to optimal LLMs based on capabilities, context windows, cost, and task requirements. Includes capability matrix and assignment algorithm.
---

# LLM Capability Matching for Multi-Agent Development

This skill helps the Framework Developer Orchestrator assign tasks to the most suitable LLMs based on their capabilities, pricing, and the specific requirements of each task.

---

## LLM Capability Matrix (2025)

### Tier 1: Flagship Models

| Model | Context | Best For | Limitations | Cost (per 1M tokens) |
|-------|---------|----------|-------------|---------------------|
| **Claude Opus 4.5** | 200K | Complex reasoning, architecture, code quality | Slower | $15 in / $75 out |
| **Claude Sonnet 4** | 200K | Balanced coding, fast iteration | Less thorough | $3 in / $15 out |
| **GPT-4o** | 128K | Multimodal, vision tasks, UI/UX | Code style varies | $2.50 in / $10 out |
| **Gemini 2.0 Pro** | 2M | Massive context, research, docs | Code quality varies | $1.25 in / $5 out |
| **DeepSeek V3** | 128K | Code generation, refactoring | Less conversational | $0.27 in / $1.10 out |

### Tier 2: Fast/Efficient Models

| Model | Context | Best For | Limitations | Cost |
|-------|---------|----------|-------------|------|
| **Claude Haiku 3.5** | 200K | Quick tasks, simple edits | Less complex reasoning | $0.80 in / $4 out |
| **GPT-4o-mini** | 128K | Fast prototyping | Smaller capability | $0.15 in / $0.60 out |
| **Gemini 2.0 Flash** | 1M | High volume tasks | Lower accuracy | $0.075 in / $0.30 out |

---

## Task-to-LLM Matching Algorithm

### Step 1: Categorize the Task

| Category | Characteristics | Example Tasks |
|----------|-----------------|---------------|
| **Architecture** | High complexity, many dependencies | System design, API planning |
| **Backend** | Business logic, data handling | API endpoints, database queries |
| **Frontend** | UI/UX, visual components | React components, styling |
| **DevOps** | Infrastructure, deployment | CI/CD, Docker, Kubernetes |
| **Testing** | Test creation, coverage | Unit tests, integration tests |
| **Documentation** | Writing, explaining | README, API docs |
| **Refactoring** | Code improvement | Performance, cleanup |

### Step 2: Match Category to Optimal LLM

```
Architecture Tasks:
  1st choice: Claude Opus 4.5 (best reasoning)
  2nd choice: Claude Sonnet 4 (good balance)
  3rd choice: GPT-4o (alternative reasoning)

Backend Tasks:
  1st choice: Claude Sonnet 4 (fast, accurate)
  2nd choice: DeepSeek V3 (cost-effective)
  3rd choice: Claude Opus 4.5 (complex logic)

Frontend Tasks:
  1st choice: GPT-4o (UI/UX strength)
  2nd choice: Claude Sonnet 4 (React expertise)
  3rd choice: Claude Haiku 3.5 (simple components)

DevOps Tasks:
  1st choice: Claude Sonnet 4 (broad knowledge)
  2nd choice: Gemini 2.0 Pro (large context for configs)
  3rd choice: GPT-4o (infrastructure)

Testing Tasks:
  1st choice: Claude Sonnet 4 (thorough tests)
  2nd choice: DeepSeek V3 (cost-effective)
  3rd choice: Claude Haiku 3.5 (simple tests)

Documentation Tasks:
  1st choice: Gemini 2.0 Pro (massive context)
  2nd choice: Claude Opus 4.5 (quality writing)
  3rd choice: GPT-4o (clear explanations)

Refactoring Tasks:
  1st choice: DeepSeek V3 (code-focused)
  2nd choice: Claude Sonnet 4 (understands patterns)
  3rd choice: Claude Opus 4.5 (complex refactors)
```

### Step 3: Consider Constraints

| Constraint | Impact on Selection |
|------------|---------------------|
| Budget limited | Use Haiku, GPT-4o-mini, DeepSeek |
| Time critical | Use Sonnet, GPT-4o, Flash |
| Quality critical | Use Opus, GPT-4o |
| Large codebase | Use Gemini Pro (2M context) |
| Simple tasks | Use Haiku, Flash |

---

## Cost Estimation Formula

### Token Estimation by Task Type

| Task Type | Est. Input Tokens | Est. Output Tokens |
|-----------|-------------------|-------------------|
| Architecture design | 5,000 | 3,000 |
| API endpoint (each) | 2,000 | 1,500 |
| React component | 3,000 | 2,000 |
| Unit test file | 1,500 | 2,000 |
| Integration test | 3,000 | 2,500 |
| Documentation page | 2,000 | 3,000 |
| Refactor module | 4,000 | 3,000 |
| Debug session | 5,000 | 2,000 |

### Cost Calculation

```
Total Cost = Σ (task_input_tokens × input_price + task_output_tokens × output_price)

Example:
- 5 API endpoints with Claude Sonnet 4
- Input: 5 × 2,000 = 10,000 tokens × $3/1M = $0.03
- Output: 5 × 1,500 = 7,500 tokens × $15/1M = $0.11
- Total: $0.14
```

---

## Assignment Matrix Template

Generate this for each project:

```markdown
## Agent Assignment Matrix

| Agent ID | LLM | Tasks | Est. Tokens | Est. Cost | Status |
|----------|-----|-------|-------------|-----------|--------|
| BE-01 | Claude Sonnet 4 | Auth API, User API | 15K | $0.25 | Assigned |
| FE-01 | GPT-4o | Login UI, Dashboard | 20K | $0.30 | Assigned |
| QA-01 | DeepSeek V3 | Unit tests | 12K | $0.05 | Pending |
| DOC-01 | Gemini Pro | API docs | 8K | $0.02 | Pending |

**Total Estimated Cost:** $0.62
```

---

## Session Splitting Strategy

### When to Split Sessions

| Scenario | Recommendation |
|----------|----------------|
| > 50K tokens expected | Split into phases |
| Context loss risk | Checkpoint every 20K |
| Multiple modules | One session per module |
| Complex dependencies | Sequential sessions |

### Session Planning

```markdown
## Session Plan: Backend API

### Session 1: Auth Module
- LLM: Claude Sonnet 4
- Scope: JWT auth, middleware
- Handoff: Auth complete → User API

### Session 2: User API
- LLM: Claude Sonnet 4
- Scope: CRUD operations
- Dependency: Auth module complete
- Handoff: User API → Frontend

### Session 3: Integration
- LLM: Claude Opus 4.5
- Scope: Connect all modules
- Dependency: All modules complete
```

---

## LLM Strengths by Language/Framework

### Programming Languages

| Language | Best LLM | Rationale |
|----------|----------|-----------|
| TypeScript | Claude Sonnet 4 | Strong type inference |
| Python | Claude Sonnet 4 | Clean pythonic code |
| Rust | Claude Opus 4.5 | Complex ownership semantics |
| Go | DeepSeek V3 | Simple, fast generation |
| Java | GPT-4o | Enterprise patterns |

### Frameworks

| Framework | Best LLM | Rationale |
|-----------|----------|-----------|
| React/Next.js | Claude Sonnet 4 / GPT-4o | Both excellent |
| Vue/Nuxt | GPT-4o | Strong Vue knowledge |
| Express/Fastify | Claude Sonnet 4 | Clean Node.js |
| Django/FastAPI | Claude Sonnet 4 | Strong Python |
| Spring Boot | GPT-4o | Java ecosystem |
| Rails | Claude Sonnet 4 | Ruby conventions |

---

## Decision Flowchart

```
START
  ↓
Is task complex architecture?
  YES → Claude Opus 4.5
  NO ↓
Is budget constrained?
  YES → Is task simple?
    YES → Claude Haiku 3.5 or GPT-4o-mini
    NO → DeepSeek V3
  NO ↓
Is massive context needed?
  YES → Gemini 2.0 Pro
  NO ↓
Is it UI/visual work?
  YES → GPT-4o
  NO ↓
Default → Claude Sonnet 4
```

---

## Research Protocol

Before finalizing assignments, research each LLM:

```markdown
### LLM Research: [Model Name]

**Official Source:** [URL to official docs]

**Verified Capabilities:**
- Context window: [verified size]
- Pricing: [verified pricing]
- Strengths: [from official benchmarks]
- Limitations: [documented limits]

**User Experience Notes:**
- [Ask user about their experience]
- [Adjust recommendations based on feedback]
```

---

## Assignment Review Checklist

Before finalizing assignments:

- [ ] All tasks have an assigned LLM
- [ ] Cost estimates provided
- [ ] Token estimates reasonable
- [ ] Handoff points defined
- [ ] Session splitting planned
- [ ] User has approved assignments

---

## Quick Reference

### Best for Quality
- Architecture: Claude Opus 4.5
- Code: Claude Sonnet 4
- UI: GPT-4o

### Best for Speed
- Any task: Claude Haiku 3.5
- Simple: GPT-4o-mini

### Best for Cost
- Code: DeepSeek V3
- Bulk: Gemini Flash

### Best for Large Context
- Research/docs: Gemini 2.0 Pro (2M)
