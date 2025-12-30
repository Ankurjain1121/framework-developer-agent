---
description: Use this skill when the user asks about "CI/CD", "deployment pipeline", "GitHub Actions", "GitLab CI", "continuous deployment", "release management", "deployment automation", "rollback strategy", "approval gates", or needs guidance on planning deployment workflows for multi-component framework projects.
---

# Deployment Pipeline Design for Framework Development

This skill provides guidance on designing CI/CD pipelines for framework projects, with focus on multi-component deployments, approval gates between phases, and rollback strategies.

## Pipeline Architecture for Multi-Agent Projects

When multiple agents contribute to a project, the pipeline must:
- Validate each component independently
- Ensure integration before deployment
- Provide clear approval gates
- Enable independent and coordinated releases

```mermaid
flowchart TB
    subgraph Agents["Agent Contributions"]
        A1[Backend Agent] -->|PR| R1[Backend Repo/Branch]
        A2[Frontend Agent] -->|PR| R2[Frontend Repo/Branch]
        A3[API Agent] -->|PR| R3[API Repo/Branch]
    end

    subgraph CI["Continuous Integration"]
        R1 --> B1[Build Backend]
        R2 --> B2[Build Frontend]
        R3 --> B3[Build API]
        B1 --> T1[Test Backend]
        B2 --> T2[Test Frontend]
        B3 --> T3[Test API]
    end

    subgraph Integration["Integration Stage"]
        T1 --> Int[Integration Tests]
        T2 --> Int
        T3 --> Int
        Int --> E2E[E2E Tests]
    end

    subgraph CD["Continuous Deployment"]
        E2E --> Staging[Deploy Staging]
        Staging --> Approval{Manual Approval}
        Approval -->|Approved| Prod[Deploy Production]
        Approval -->|Rejected| Fix[Return to Agents]
    end
```

## GitHub Actions Pipeline

### Complete Workflow Structure

