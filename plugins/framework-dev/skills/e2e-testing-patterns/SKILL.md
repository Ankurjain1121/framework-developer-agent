---
description: Use this skill when the user asks about "e2e testing", "end-to-end tests", "Playwright", "Cypress", "integration testing", "test strategy", "testing multi-agent work", "test automation", or needs guidance on planning test approaches for framework projects that involve multiple agents or components.
---

# E2E Testing Patterns for Framework Development

This skill provides guidance on end-to-end testing strategies for framework projects, with special focus on testing work produced by multiple agents during Phase 5 (Execution) and Phase 6 (Integration).

## Testing in Multi-Agent Development

When work is divided among multiple agents:

```mermaid
flowchart TB
    subgraph Phase5["Phase 5: Agent Work"]
        A1[Backend Agent] --> C1[Backend Tests]
        A2[Frontend Agent] --> C2[Frontend Tests]
        A3[API Agent] --> C3[API Tests]
    end

    subgraph Phase6["Phase 6: Integration"]
        C1 --> Int[Integration Tests]
        C2 --> Int
        C3 --> Int
        Int --> E2E[E2E Tests]
    end

    E2E --> Report[Test Report]
```

## Test Strategy Layers

| Layer | Scope | Responsible Agent | Tools |
|-------|-------|-------------------|-------|
| Unit | Single function/component | Component owner | Jest, pytest, Go test |
| Integration | Module interactions | Integration agent | Supertest, pytest |
| Contract | API compliance | QA agent | Dredd, Schemathesis |
| E2E | Full user flows | QA agent | Playwright, Cypress |
| Visual | UI appearance | Frontend agent | Percy, Chromatic |

## Playwright Patterns

### Project Setup

```typescript
// playwright.config.ts
import { defineConfig, devices } from '@playwright/test';

export default defineConfig({
  testDir: './tests/e2e',
  fullyParallel: true,
  forbidOnly: !!process.env.CI,
  retries: process.env.CI ? 2 : 0,
  workers: process.env.CI ? 4 : undefined,
  reporter: [
    ['html', { outputFolder: 'playwright-report' }],
    ['junit', { outputFile: 'results.xml' }]
  ],

  use: {
    baseURL: process.env.BASE_URL || 'http://localhost:3000',
    trace: 'on-first-retry',
    screenshot: 'only-on-failure',
    video: 'retain-on-failure',
  },

  projects: [
    {
      name: 'chromium',
      use: { ...devices['Desktop Chrome'] },
    },
    {
      name: 'firefox',
      use: { ...devices['Desktop Firefox'] },
    },
    {
      name: 'mobile',
      use: { ...devices['iPhone 13'] },
    },
  ],

  webServer: {
    command: 'npm run start',
    url: 'http://localhost:3000',
    reuseExistingServer: !process.env.CI,
  },
});
```

### Page Object Model

```typescript
// tests/pages/TaskPage.ts
import { Page, Locator, expect } from '@playwright/test';

export class TaskPage {
  readonly page: Page;
  readonly taskList: Locator;
  readonly createButton: Locator;
  readonly titleInput: Locator;
  readonly submitButton: Locator;

  constructor(page: Page) {
    this.page = page;
    this.taskList = page.getByTestId('task-list');
    this.createButton = page.getByRole('button', { name: 'Create Task' });
    this.titleInput = page.getByLabel('Task Title');
    this.submitButton = page.getByRole('button', { name: 'Submit' });
  }

  async goto() {
    await this.page.goto('/tasks');
  }

  async createTask(title: string) {
    await this.createButton.click();
    await this.titleInput.fill(title);
    await this.submitButton.click();
    await expect(this.page.getByText(title)).toBeVisible();
  }

  async getTaskCount(): Promise<number> {
    return await this.taskList.getByTestId('task-item').count();
  }

  async waitForTasksLoaded() {
    await this.page.waitForResponse(
      response => response.url().includes('/api/tasks') && response.status() === 200
    );
  }
}
```

### Test Structure

