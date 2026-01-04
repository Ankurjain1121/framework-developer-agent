---
description: Use this skill to generate OpenAPI 3.1 specifications from API contracts during Phase 3. Provides templates, validation guidance, and SDK generation tips.
---

# OpenAPI Specification Generation

This skill guides the creation of OpenAPI 3.1 specifications from the API contracts defined in Phase 3, enabling SDK generation and interactive documentation.

---

## OpenAPI 3.1 Template

```yaml
openapi: 3.1.0
info:
  title: [Project Name] API
  description: |
    API for [Project Name].

    ## Authentication
    All endpoints require Bearer token authentication unless marked as public.

    ## Rate Limits
    - Authenticated: 1000 requests/minute
    - Unauthenticated: 100 requests/minute
  version: 1.0.0
  contact:
    name: API Support
    email: api@example.com
  license:
    name: MIT
    url: https://opensource.org/licenses/MIT

servers:
  - url: https://api.example.com/v1
    description: Production
  - url: https://staging-api.example.com/v1
    description: Staging
  - url: http://localhost:3000/api/v1
    description: Development

tags:
  - name: Auth
    description: Authentication endpoints
  - name: Users
    description: User management

paths:
  /auth/login:
    post:
      tags: [Auth]
      summary: User login
      description: Authenticate user and return access token
      operationId: login
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/LoginRequest'
      responses:
        '200':
          description: Successful login
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/LoginResponse'
        '401':
          $ref: '#/components/responses/Unauthorized'
        '422':
          $ref: '#/components/responses/ValidationError'

  /users:
    get:
      tags: [Users]
      summary: List users
      description: Get paginated list of users
      operationId: listUsers
      security:
        - bearerAuth: []
      parameters:
        - $ref: '#/components/parameters/PageParam'
        - $ref: '#/components/parameters/LimitParam'
        - name: sort
          in: query
          schema:
            type: string
            enum: [createdAt, name, email]
            default: createdAt
      responses:
        '200':
          description: List of users
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/UserListResponse'
        '401':
          $ref: '#/components/responses/Unauthorized'

    post:
      tags: [Users]
      summary: Create user
      description: Create a new user account
      operationId: createUser
      security:
        - bearerAuth: []
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/CreateUserRequest'
      responses:
        '201':  # NOTE: 201 for creation, not 200
          description: User created
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/UserResponse'
        '401':
          $ref: '#/components/responses/Unauthorized'
        '409':
          $ref: '#/components/responses/Conflict'
        '422':
          $ref: '#/components/responses/ValidationError'

  /users/{id}:
    parameters:
      - name: id
        in: path
        required: true
        schema:
          type: string
          format: uuid
    get:
      tags: [Users]
      summary: Get user
      operationId: getUser
      security:
        - bearerAuth: []
      responses:
        '200':
          description: User details
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/UserResponse'
        '404':
          $ref: '#/components/responses/NotFound'

components:
  securitySchemes:
    bearerAuth:
      type: http
      scheme: bearer
      bearerFormat: JWT

  parameters:
    PageParam:
      name: page
      in: query
      schema:
        type: integer
        minimum: 1
        default: 1
    LimitParam:
      name: limit
      in: query
      schema:
        type: integer
        minimum: 1
        maximum: 100
        default: 20

  schemas:
    # Request Schemas
    LoginRequest:
      type: object
      required: [email, password]
      properties:
        email:
          type: string
          format: email
        password:
          type: string
          minLength: 8

    CreateUserRequest:
      type: object
      required: [email, name, password]
      properties:
        email:
          type: string
          format: email
          maxLength: 255
        name:
          type: string
          minLength: 2
          maxLength: 100
        password:
          type: string
          minLength: 8
        role:
          type: string
          enum: [user, admin]
          default: user

    # Response Schemas
    LoginResponse:
      type: object
      properties:
        data:
          type: object
          properties:
            token:
              type: string
            expiresAt:
              type: string
              format: date-time
            user:
              $ref: '#/components/schemas/User'

    UserResponse:
      type: object
      properties:
        data:
          $ref: '#/components/schemas/User'

    UserListResponse:
      type: object
      properties:
        data:
          type: array
          items:
            $ref: '#/components/schemas/User'
        meta:
          $ref: '#/components/schemas/PaginationMeta'

    # Core Schemas
    User:
      type: object
      properties:
        id:
          type: string
          format: uuid
        email:
          type: string
          format: email
        name:
          type: string
        role:
          type: string
          enum: [user, admin]
        createdAt:
          type: string
          format: date-time
        updatedAt:
          type: string
          format: date-time

    PaginationMeta:
      type: object
      properties:
        page:
          type: integer
        limit:
          type: integer
        total:
          type: integer
        totalPages:
          type: integer

    Error:
      type: object
      properties:
        error:
          type: object
          properties:
            code:
              type: string
            message:
              type: string
            details:
              type: array
              items:
                type: object
                properties:
                  field:
                    type: string
                  message:
                    type: string
            requestId:
              type: string
              format: uuid

  responses:
    Unauthorized:
      description: Authentication required
      content:
        application/json:
          schema:
            $ref: '#/components/schemas/Error'
          example:
            error:
              code: UNAUTHORIZED
              message: Authentication required

    NotFound:
      description: Resource not found
      content:
        application/json:
          schema:
            $ref: '#/components/schemas/Error'

    Conflict:
      description: Resource conflict
      content:
        application/json:
          schema:
            $ref: '#/components/schemas/Error'

    ValidationError:
      description: Validation failed
      content:
        application/json:
          schema:
            $ref: '#/components/schemas/Error'
          example:
            error:
              code: VALIDATION_ERROR
              message: Invalid request
              details:
                - field: email
                  message: Must be a valid email address
```

---

## Converting api-contracts.md to OpenAPI

### Step 1: Extract Endpoints

From `api-contracts.md`:
```markdown
### GET /users
**Response 200:**
```json
{ "data": [...], "meta": {...} }
```
```

To OpenAPI:
```yaml
paths:
  /users:
    get:
      responses:
        '200':
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/UserListResponse'
```

### Step 2: Define Schemas

From response examples, create component schemas.

### Step 3: Add Security

```yaml
security:
  - bearerAuth: []

components:
  securitySchemes:
    bearerAuth:
      type: http
      scheme: bearer
```

---

## Validation

### Using Spectral

```bash
npm install -g @stoplight/spectral-cli
spectral lint openapi.yaml
```

### Common Issues

| Issue | Fix |
|-------|-----|
| Missing operationId | Add unique operationId to each operation |
| No examples | Add example values to schemas |
| Missing descriptions | Document all endpoints and schemas |

---

## SDK Generation

### Using OpenAPI Generator

```bash
# TypeScript SDK
openapi-generator generate \
  -i openapi.yaml \
  -g typescript-fetch \
  -o ./sdk/typescript

# Python SDK
openapi-generator generate \
  -i openapi.yaml \
  -g python \
  -o ./sdk/python
```

### Using Orval (TypeScript)

```bash
npm install orval
orval --config orval.config.ts
```

---

## Integration with Framework Developer

During Phase 3:
1. Define API contracts in markdown
2. Convert to OpenAPI spec
3. Validate spec
4. Generate SDKs if needed
5. Store in `03-api-planning/openapi.yaml`
