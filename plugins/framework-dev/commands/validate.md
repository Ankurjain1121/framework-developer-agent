---
description: Validate blueprints against implementation. Checks API contracts, file links, and state file integrity.
argument-hint: "[contracts|links|state|all]"
allowed-tools: Read, Glob, Grep, Bash
---

# Validate Command

This command validates framework blueprints against the actual implementation, detecting mismatches, broken links, and schema violations.

## Usage

```
/validate contracts   # Check endpoints match api-contracts.md
/validate links       # Find broken file references
/validate state       # Validate state file against schema
/validate all         # Run all validations
```

## Mode: $ARGUMENTS

---

## Validation: Contracts

Check all implemented endpoints match `api-contracts.md`:

### Step 1: Read API Contracts
```
Read: .framework-blueprints/03-api-planning/api-contracts.md
```

Extract all endpoints defined in the contract.

### Step 2: Find Implemented Endpoints

Search for route definitions in source:
```bash
grep -r "router\.\|app\.\|@Get\|@Post\|@Put\|@Delete" src/
```

### Step 3: Compare

For each endpoint in contract:
- [ ] Endpoint exists in code
- [ ] HTTP method matches
- [ ] Path matches exactly
- [ ] Response status codes match (especially 201 vs 200)

### Step 4: Report

```markdown
## Contract Validation Report

**Status:** PASS | FAIL

### Matched Endpoints
| Endpoint | Method | Status |
|----------|--------|--------|
| /api/users | GET | ✅ Match |
| /api/users | POST | ✅ Match (201) |

### Mismatches
| Endpoint | Issue | Expected | Actual |
|----------|-------|----------|--------|
| /api/users/:id | Method | GET | Not found |

### Missing from Contract
- POST /api/internal/sync (in code but not contract)

### Recommendations
1. Add missing endpoints to contract
2. Implement missing handlers
```

---

## Validation: Links

Find broken file references in blueprints:

### Step 1: Extract File References

Find all file paths mentioned in blueprints:
```bash
grep -r "src/\|\.ts\|\.js\|\.md" .framework-blueprints/
```

### Step 2: Verify Each Path

For each referenced path:
```bash
ls [path] 2>/dev/null || echo "BROKEN: [path]"
```

### Step 3: Check Import Statements

Find imports that reference moved/deleted files:
```bash
grep -r "from ['\"].*['\"]" src/ | while read line; do
  # Extract import path and verify it exists
done
```

### Step 4: Report

```markdown
## Link Validation Report

**Status:** PASS | FAIL

### Broken Links in Blueprints
| File | Line | Broken Reference |
|------|------|------------------|
| outline-v1.md | 45 | src/auth/middleware.ts |

### Broken Imports in Source
| File | Import | Issue |
|------|--------|-------|
| api.ts | ./services/old | File not found |

### Recommendations
1. Update outline-v1.md line 45: src/auth/ → src/core/auth/
2. Fix import in api.ts
```

---

## Validation: State

Validate `00-project-state.json` against schema:

### Step 1: Read State File
```
Read: .framework-blueprints/00-project-state.json
```

### Step 2: Read Schema
```
Read: skills/project-state-management/references/state-schema.json
```

### Step 3: Validate

Check:
- [ ] All required fields present
- [ ] Field types correct
- [ ] Phase numbers consistent
- [ ] Decisions have sources
- [ ] IDs follow naming patterns

### Step 4: Report

```markdown
## State Validation Report

**Status:** PASS | FAIL

### Schema Errors
| Field | Issue |
|-------|-------|
| decisions[2].source | Missing required field |

### Consistency Issues
| Issue | Details |
|-------|---------|
| Phase mismatch | currentPhase=4 but phase 3 not completed |

### Recommendations
1. Add source URL to decision D003
2. Update phase 3 status to 'completed'
```

---

## Validation: All

Run all validations and generate summary:

```markdown
## Full Validation Report

**Generated:** [timestamp]
**Overall Status:** PASS | PARTIAL | FAIL

### Summary
| Validation | Status | Issues |
|------------|--------|--------|
| Contracts | ✅ PASS | 0 |
| Links | ⚠️ PARTIAL | 2 |
| State | ✅ PASS | 0 |

### Critical Issues (Must Fix)
1. [Issue requiring immediate attention]

### Warnings (Should Fix)
1. [Non-critical issue]

### Next Steps
1. Fix broken links in outline-v1.md
2. Run /validate again after fixes
```

---

## Auto-Fix Suggestions

When issues are found, suggest fixes:

```markdown
### Auto-Fix Available

The following issues can be automatically fixed:

1. **Update path reference**
   - File: outline-v1.md
   - Old: src/auth/middleware.ts
   - New: src/core/auth/middleware.ts
   - Command: `sed -i 's|src/auth/|src/core/auth/|g' outline-v1.md`

Apply fixes? [y/n]
```

---

## Integration with Workflow

Run `/validate` when:
- Before phase transitions
- After receiving handoffs
- Before creating PRs
- When something "feels off"

Validation is also triggered automatically by:
- PostTask hook (partial)
- PhaseTransition hook (full)