```typescript
// tests/e2e/tasks.spec.ts
import { test, expect } from '@playwright/test';
import { TaskPage } from '../pages/TaskPage';
import { LoginPage } from '../pages/LoginPage';

test.describe('Task Management', () => {
  let taskPage: TaskPage;

  test.beforeEach(async ({ page }) => {
    // Login before each test
    const loginPage = new LoginPage(page);
    await loginPage.goto();
    await loginPage.login('test@example.com', 'password');

    taskPage = new TaskPage(page);
    await taskPage.goto();
  });

  test('should create a new task', async ({ page }) => {
    const taskTitle = `Test Task ${Date.now()}`;

    await taskPage.createTask(taskTitle);

    await expect(page.getByText(taskTitle)).toBeVisible();
  });

  test('should mark task as complete', async ({ page }) => {
    await taskPage.createTask('Complete me');

    await page.getByTestId('task-checkbox').first().click();

    await expect(page.getByTestId('task-item').first())
      .toHaveAttribute('data-status', 'completed');
  });

  test('should filter tasks by status', async ({ page }) => {
    await page.getByRole('combobox', { name: 'Status' }).selectOption('completed');

    const tasks = await page.getByTestId('task-item').all();
    for (const task of tasks) {
      await expect(task).toHaveAttribute('data-status', 'completed');
    }
  });
});
```

### API Mocking for Isolated Tests

```typescript
// tests/e2e/tasks-isolated.spec.ts
import { test, expect } from '@playwright/test';

test.describe('Task Management (Isolated)', () => {
  test.beforeEach(async ({ page }) => {
    // Mock API responses
    await page.route('**/api/tasks', async route => {
      if (route.request().method() === 'GET') {
        await route.fulfill({
          status: 200,
          body: JSON.stringify({
            data: [
              { id: '1', title: 'Mock Task 1', status: 'todo' },
              { id: '2', title: 'Mock Task 2', status: 'done' },
            ],
            meta: { totalCount: 2 }
          })
        });
      } else {
        await route.continue();
      }
    });
  });

  test('should display mocked tasks', async ({ page }) => {
    await page.goto('/tasks');

    await expect(page.getByText('Mock Task 1')).toBeVisible();
    await expect(page.getByText('Mock Task 2')).toBeVisible();
  });
});
```

## Cypress Patterns

### Project Setup

```typescript
// cypress.config.ts
import { defineConfig } from 'cypress';

export default defineConfig({
  e2e: {
    baseUrl: 'http://localhost:3000',
    specPattern: 'cypress/e2e/**/*.cy.{js,ts}',
    supportFile: 'cypress/support/e2e.ts',
    viewportWidth: 1280,
    viewportHeight: 720,
    video: true,
    screenshotOnRunFailure: true,
    retries: {
      runMode: 2,
      openMode: 0,
    },
  },
  component: {
    devServer: {
      framework: 'react',
      bundler: 'vite',
    },
  },
});
```

### Custom Commands

```typescript
// cypress/support/commands.ts
declare global {
  namespace Cypress {
    interface Chainable {
      login(email: string, password: string): Chainable<void>;
      createTask(title: string): Chainable<void>;
      apiLogin(): Chainable<void>;
    }
  }
}

Cypress.Commands.add('login', (email: string, password: string) => {
  cy.visit('/login');
  cy.get('[data-testid="email-input"]').type(email);
  cy.get('[data-testid="password-input"]').type(password);
  cy.get('[data-testid="submit-button"]').click();
  cy.url().should('not.include', '/login');
});

Cypress.Commands.add('apiLogin', () => {
  cy.request({
    method: 'POST',
    url: '/api/auth/login',
    body: {
      email: Cypress.env('TEST_EMAIL'),
      password: Cypress.env('TEST_PASSWORD'),
    },
  }).then((response) => {
    window.localStorage.setItem('token', response.body.token);
  });
});

Cypress.Commands.add('createTask', (title: string) => {
  cy.get('[data-testid="create-task-btn"]').click();
  cy.get('[data-testid="task-title-input"]').type(title);
  cy.get('[data-testid="submit-btn"]').click();
  cy.contains(title).should('be.visible');
});
```

### Test Structure