```yaml
# .github/workflows/ci-cd.yml
name: CI/CD Pipeline

on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main]
  workflow_dispatch:
    inputs:
      environment:
        description: 'Target environment'
        required: true
        default: 'staging'
        type: choice
        options:
          - staging
          - production

env:
  REGISTRY: ghcr.io
  IMAGE_NAME: ${{ github.repository }}

jobs:
  # Stage 1: Lint and Type Check
  quality:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: '20'
          cache: 'npm'

      - name: Install dependencies
        run: npm ci

      - name: Lint
        run: npm run lint

      - name: Type check
        run: npm run typecheck

      - name: Security audit
        run: npm audit --audit-level=high

  # Stage 2: Unit Tests
  test-unit:
    needs: quality
    runs-on: ubuntu-latest
    strategy:
      matrix:
        component: [backend, frontend, shared]
    steps:
      - uses: actions/checkout@v4

      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: '20'
          cache: 'npm'

      - name: Install dependencies
        run: npm ci

      - name: Run unit tests
        run: npm run test:unit --workspace=${{ matrix.component }}

      - name: Upload coverage
        uses: codecov/codecov-action@v4
        with:
          flags: ${{ matrix.component }}

  # Stage 3: Build
  build:
    needs: test-unit
    runs-on: ubuntu-latest
    outputs:
      version: ${{ steps.version.outputs.version }}
    steps:
      - uses: actions/checkout@v4

      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: '20'
          cache: 'npm'

      - name: Install dependencies
        run: npm ci

      - name: Build all packages
        run: npm run build

      - name: Generate version
        id: version
        run: echo "version=$(date +%Y%m%d%H%M%S)-${GITHUB_SHA::7}" >> $GITHUB_OUTPUT

      - name: Upload build artifacts
        uses: actions/upload-artifact@v4
        with:
          name: build-${{ steps.version.outputs.version }}
          path: |
            packages/*/dist
            apps/*/build
          retention-days: 7

  # Stage 4: Integration Tests
  test-integration:
    needs: build
    runs-on: ubuntu-latest
    services:
      postgres:
        image: postgres:15
        env:
          POSTGRES_PASSWORD: postgres
        ports:
          - 5432:5432
      redis:
        image: redis:7
        ports:
          - 6379:6379
    steps:
      - uses: actions/checkout@v4

      - name: Download build artifacts
        uses: actions/download-artifact@v4
        with:
          name: build-${{ needs.build.outputs.version }}

      - name: Setup test database
        run: npm run db:setup:test

      - name: Run integration tests
        run: npm run test:integration
        env:
          DATABASE_URL: postgres://postgres:postgres@localhost:5432/test
          REDIS_URL: redis://localhost:6379

  # Stage 5: E2E Tests
  test-e2e:
    needs: test-integration
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Download build artifacts
        uses: actions/download-artifact@v4
        with:
          name: build-${{ needs.build.outputs.version }}

      - name: Install Playwright
        run: npx playwright install --with-deps

      - name: Run E2E tests
        run: npm run test:e2e

      - name: Upload test report
        uses: actions/upload-artifact@v4
        if: always()
        with:
          name: e2e-report
          path: playwright-report/

  # Stage 6: Build Docker Images
  docker:
    needs: [test-e2e, build]
    runs-on: ubuntu-latest
    permissions:
      contents: read
      packages: write
    strategy:
      matrix:
        service: [api, web, worker]
    steps:
      - uses: actions/checkout@v4

      - name: Download build artifacts
        uses: actions/download-artifact@v4
        with:
          name: build-${{ needs.build.outputs.version }}

      - name: Set up Docker Buildx
        uses: docker/setup-buildx-action@v3

      - name: Login to Container Registry
        uses: docker/login-action@v3
        with:
          registry: ${{ env.REGISTRY }}
          username: ${{ github.actor }}
          password: ${{ secrets.GITHUB_TOKEN }}

      - name: Build and push
        uses: docker/build-push-action@v5
        with:
          context: .
          file: ./docker/${{ matrix.service }}/Dockerfile
          push: true
          tags: |
            ${{ env.REGISTRY }}/${{ env.IMAGE_NAME }}/${{ matrix.service }}:${{ needs.build.outputs.version }}
            ${{ env.REGISTRY }}/${{ env.IMAGE_NAME }}/${{ matrix.service }}:latest
          cache-from: type=gha
          cache-to: type=gha,mode=max

  # Stage 7: Deploy to Staging
  deploy-staging:
    needs: [docker, build]
    runs-on: ubuntu-latest
    environment:
      name: staging
      url: https://staging.example.com
    steps:
      - uses: actions/checkout@v4

      - name: Deploy to Kubernetes
        uses: azure/k8s-deploy@v4
        with:
          manifests: |
            k8s/staging/
          images: |
            ${{ env.REGISTRY }}/${{ env.IMAGE_NAME }}/api:${{ needs.build.outputs.version }}
            ${{ env.REGISTRY }}/${{ env.IMAGE_NAME }}/web:${{ needs.build.outputs.version }}
            ${{ env.REGISTRY }}/${{ env.IMAGE_NAME }}/worker:${{ needs.build.outputs.version }}

      - name: Wait for deployment
        run: kubectl rollout status deployment/api -n staging

      - name: Run smoke tests
        run: npm run test:smoke -- --base-url=https://staging.example.com

  # Stage 8: Production Approval Gate
  approve-production:
    needs: deploy-staging
    runs-on: ubuntu-latest
    environment:
      name: production-approval
    steps:
      - name: Approval checkpoint
        run: echo "Production deployment approved"

  # Stage 9: Deploy to Production
  deploy-production:
    needs: [approve-production, docker, build]
    runs-on: ubuntu-latest
    environment:
      name: production
      url: https://example.com
    steps:
      - uses: actions/checkout@v4

      - name: Deploy to Production
        uses: azure/k8s-deploy@v4
        with:
          manifests: |
            k8s/production/
          images: |
            ${{ env.REGISTRY }}/${{ env.IMAGE_NAME }}/api:${{ needs.build.outputs.version }}
            ${{ env.REGISTRY }}/${{ env.IMAGE_NAME }}/web:${{ needs.build.outputs.version }}
            ${{ env.REGISTRY }}/${{ env.IMAGE_NAME }}/worker:${{ needs.build.outputs.version }}
          strategy: canary
          percentage: 20

      - name: Monitor canary
        run: |
          sleep 300
          npm run test:smoke -- --base-url=https://example.com

      - name: Promote or rollback
        run: |
          if [ $? -eq 0 ]; then
            kubectl argo rollouts promote api -n production
          else
            kubectl argo rollouts abort api -n production
            exit 1
          fi
```

