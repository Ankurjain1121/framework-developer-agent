# Model Strengths Reference

## Quick Reference

| Model | Context | Speed | Best For |
|-------|---------|-------|----------|
| Claude Opus | 200K | Slow | Complex architecture, critical logic |
| Claude Sonnet | 200K | Medium | General implementation, balanced |
| Claude Haiku | 200K | Fast | Simple edits, boilerplate |
| Gemini Pro | 1M+ | Medium | Long context, research, analysis |
| Gemini Flash | 1M+ | Fast | Quick analysis, large files |
| Qwen | 128K | Fast | Boilerplate, tests, migrations |

**Note:** Cost is not a factor - user has large usage allowances for all models.

---

## Claude Models

### Claude Opus
**Strengths:**
- Complex architectural decisions
- Multi-step reasoning
- Refactoring with context preservation
- Security-sensitive code
- Novel problem solving

**Assign when:**
- Core business logic changes
- Security implementations
- Complex state management
- API design decisions
- Performance-critical code

**Avoid for:**
- Simple CRUD operations
- Repetitive tasks
- Configuration changes

### Claude Sonnet
**Strengths:**
- Balanced quality/speed
- Good at following patterns
- Reliable implementations
- Code review
- Documentation

**Assign when:**
- General feature implementation
- Component creation
- Service layer code
- Hook implementations
- Most day-to-day coding

**Default choice** for most tasks.

### Claude Haiku
**Strengths:**
- Fast execution
- Low cost
- Good for simple tasks
- Pattern following

**Assign when:**
- Config file changes
- Simple type updates
- Import reorganization
- README updates
- Simple test cases

**Avoid for:**
- Complex logic
- Architecture decisions
- Security code

---

## Gemini Models

### Gemini Pro
**Strengths:**
- 1M+ token context
- Good at analysis
- Research tasks
- Large codebase understanding
- Documentation synthesis

**Assign when:**
- Analyzing large files (10K+ lines)
- Research across many files
- Documentation generation
- Understanding legacy code
- Comparative analysis

**Particularly good for:**
- "Understand this codebase" tasks
- "Find all usages of X" tasks
- "Document this system" tasks

### Gemini Flash
**Strengths:**
- Very fast
- Large context
- Good for scanning

**Assign when:**
- Quick file scanning
- Simple analysis
- Bulk file reading

---

## Qwen Models

### Qwen
**Strengths:**
- Fast execution
- Good at boilerplate
- Reliable for simple patterns
- Test generation
- Migration scripts

**Assign when:**
- Writing test files
- Simple CRUD components
- Database migrations
- Repetitive code patterns
- Config file generation

**Particularly good for:**
- "Write tests for X" (simple unit tests)
- "Create migration for Y"
- "Add basic CRUD for Z"

---

## Task → Model Mapping

| Task Type | Primary | Fallback |
|-----------|---------|----------|
| Architecture design | Opus | Sonnet |
| Core business logic | Opus/Sonnet | - |
| API implementation | Sonnet | - |
| Component creation | Sonnet | Haiku |
| Test writing | Qwen | Sonnet |
| Documentation | Gemini | Sonnet |
| Code analysis | Gemini | Sonnet |
| Config changes | Haiku | Qwen |
| Migrations | Qwen | Haiku |
| Refactoring | Sonnet | Opus |
| Bug fixes | Sonnet | - |
| Security code | Opus | Sonnet |
| Performance optimization | Opus | Sonnet |

---

## Multi-Terminal Distribution Example

For a feature with 45 files:

```
Terminal 1 (Claude Sonnet) - Core Logic
├── src/services/auth/auth.service.ts
├── src/services/auth/auth.queries.ts
└── src/lib/auth.ts

Terminal 2 (Claude Sonnet) - API Layer
├── src/app/api/auth/login/route.ts
├── src/app/api/auth/register/route.ts
└── src/app/api/auth/logout/route.ts

Terminal 3 (Gemini Pro) - Research & Analysis
├── Analyze existing auth patterns
├── Document current flow
└── Identify edge cases

Terminal 4 (Claude Sonnet) - Components
├── src/components/auth/login-form.tsx
├── src/components/auth/register-form.tsx
└── src/components/auth/auth-provider.tsx

Terminal 5 (Qwen) - Tests
├── src/services/auth/__tests__/auth.test.ts
├── src/components/auth/__tests__/login.test.tsx
└── e2e/auth.spec.ts

Terminal 6 (Qwen) - Migrations & Config
├── prisma/migrations/...
├── src/lib/auth-config.ts
└── .env.example updates
```

---

## Model Escalation

When a model fails or produces poor output:

```
Qwen failed → Retry with Claude Haiku
Haiku failed → Retry with Claude Sonnet
Sonnet failed → Retry with Claude Opus
Opus failed → Escalate to user

Gemini Flash failed → Retry with Gemini Pro
Gemini Pro failed → Retry with Claude Sonnet
```

---

## Capability-Based Assignment

| Strategy | When to Use |
|----------|-------------|
| Use Opus | Complex architecture, security, critical paths |
| Use Sonnet | General implementation, unknown complexity |
| Use Haiku | Simple known patterns, config changes |
| Use Gemini | Large context needed, research, analysis |
| Use Qwen | Tests, migrations, repetitive patterns |

**Rule:** Match model capability to task complexity. Use parallel models freely.
