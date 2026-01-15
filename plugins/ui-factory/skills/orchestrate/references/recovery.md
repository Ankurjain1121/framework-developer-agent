# Error Recovery Patterns

## Decision Tree

```
Error occurred
├── Type error?
│   └── Contract incomplete → Update contract → Restart affected agents
├── Lint error?
│   └── Spawn /fix skill → Continue
├── Test failure?
│   └── Identify broken contract → Fix → Retry agent
├── Agent timeout?
│   └── Retry once → Escalate if fails again
└── Agent failed (other)?
    └── Was using Haiku?
        ├── Yes → Retry with Sonnet
        └── No → Escalate to user
```

## Error Types & Actions

| Error | Cause | Action |
|-------|-------|--------|
| Type mismatch | Contract incomplete | Update contract, restart consumers |
| Lint failure | Agent missed rule | Run /fix, continue |
| Test failure | Logic error or bad contract | Review contract, retry agent |
| Agent timeout | Task too complex | Split task or use Sonnet |
| Agent confused | Prompt unclear | Clarify prompt, retry |

## Retry Pattern

```
Attempt 1: Original agent + model
├── Success → Done
└── Failure → Attempt 2

Attempt 2: Same agent, add error context
├── Success → Done
└── Failure → Attempt 3

Attempt 3: Escalate model (Haiku → Sonnet)
├── Success → Done
└── Failure → Escalate to user
```

## Contract Change Mid-Execution

If contract needs change after parallel work started:

1. **STOP** all running agents
2. **Assess impact** - who consumes this contract?
3. **Update contract** in contract files
4. **Restart affected agents** with new contract context
5. **Do NOT restart** unaffected agents

## Common Fixes

### Type Error After Parallel Work

```
Symptom: "Property 'x' does not exist on type 'Y'"
Cause: Contract didn't include all needed properties
Fix:
1. Add missing property to contract
2. Restart agent that produced the type
3. Restart agents that consume the type
```

### Agent Produced Wrong Output

```
Symptom: Agent edited wrong file or wrong function
Cause: Prompt was ambiguous
Fix:
1. Clarify file ownership in prompt
2. Explicitly list what NOT to modify
3. Retry with clearer prompt
```

### Haiku Agent Failed on Complex Task

```
Symptom: Output is incomplete or nonsensical
Cause: Task exceeded Haiku's capability
Fix:
1. Retry with model: "sonnet"
2. If still fails, split task into smaller pieces
```

## Escalation Thresholds

| Condition | Action |
|-----------|--------|
| 2 retries failed | Escalate to user |
| Contract changed 3+ times | Pause, reassess scope |
| >50% agents failed | Architecture issue, stop |