### Environment Protection Rules

Configure in GitHub Settings > Environments:

```yaml
# Staging environment
staging:
  reviewers: []  # Auto-deploy
  wait_timer: 0
  deployment_branch_policy:
    protected_branches: true

# Production approval
production-approval:
  reviewers:
    - team-leads
    - devops
  wait_timer: 0
  required_reviewers: 2

# Production environment
production:
  reviewers: []  # Already approved in previous stage
  wait_timer: 5  # 5-minute delay for final checks
  deployment_branch_policy:
    custom_branches:
      - main
```

## GitLab CI Pipeline

```yaml
# .gitlab-ci.yml
stages:
  - quality
  - test
  - build
  - integration
  - staging
  - approval
  - production

variables:
  DOCKER_REGISTRY: registry.gitlab.com
  IMAGE_TAG: $CI_COMMIT_SHORT_SHA

# Stage 1: Quality checks
lint:
  stage: quality
  image: node:20
  script:
    - npm ci
    - npm run lint
    - npm run typecheck
  cache:
    key: $CI_COMMIT_REF_SLUG
    paths:
      - node_modules/

security-scan:
  stage: quality
  image: node:20
  script:
    - npm audit --audit-level=high
  allow_failure: true

# Stage 2: Unit tests (parallel by component)
.test-template:
  stage: test
  image: node:20
  script:
    - npm ci
    - npm run test:unit --workspace=$COMPONENT
  coverage: '/All files[^|]*\|[^|]*\s+([\d\.]+)/'
  artifacts:
    reports:
      coverage_report:
        coverage_format: cobertura
        path: coverage/cobertura-coverage.xml

test-backend:
  extends: .test-template
  variables:
    COMPONENT: backend

test-frontend:
  extends: .test-template
  variables:
    COMPONENT: frontend

test-shared:
  extends: .test-template
  variables:
    COMPONENT: shared

# Stage 3: Build
build:
  stage: build
  image: node:20
  script:
    - npm ci
    - npm run build
  artifacts:
    paths:
      - packages/*/dist
      - apps/*/build
    expire_in: 1 week

# Stage 4: Docker images
docker-build:
  stage: build
  image: docker:24
  services:
    - docker:24-dind
  parallel:
    matrix:
      - SERVICE: [api, web, worker]
  script:
    - docker build -f docker/$SERVICE/Dockerfile -t $DOCKER_REGISTRY/$CI_PROJECT_PATH/$SERVICE:$IMAGE_TAG .
    - docker push $DOCKER_REGISTRY/$CI_PROJECT_PATH/$SERVICE:$IMAGE_TAG
  needs:
    - build

# Stage 5: Integration tests
integration-tests:
  stage: integration
  image: node:20
  services:
    - postgres:15
    - redis:7
  variables:
    DATABASE_URL: postgres://postgres:postgres@postgres:5432/test
    REDIS_URL: redis://redis:6379
  script:
    - npm ci
    - npm run db:setup:test
    - npm run test:integration
  needs:
    - test-backend
    - test-frontend
    - test-shared

e2e-tests:
  stage: integration
  image: mcr.microsoft.com/playwright:v1.40.0
  script:
    - npm ci
    - npm run test:e2e
  artifacts:
    when: always
    paths:
      - playwright-report/
    expire_in: 1 week
  needs:
    - integration-tests

# Stage 6: Deploy to staging
deploy-staging:
  stage: staging
  image: bitnami/kubectl:latest
  environment:
    name: staging
    url: https://staging.example.com
  script:
    - kubectl apply -k k8s/overlays/staging
    - kubectl set image deployment/api api=$DOCKER_REGISTRY/$CI_PROJECT_PATH/api:$IMAGE_TAG -n staging
    - kubectl set image deployment/web web=$DOCKER_REGISTRY/$CI_PROJECT_PATH/web:$IMAGE_TAG -n staging
    - kubectl rollout status deployment/api -n staging
  needs:
    - e2e-tests
    - docker-build
  only:
    - main

# Stage 7: Manual approval for production
approve-production:
  stage: approval
  script:
    - echo "Production deployment approved"
  when: manual
  allow_failure: false
  only:
    - main
  needs:
    - deploy-staging

# Stage 8: Deploy to production
deploy-production:
  stage: production
  image: bitnami/kubectl:latest
  environment:
    name: production
    url: https://example.com
  script:
    - kubectl apply -k k8s/overlays/production
    - kubectl set image deployment/api api=$DOCKER_REGISTRY/$CI_PROJECT_PATH/api:$IMAGE_TAG -n production
    - kubectl set image deployment/web web=$DOCKER_REGISTRY/$CI_PROJECT_PATH/web:$IMAGE_TAG -n production
    - kubectl rollout status deployment/api -n production
  needs:
    - approve-production
  only:
    - main
```

