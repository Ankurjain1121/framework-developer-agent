# API Contracts Template

> **Usage:** Copy this template to `.framework-blueprints/03-api-planning/api-contracts.md`
> and document all API endpoints for the project.

---

## API Overview

### Base URL
```
<!-- e.g., https://api.example.com/v1 -->
```

### Authentication
<!-- Describe authentication method -->
- Type: <!-- Bearer Token / API Key / OAuth2 / etc. -->
- Header: `Authorization: Bearer <token>`

### Common Headers

| Header | Required | Description |
|--------|----------|-------------|
| `Content-Type` | Yes | `application/json` |
| `Authorization` | Yes | Bearer token |
| `X-Request-ID` | No | Request tracking ID |

### Rate Limits
<!-- Document rate limiting if applicable -->
- Requests per minute: <!-- number -->
- Requests per hour: <!-- number -->

---

## Error Response Format

All errors follow this structure:

```json
{
  "error": {
    "code": "ERROR_CODE",
    "message": "Human-readable message",
    "details": {},
    "requestId": "uuid"
  }
}
```

### Common Error Codes

| HTTP Status | Code | Meaning |
|-------------|------|---------|
| 400 | `BAD_REQUEST` | Invalid request body |
| 401 | `UNAUTHORIZED` | Missing or invalid auth |
| 403 | `FORBIDDEN` | Insufficient permissions |
| 404 | `NOT_FOUND` | Resource not found |
| 422 | `VALIDATION_ERROR` | Request validation failed |
| 429 | `RATE_LIMITED` | Too many requests |
| 500 | `INTERNAL_ERROR` | Server error |

---

## Module: <!-- Module Name -->

### Endpoint: `POST /resource`

**Description:**
<!-- What does this endpoint do? -->

**Authentication:** Required

**Request Body:**

```json
{
  "field1": "string",
  "field2": 123,
  "nested": {
    "subField": "value"
  }
}
```

**Request Schema:**

| Field | Type | Required | Description | Constraints |
|-------|------|----------|-------------|-------------|
| `field1` | string | Yes | <!-- Description --> | max 255 chars |
| `field2` | integer | No | <!-- Description --> | min: 0 |
| `nested.subField` | string | Yes | <!-- Description --> | enum: ["a", "b"] |

**Response (201 Created):**

```json
{
  "id": "uuid",
  "field1": "string",
  "field2": 123,
  "createdAt": "2025-01-15T10:30:00Z",
  "updatedAt": "2025-01-15T10:30:00Z"
}
```

**Response Schema:**

| Field | Type | Description |
|-------|------|-------------|
| `id` | string (uuid) | Unique identifier |
| `createdAt` | string (ISO 8601) | Creation timestamp |
| `updatedAt` | string (ISO 8601) | Last update timestamp |

**Error Responses:**

| Status | Code | When |
|--------|------|------|
| 400 | `BAD_REQUEST` | Invalid JSON body |
| 401 | `UNAUTHORIZED` | No auth token |
| 422 | `VALIDATION_ERROR` | Field validation failed |

**Example Request:**

```bash
curl -X POST https://api.example.com/v1/resource \
  -H "Authorization: Bearer token" \
  -H "Content-Type: application/json" \
  -d '{"field1": "value", "field2": 123}'
```

---

### Endpoint: `GET /resource/:id`

**Description:**
<!-- What does this endpoint do? -->

**Authentication:** Required

**Path Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `id` | string (uuid) | Resource identifier |

**Query Parameters:**

| Parameter | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| `include` | string | No | - | Comma-separated relations to include |

**Response (200 OK):**

```json
{
  "id": "uuid",
  "field1": "string",
  "createdAt": "2025-01-15T10:30:00Z"
}
```

**Error Responses:**

| Status | Code | When |
|--------|------|------|
| 404 | `NOT_FOUND` | Resource doesn't exist |

---

### Endpoint: `GET /resources`

**Description:**
<!-- List resources with pagination -->

**Authentication:** Required

**Query Parameters:**

| Parameter | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| `page` | integer | No | 1 | Page number |
| `limit` | integer | No | 20 | Items per page (max 100) |
| `sort` | string | No | `createdAt` | Sort field |
| `order` | string | No | `desc` | Sort order: `asc` or `desc` |
| `filter[field]` | string | No | - | Filter by field value |

**Response (200 OK):**

```json
{
  "data": [
    {
      "id": "uuid",
      "field1": "string"
    }
  ],
  "pagination": {
    "page": 1,
    "limit": 20,
    "total": 100,
    "totalPages": 5,
    "hasNext": true,
    "hasPrev": false
  }
}
```

---

### Endpoint: `PUT /resource/:id`

**Description:**
<!-- Full update of resource -->

**Authentication:** Required

**Path Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `id` | string (uuid) | Resource identifier |

**Request Body:**

```json
{
  "field1": "updated string",
  "field2": 456
}
```

**Response (200 OK):**

```json
{
  "id": "uuid",
  "field1": "updated string",
  "field2": 456,
  "updatedAt": "2025-01-15T11:00:00Z"
}
```

---

### Endpoint: `PATCH /resource/:id`

**Description:**
<!-- Partial update of resource -->

**Authentication:** Required

**Request Body:**
<!-- Only include fields to update -->

```json
{
  "field1": "partial update"
}
```

---

### Endpoint: `DELETE /resource/:id`

**Description:**
<!-- Delete resource -->

**Authentication:** Required

**Response (204 No Content):**
<!-- Empty response body -->

**Error Responses:**

| Status | Code | When |
|--------|------|------|
| 404 | `NOT_FOUND` | Resource doesn't exist |
| 409 | `CONFLICT` | Resource has dependencies |

---

## Webhooks

### Webhook: `resource.created`

**Payload:**

```json
{
  "event": "resource.created",
  "timestamp": "2025-01-15T10:30:00Z",
  "data": {
    "id": "uuid",
    "field1": "string"
  }
}
```

---

## Data Types Reference

### Common Types

| Type | Format | Example |
|------|--------|---------|
| UUID | v4 format | `550e8400-e29b-41d4-a716-446655440000` |
| Timestamp | ISO 8601 | `2025-01-15T10:30:00Z` |
| Date | ISO 8601 | `2025-01-15` |
| Money | cents integer | `1999` (= $19.99) |

### Enums

#### Status Enum
```
pending | active | completed | cancelled
```

---

## API Versioning

- Current version: `v1`
- Version in URL path: `/v1/resource`
- Deprecation notice: Header `X-API-Deprecated: true`

---

## Changelog

| Version | Date | Changes |
|---------|------|---------|
| v1.0 | <!-- Date --> | Initial API design |
