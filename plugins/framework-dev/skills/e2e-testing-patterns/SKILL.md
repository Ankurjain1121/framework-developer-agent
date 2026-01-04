---
description: Use this skill for end-to-end testing strategy in multi-agent projects. Covers testing patterns, contract testing, and test plan templates for Phase 6 integration.
---

# End-to-End Testing Patterns

This skill guides testing strategy for multi-agent framework development, ensuring all components work together correctly.

---

## Testing Pyramid for Multi-Agent Projects

```
                    ┌─────────────┐
                   /   E2E Tests   \
                  /   (Few, Slow)   \
                 /───────────────────\
                /   Integration Tests  \
               /   (Medium, Moderate)   \
              /─────────────────────────\
             /       Unit Tests          \
            /     (Many, Fast)            \
           /───────────────────────────────\
          /        Contract Tests           \
         /   (Per Agent, Validates APIs)     \
        /─────────────────────────────────────\
```

---

## Testing Layers

### Layer 1: Contract Tests

Verify each agent's output matches API contracts.

```typescript
// tests/contracts/user-api.contract.test.ts
import { validateSchema } from './helpers';
import userSchema from '../schemas/user.json';

describe('User API Contract', () => {
  it('GET /users returns valid schema', async () => {
    const response = await fetch('/api/v1/users');
    const data = await response.json();

    expect(validateSchema(data, userSchema)).toBe(true);
  });

  it('POST /users returns 201', async () => {
    const response = await fetch('/api/v1/users', {
      method: 'POST',
      body: JSON.stringify({ email: 'test@test.com', name: 'Test' }),
    });

    expect(response.status).toBe(201); // Not 200!
  });
});
```

### Layer 2: Unit Tests

Test individual functions in isolation.

```typescript
// tests/unit/auth.test.ts
describe('Auth Service', () => {
  it('generates valid JWT token', () => {
    const token = authService.generateToken({ userId: '123' });
    const decoded = authService.verifyToken(token);

    expect(decoded.userId).toBe('123');
  });

  it('rejects expired tokens', () => {
    const expiredToken = createExpiredToken();

    expect(() => authService.verifyToken(expiredToken))
      .toThrow('Token expired');
  });
});
```

### Layer 3: Integration Tests

Test module interactions.

```typescript
// tests/integration/auth-flow.test.ts
describe('Auth Flow Integration', () => {
  it('login creates session and returns token', async () => {
    // Create test user
    const user = await createTestUser();

    // Login
    const response = await request(app)
      .post('/api/v1/auth/login')
      .send({ email: user.email, password: 'password' });

    expect(response.status).toBe(200);
    expect(response.body.token).toBeDefined();

    // Verify session created
    const sessions = await db.sessions.findByUser(user.id);
    expect(sessions.length).toBe(1);
  });
});
```

### Layer 4: E2E Tests

Test complete user flows.

```typescript
// tests/e2e/user-journey.test.ts
describe('User Journey', () => {
  it('new user can register, login, and create task', async () => {
    // Register
    await page.goto('/register');
    await page.fill('[name="email"]', 'new@user.com');
    await page.fill('[name="password"]', 'Password123');
    await page.click('button[type="submit"]');

    // Should redirect to dashboard
    await expect(page).toHaveURL('/dashboard');

    // Create task
    await page.click('[data-testid="new-task"]');
    await page.fill('[name="title"]', 'My First Task');
    await page.click('button[type="submit"]');

    // Verify task created
    await expect(page.locator('[data-testid="task-list"]'))
      .toContainText('My First Task');
  });
});
```

---

## Contract Testing for Multi-Agent Work

When multiple agents work on different parts:

### Define Contracts First

```yaml
# contracts/user-api.yaml
openapi: 3.0.0
paths:
  /api/v1/users:
    get:
      responses:
        '200':
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/UserList'
    post:
      responses:
        '201':  # IMPORTANT: 201 not 200
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/User'
```