```typescript
// cypress/e2e/tasks.cy.ts
describe('Task Management', () => {
  beforeEach(() => {
    cy.apiLogin();
    cy.visit('/tasks');
  });

  it('should create a new task', () => {
    const taskTitle = `Test Task ${Date.now()}`;

    cy.createTask(taskTitle);

    cy.get('[data-testid="task-list"]')
      .should('contain', taskTitle);
  });

  it('should handle API errors gracefully', () => {
    cy.intercept('POST', '/api/tasks', {
      statusCode: 500,
      body: { error: 'Server error' },
    });

    cy.get('[data-testid="create-task-btn"]').click();
    cy.get('[data-testid="task-title-input"]').type('Will fail');
    cy.get('[data-testid="submit-btn"]').click();

    cy.get('[data-testid="error-toast"]')
      .should('contain', 'Failed to create task');
  });
});
```

## Test Strategy for Multi-Agent Work

### Contract Testing Between Agents

Ensure components built by different agents integrate correctly:

```typescript
// tests/contract/api-contract.spec.ts
import { test, expect } from '@playwright/test';
import Ajv from 'ajv';
import openApiSpec from '../../docs/api/openapi.json';

const ajv = new Ajv();

test.describe('API Contract Compliance', () => {
  test('GET /tasks matches OpenAPI schema', async ({ request }) => {
    const response = await request.get('/api/tasks');
    const body = await response.json();

    const schema = openApiSpec.components.schemas.TaskListResponse;
    const validate = ajv.compile(schema);
    const valid = validate(body);

    expect(valid).toBe(true);
    if (!valid) {
      console.error(validate.errors);
    }
  });

  test('POST /tasks validates request', async ({ request }) => {
    const response = await request.post('/api/tasks', {
      data: {
        title: 'ab', // Too short, should fail validation
        projectId: 'invalid-uuid',
      },
    });

    expect(response.status()).toBe(422);
    const body = await response.json();
    expect(body.errors).toBeDefined();
  });
});
```

### Integration Points Testing

Test handoff points between agent-owned components:

```typescript
// tests/integration/agent-handoffs.spec.ts
import { test, expect } from '@playwright/test';

test.describe('Agent Integration Points', () => {
  test.describe('Backend -> Frontend handoff', () => {
    test('frontend correctly renders API response format', async ({ page }) => {
      // Backend agent's API response
      await page.route('**/api/tasks/*', async route => {
        await route.fulfill({
          status: 200,
          body: JSON.stringify({
            data: {
              id: 'task-123',
              title: 'Integration Test',
              status: 'in_progress',
              priority: 'high',
              createdAt: '2025-01-15T10:00:00Z',
            }
          })
        });
      });

      await page.goto('/tasks/task-123');

      // Frontend agent's rendering
      await expect(page.getByTestId('task-title')).toHaveText('Integration Test');
      await expect(page.getByTestId('task-status')).toHaveText('In Progress');
      await expect(page.getByTestId('task-priority')).toHaveClass(/priority-high/);
    });
  });

  test.describe('Frontend -> API handoff', () => {
    test('frontend sends correct request format', async ({ page }) => {
      let capturedRequest: any;

      await page.route('**/api/tasks', async route => {
        capturedRequest = route.request().postDataJSON();
        await route.fulfill({
          status: 201,
          body: JSON.stringify({ data: { id: 'new-task' } })
        });
      });

      await page.goto('/tasks/new');
      await page.getByLabel('Title').fill('New Task');
      await page.getByLabel('Priority').selectOption('high');
      await page.getByRole('button', { name: 'Create' }).click();

      expect(capturedRequest).toMatchObject({
        title: 'New Task',
        priority: 'high',
      });
    });
  });
});
```

### Parallel Agent Work Testing

Test that independently developed features work together:

```typescript
// tests/e2e/feature-integration.spec.ts
import { test, expect } from '@playwright/test';

test.describe('Multi-Feature Integration', () => {
  test.beforeEach(async ({ page }) => {
    // Features developed by different agents
    await page.goto('/');
  });

  test('task creation triggers notification (Task Agent + Notification Agent)', async ({ page }) => {
    // Create task (Task Agent's feature)
    await page.goto('/tasks');
    await page.getByRole('button', { name: 'Create' }).click();
    await page.getByLabel('Title').fill('Important Task');
    await page.getByLabel('Assign to').selectOption('teammate@example.com');
    await page.getByRole('button', { name: 'Submit' }).click();

    // Check notification was sent (Notification Agent's feature)
    await page.goto('/notifications');
    await expect(page.getByText('Task assigned: Important Task')).toBeVisible();
  });

  test('project dashboard shows tasks and analytics (Dashboard + Tasks + Analytics Agents)', async ({ page }) => {
    await page.goto('/projects/proj-123');

    // Tasks section (Task Agent)
    await expect(page.getByTestId('project-tasks')).toBeVisible();

    // Analytics section (Analytics Agent)
    await expect(page.getByTestId('project-analytics')).toBeVisible();

    // Dashboard layout (Dashboard Agent)
    await expect(page.getByTestId('dashboard-grid')).toBeVisible();
  });
});
```

