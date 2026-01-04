---
description: Use this skill for CI/CD pipeline design during framework planning. Provides templates for GitHub Actions, GitLab CI, and deployment strategies.
---

# Deployment Pipeline Design

This skill guides CI/CD pipeline design during framework planning, providing templates and best practices for automated build, test, and deployment workflows.

---

## Pipeline Stages Overview

```
┌─────────┐   ┌─────────┐   ┌─────────┐   ┌─────────┐   ┌─────────┐
│  Build  │──▶│  Test   │──▶│  Scan   │──▶│  Stage  │──▶│  Prod   │
└─────────┘   └─────────┘   └─────────┘   └─────────┘   └─────────┘
    │             │             │             │             │
    ▼             ▼             ▼             ▼             ▼
 Compile      Unit Tests    Security     Deploy to    Deploy to
 Lint         Integration   SAST/DAST    Staging      Production
 Type Check   E2E Tests     Deps Audit   Smoke Test   Health Check
```

---

## GitHub Actions Template

### Basic Node.js Pipeline

```yaml
# .github/workflows/ci.yml
name: CI/CD Pipeline

on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main]

env:
  NODE_VERSION: '20'

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: ${{ env.NODE_VERSION }}
          cache: 'npm'

      - name: Install dependencies
        run: npm ci

      - name: Lint
        run: npm run lint

      - name: Type check
        run: npm run type-check

      - name: Build
        run: npm run build

      - name: Upload build artifacts
        uses: actions/upload-artifact@v4
        with:
          name: build
          path: dist/

  test:
    needs: build
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: ${{ env.NODE_VERSION }}
          cache: 'npm'

      - name: Install dependencies
        run: npm ci

      - name: Run unit tests
        run: npm run test:unit -- --coverage

      - name: Run integration tests
        run: npm run test:integration

      - name: Upload coverage
        uses: codecov/codecov-action@v4
        with:
          files: ./coverage/lcov.info

  security:
    needs: build
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Run security audit
        run: npm audit --audit-level=high

      - name: Run SAST
        uses: github/codeql-action/analyze@v3

  deploy-staging:
    needs: [test, security]
    if: github.ref == 'refs/heads/develop'
    runs-on: ubuntu-latest
    environment: staging
    steps:
      - uses: actions/checkout@v4

      - name: Download build
        uses: actions/download-artifact@v4
        with:
          name: build
          path: dist/

      - name: Deploy to staging
        run: |
          # Your deployment command here
          echo "Deploying to staging..."

      - name: Smoke tests
        run: npm run test:smoke -- --env=staging

  deploy-production:
    needs: [test, security]
    if: github.ref == 'refs/heads/main'
    runs-on: ubuntu-latest
    environment: production
    steps:
      - uses: actions/checkout@v4

      - name: Download build
        uses: actions/download-artifact@v4
        with:
          name: build
          path: dist/

      - name: Deploy to production
        run: |
          # Your deployment command here
          echo "Deploying to production..."

      - name: Health check
        run: curl -f ${{ vars.PROD_URL }}/health || exit 1
```

---

## GitLab CI Template

```yaml
# .gitlab-ci.yml
stages:
  - build
  - test
  - security
  - staging
  - production

variables:
  NODE_VERSION: "20"

default:
  image: node:${NODE_VERSION}
  cache:
    paths:
      - node_modules/

build:
  stage: build
  script:
    - npm ci
    - npm run lint
    - npm run type-check
    - npm run build
  artifacts:
    paths:
      - dist/
    expire_in: 1 hour

test:unit:
  stage: test
  script:
    - npm ci
    - npm run test:unit -- --coverage
  coverage: '/Lines\s*:\s*(\d+\.\d+)%/'
  artifacts:
    reports:
      coverage_report:
        coverage_format: cobertura
        path: coverage/cobertura-coverage.xml

test:integration:
  stage: test
  services:
    - postgres:15
    - redis:7
  variables:
    DATABASE_URL: "postgresql://postgres:postgres@postgres/test"
    REDIS_URL: "redis://redis:6379"
  script:
    - npm ci
    - npm run test:integration

security:audit:
  stage: security
  script:
    - npm audit --audit-level=high
  allow_failure: true

security:sast:
  stage: security
  image: returntocorp/semgrep
  script:
    - semgrep --config=auto .

deploy:staging:
  stage: staging
  environment:
    name: staging
    url: https://staging.example.com
  script:
    - echo "Deploying to staging..."
  rules:
    - if: $CI_COMMIT_BRANCH == "develop"

deploy:production:
  stage: production
  environment:
    name: production
    url: https://example.com
  script:
    - echo "Deploying to production..."
  rules:
    - if: $CI_COMMIT_BRANCH == "main"
  when: manual
```