### Generate Tests from Contracts

```typescript
// Auto-generate contract tests
import { generateContractTests } from './helpers';
import apiContract from '../contracts/user-api.yaml';

generateContractTests(apiContract);
```

### Verify Agent Output

Each agent's output must pass contract tests before handoff:

```markdown
## Handoff Quality Gate

Before accepting handoff:
- [ ] All contract tests pass
- [ ] Response schemas match
- [ ] Status codes correct
- [ ] Error formats consistent
```

---

## Test Plan Template

```markdown
# Integration Test Plan

**Project:** [Name]
**Phase:** 6 - Integration
**Date:** [Date]

---

## Scope

### In Scope
- [ ] User authentication flow
- [ ] Task CRUD operations
- [ ] Cross-module data flow

### Out of Scope
- Performance testing
- Security penetration testing

---

## Test Cases

### TC-001: User Registration Flow

**Description:** Verify new user can register
**Priority:** High
**Agent Owner:** FE-01 (Frontend)

**Prerequisites:**
- Database running
- Email service mocked

**Steps:**
1. Navigate to /register
2. Fill registration form
3. Submit form
4. Check email verification

**Expected Result:**
- User created in database
- Verification email sent
- Redirect to dashboard

**Actual Result:** [To be filled]
**Status:** PASS | FAIL | BLOCKED

---

### TC-002: API Contract Compliance

**Description:** Verify all endpoints match contracts
**Priority:** Critical
**Agent Owner:** QA-01

**Test Command:**
```bash
npm run test:contracts
```

**Expected Result:** All contract tests pass
**Status:** [To be filled]

---

## Test Environment

| Component | Version | URL |
|-----------|---------|-----|
| API Server | 1.0.0 | http://localhost:3000 |
| Database | PostgreSQL 15 | localhost:5432 |
| Frontend | 1.0.0 | http://localhost:5173 |

---

## Test Data

| Data Set | Purpose | Location |
|----------|---------|----------|
| seed.sql | Base data | tests/fixtures/ |
| users.json | Test users | tests/fixtures/ |

---

## Risk Assessment

| Risk | Impact | Mitigation |
|------|--------|------------|
| Flaky E2E tests | High | Retry mechanism |
| Test data pollution | Medium | Transaction rollback |

---

## Sign-off

| Role | Name | Status | Date |
|------|------|--------|------|
| Dev Lead | | | |
| QA Lead | | | |
| Product | | | |
```

---

## Testing Multi-Agent Handoffs

### Before Handoff

```typescript
// Run before sending handoff
describe('Pre-Handoff Validation', () => {
  it('all endpoints implemented', async () => {
    const endpoints = getContractEndpoints();
    for (const endpoint of endpoints) {
      const response = await fetch(endpoint.url, {
        method: endpoint.method,
      });
      expect(response.status).not.toBe(404);
    }
  });

  it('response schemas valid', async () => {
    // Validate all response schemas
  });
});
```

### After Receiving Handoff

```typescript
// Run when receiving handoff
describe('Post-Handoff Validation', () => {
  it('can connect to provided APIs', async () => {
    // Test connectivity
  });

  it('auth tokens work', async () => {
    // Verify auth integration
  });
});
```

---

## Test Commands

Add these to package.json:

```json
{
  "scripts": {
    "test": "npm run test:unit && npm run test:integration",
    "test:unit": "vitest run tests/unit",
    "test:integration": "vitest run tests/integration",
    "test:e2e": "playwright test",
    "test:contracts": "vitest run tests/contracts",
    "test:smoke": "vitest run tests/smoke",
    "test:coverage": "vitest run --coverage"
  }
}
```

---

## Integration with Phase 6

During Phase 6 (Integration):

1. **Collect test results from all agents**
2. **Run cross-module integration tests**
3. **Execute contract validation**
4. **Run E2E user journeys**
5. **Document any failures**
6. **Create resolution plan**