## CI/CD Integration

### GitHub Actions

```yaml
# .github/workflows/e2e.yml
name: E2E Tests

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  e2e:
    runs-on: ubuntu-latest

    services:
      postgres:
        image: postgres:15
        env:
          POSTGRES_PASSWORD: postgres
        ports:
          - 5432:5432

    steps:
      - uses: actions/checkout@v4

      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: '20'
          cache: 'npm'

      - name: Install dependencies
        run: npm ci

      - name: Install Playwright browsers
        run: npx playwright install --with-deps

      - name: Setup database
        run: npm run db:migrate
        env:
          DATABASE_URL: postgres://postgres:postgres@localhost:5432/test

      - name: Run E2E tests
        run: npm run test:e2e
        env:
          BASE_URL: http://localhost:3000
          DATABASE_URL: postgres://postgres:postgres@localhost:5432/test

      - name: Upload test results
        uses: actions/upload-artifact@v4
        if: failure()
        with:
          name: playwright-report
          path: playwright-report/
          retention-days: 7
```

### Test Reports for Multi-Agent Review

```typescript
// playwright.config.ts
export default defineConfig({
  reporter: [
    ['html', { outputFolder: 'playwright-report' }],
    ['json', { outputFile: 'test-results.json' }],
    ['github'],
    // Custom reporter for agent assignment
    ['./tests/reporters/agent-report.ts'],
  ],
});

// tests/reporters/agent-report.ts
import type { Reporter, TestCase, TestResult } from '@playwright/test/reporter';

class AgentReport implements Reporter {
  private results: Map<string, { passed: number; failed: number }> = new Map();

  onTestEnd(test: TestCase, result: TestResult) {
    // Extract agent from test path: tests/e2e/[agent]/feature.spec.ts
    const agent = test.location.file.split('/')[2] || 'shared';

    const stats = this.results.get(agent) || { passed: 0, failed: 0 };
    if (result.status === 'passed') {
      stats.passed++;
    } else {
      stats.failed++;
    }
    this.results.set(agent, stats);
  }

  onEnd() {
    console.log('\n=== Test Results by Agent ===\n');
    for (const [agent, stats] of this.results) {
      const status = stats.failed === 0 ? 'PASS' : 'FAIL';
      console.log(`${agent}: ${status} (${stats.passed}/${stats.passed + stats.failed})`);
    }
  }
}

export default AgentReport;
```

## Test Data Management

### Fixtures for Consistent Testing

```typescript
// tests/fixtures/tasks.ts
export const testTasks = {
  basic: {
    id: 'task-001',
    title: 'Basic Test Task',
    status: 'todo',
    priority: 'medium',
  },
  completed: {
    id: 'task-002',
    title: 'Completed Task',
    status: 'done',
    priority: 'low',
  },
  urgent: {
    id: 'task-003',
    title: 'Urgent Task',
    status: 'in_progress',
    priority: 'urgent',
    dueDate: new Date(Date.now() + 86400000).toISOString(),
  },
};

// Usage in tests
test('should display urgent task with warning', async ({ page }) => {
  await seedDatabase([testTasks.urgent]);
  await page.goto('/tasks');

  await expect(page.getByTestId('task-003')).toHaveClass(/urgent-warning/);
});
```

## Phase 6 Integration Testing Checklist

Before declaring integration complete:

- [ ] All agent components pass their unit tests
- [ ] Contract tests validate API compliance
- [ ] Integration tests cover agent handoff points
- [ ] E2E tests cover critical user flows
- [ ] Visual regression tests pass (if applicable)
- [ ] Performance benchmarks met
- [ ] Error scenarios tested
- [ ] Test coverage meets threshold
- [ ] CI pipeline passes consistently
- [ ] Test results documented per agent contribution