---

## Environment Strategy

### Environment Progression

```
Feature Branch → develop → staging → main → production
      │             │          │         │         │
      ▼             ▼          ▼         ▼         ▼
   PR Tests    Integration   QA Test   Final    Live
                  Tests      Manual    Review
```

### Environment Configuration

| Environment | Purpose | Data | Access |
|-------------|---------|------|--------|
| Development | Local dev | Fake/seed | Developer |
| CI | Automated tests | Test fixtures | CI only |
| Staging | Pre-production | Anonymized prod | Team |
| Production | Live | Real | Restricted |

---

## Deployment Strategies

### Blue-Green Deployment

```
        ┌─────────────┐
        │  Load       │
        │  Balancer   │
        └──────┬──────┘
               │
       ┌───────┴───────┐
       │               │
┌──────▼──────┐ ┌──────▼──────┐
│   Blue      │ │   Green     │
│  (v1.0)     │ │  (v1.1)     │
│  [ACTIVE]   │ │  [STANDBY]  │
└─────────────┘ └─────────────┘

1. Deploy to Green (standby)
2. Test Green
3. Switch traffic to Green
4. Blue becomes standby
```

### Canary Deployment

```
        ┌─────────────┐
        │  Load       │
        │  Balancer   │
        └──────┬──────┘
               │
    ┌──────────┼──────────┐
    │          │          │
    ▼ (90%)    ▼ (10%)    │
┌───────────┐ ┌───────────┐
│  Stable   │ │  Canary   │
│  (v1.0)   │ │  (v1.1)   │
└───────────┘ └───────────┘

1. Deploy canary with 10% traffic
2. Monitor metrics
3. Gradually increase to 100%
4. Rollback if issues
```

### Rolling Deployment

```
Time 0: [v1] [v1] [v1] [v1]
Time 1: [v2] [v1] [v1] [v1]
Time 2: [v2] [v2] [v1] [v1]
Time 3: [v2] [v2] [v2] [v1]
Time 4: [v2] [v2] [v2] [v2]
```

---

## Secrets Management

### GitHub Secrets

```yaml
# Use in workflow
env:
  DATABASE_URL: ${{ secrets.DATABASE_URL }}
  API_KEY: ${{ secrets.API_KEY }}

# Or per-step
- name: Deploy
  env:
    TOKEN: ${{ secrets.DEPLOY_TOKEN }}
  run: deploy.sh
```

### Required Secrets

| Secret | Environment | Purpose |
|--------|-------------|---------|
| `DATABASE_URL` | staging, production | Database connection |
| `JWT_SECRET` | staging, production | Token signing |
| `DEPLOY_TOKEN` | CI | Deployment auth |
| `CODECOV_TOKEN` | CI | Coverage reporting |

---

## Pipeline Checklist

Before deploying:
- [ ] All tests pass
- [ ] Lint clean
- [ ] Type check passes
- [ ] Security scan clean
- [ ] Build succeeds
- [ ] Staging smoke tests pass

---

## Integration with Framework Developer

During Phase 3 (Planning) or Phase 6 (Integration):
1. Choose CI/CD platform
2. Define environment strategy
3. Create pipeline configuration
4. Set up secrets management
5. Configure deployment targets
6. Document in final report