## Multi-Component Deployment Strategies

### Strategy 1: Coordinated Release

All components deploy together:

```mermaid
flowchart LR
    Build[Build All] --> Test[Test All]
    Test --> Stage[Deploy All to Staging]
    Stage --> Approve{Approval}
    Approve --> Prod[Deploy All to Production]
```

Best for: Tightly coupled components, breaking API changes

### Strategy 2: Independent Releases

Each component deploys independently:

```mermaid
flowchart TB
    subgraph API["API Component"]
        A1[Build] --> A2[Test] --> A3[Deploy]
    end

    subgraph Web["Web Component"]
        W1[Build] --> W2[Test] --> W3[Deploy]
    end

    subgraph Worker["Worker Component"]
        K1[Build] --> K2[Test] --> K3[Deploy]
    end
```

Best for: Loosely coupled microservices, frequent updates

### Strategy 3: Canary Deployment

Gradual rollout with automatic rollback:

```yaml
# Argo Rollouts configuration
apiVersion: argoproj.io/v1alpha1
kind: Rollout
metadata:
  name: api
spec:
  replicas: 10
  strategy:
    canary:
      steps:
        - setWeight: 10
        - pause: { duration: 5m }
        - setWeight: 30
        - pause: { duration: 5m }
        - setWeight: 60
        - pause: { duration: 5m }
        - setWeight: 100
      canaryMetadata:
        labels:
          deployment: canary
      stableMetadata:
        labels:
          deployment: stable
      analysis:
        templates:
          - templateName: success-rate
        startingStep: 1
```

### Strategy 4: Blue-Green Deployment

```yaml
# Blue-green with Kubernetes
apiVersion: v1
kind: Service
metadata:
  name: api
spec:
  selector:
    app: api
    version: green  # Switch between blue/green
  ports:
    - port: 80
      targetPort: 8080
```

## Rollback Strategies

### Automatic Rollback on Failure

```yaml
# GitHub Actions rollback
- name: Deploy with rollback
  run: |
    kubectl apply -f k8s/production/

    if ! kubectl rollout status deployment/api -n production --timeout=300s; then
      echo "Deployment failed, rolling back..."
      kubectl rollout undo deployment/api -n production
      exit 1
    fi
```

### Manual Rollback Command

```yaml
# Add to workflow
rollback:
  runs-on: ubuntu-latest
  if: github.event_name == 'workflow_dispatch'
  steps:
    - name: Rollback to previous version
      run: |
        kubectl rollout undo deployment/api -n ${{ github.event.inputs.environment }}
        kubectl rollout undo deployment/web -n ${{ github.event.inputs.environment }}
```

### Database Migration Rollback

```yaml
- name: Deploy with migration rollback support
  run: |
    # Take snapshot before migration
    pg_dump $DATABASE_URL > backup-$(date +%Y%m%d%H%M%S).sql

    # Run migrations
    if ! npm run db:migrate; then
      echo "Migration failed, restoring backup..."
      psql $DATABASE_URL < backup-*.sql
      exit 1
    fi
```

## Approval Gates Between Phases

### Phase-Aligned Approvals

```yaml
# Map framework phases to deployment stages
phase-5-review:
  # After agents complete their work
  stage: review
  environment:
    name: phase-5-review
  script:
    - echo "Agent work complete, ready for integration"
  when: manual

phase-6-integration:
  # After integration testing
  stage: integration-approval
  environment:
    name: phase-6-approval
  script:
    - echo "Integration complete, ready for staging"
  when: manual
  needs:
    - integration-tests
```

### Slack/Teams Approval Notifications

```yaml
- name: Request approval
  uses: slackapi/slack-github-action@v1
  with:
    channel-id: 'deployments'
    payload: |
      {
        "blocks": [
          {
            "type": "section",
            "text": {
              "type": "mrkdwn",
              "text": "*Production Deployment Request*\n\nVersion: ${{ needs.build.outputs.version }}\nChanges: ${{ github.event.head_commit.message }}\n\n<${{ github.server_url }}/${{ github.repository }}/actions/runs/${{ github.run_id }}|Approve in GitHub>"
            }
          }
        ]
      }
```

## Monitoring and Observability

### Deployment Metrics

```yaml
- name: Record deployment metrics
  run: |
    curl -X POST $DATADOG_API_URL/api/v1/events \
      -H "DD-API-KEY: ${{ secrets.DATADOG_API_KEY }}" \
      -d '{
        "title": "Deployment to production",
        "text": "Version ${{ needs.build.outputs.version }} deployed",
        "tags": ["environment:production", "version:${{ needs.build.outputs.version }}"],
        "alert_type": "success"
      }'
```

### Health Check Validation

```yaml
- name: Validate deployment health
  run: |
    for i in {1..10}; do
      status=$(curl -s -o /dev/null -w "%{http_code}" https://api.example.com/health)
      if [ "$status" == "200" ]; then
        echo "Health check passed"
        exit 0
      fi
      echo "Attempt $i: Health check returned $status, retrying..."
      sleep 30
    done
    echo "Health check failed after 10 attempts"
    exit 1
```

## Framework Phase Integration

| Framework Phase | Pipeline Stage | Approval Required |
|-----------------|----------------|-------------------|
| Phase 4: Agent Assignment | Branch creation | No |
| Phase 5: Execution | PR creation per agent | Code review |
| Phase 5: Execution | CI tests | Automatic |
| Phase 6: Integration | Integration tests | Automatic |
| Phase 6: Integration | E2E tests | Automatic |
| Phase 6: Integration | Staging deploy | No |
| Phase 6: Integration | Production deploy | Manual |

## Pipeline Design Checklist

Before finalizing deployment pipeline:

- [ ] Quality gates defined (lint, type check, security)
- [ ] Test stages cover unit, integration, E2E
- [ ] Build artifacts stored and versioned
- [ ] Container images tagged and pushed
- [ ] Staging environment configured
- [ ] Production approval gates defined
- [ ] Rollback procedures documented
- [ ] Health checks implemented
- [ ] Monitoring and alerting configured
- [ ] Secrets properly managed
- [ ] Branch protection rules set
- [ ] Environment variables documented
