# Project State Management Skill

This skill teaches the Framework Developer Orchestrator how to track and persist project state across sessions, ensuring continuity and enabling seamless resumption of work.

## Overview

The state management system maintains a single source of truth for:
- Current workflow phase (1-6)
- All decisions made (with sources)
- Module definitions and dependencies
- Agent assignments and status
- Handoff tracking between agents
- Risk registry

**State File Location:** `.framework-dev/00-project-state.json`

---

## 1. State File Initialization

### When to Initialize

Initialize a new state file when:
1. User starts a new project with `/framework-dev`
2. No existing state file is found in the project directory
3. User explicitly requests a fresh start

### Initialization Process

```typescript
// Pseudo-code for initialization
async function initializeState(projectName: string): Promise<ProjectState> {
  const statePath = '.framework-dev/00-project-state.json';

  // Check if state already exists
  if (await fileExists(statePath)) {
    const proceed = await askUser(
      'Existing project state found. Options:\n' +
      '1. Resume existing project\n' +
      '2. Archive and start fresh\n' +
      'Which do you prefer?'
    );

    if (proceed === 'resume') {
      return loadState(statePath);
    } else {
      await archiveState(statePath);
    }
  }

  // Create new state
  const newState: ProjectState = {
    projectName: projectName,
    version: '1.0.0',
    createdAt: new Date().toISOString(),
    updatedAt: new Date().toISOString(),
    currentPhase: 1,
    phases: {
      '1': { status: 'in_progress', startedAt: new Date().toISOString() },
      '2': { status: 'pending' },
      '3': { status: 'pending' },
      '4': { status: 'pending' },
      '5': { status: 'pending' },
      '6': { status: 'pending' }
    },
    decisions: [],
    modules: [],
    agents: [],
    handoffs: [],
    risks: []
  };

  await writeState(statePath, newState);
  return newState;
}
```

### Initial State Template

```json
{
  "projectName": "",
  "version": "1.0.0",
  "createdAt": "",
  "updatedAt": "",
  "currentPhase": 1,
  "phases": {
    "1": { "status": "in_progress", "startedAt": "" },
    "2": { "status": "pending" },
    "3": { "status": "pending" },
    "4": { "status": "pending" },
    "5": { "status": "pending" },
    "6": { "status": "pending" }
  },
  "decisions": [],
  "modules": [],
  "agents": [],
  "handoffs": [],
  "risks": []
}
```

---

## 2. State Update Operations

### Update Triggers

Update the state file after every significant action:

| Event | State Fields to Update |
|-------|------------------------|
| Phase transition | `currentPhase`, `phases[n].status`, `phases[n].completedAt` or `startedAt`, `updatedAt` |
| Decision made | `decisions[]`, `updatedAt` |
| Module defined | `modules[]`, `updatedAt` |
| Agent assigned | `agents[]`, `updatedAt` |
| Handoff initiated | `handoffs[]`, `updatedAt` |
| Risk identified | `risks[]`, `updatedAt` |

### Update Functions

#### Phase Transition

```typescript
async function transitionPhase(
  state: ProjectState,
  fromPhase: number,
  toPhase: number
): Promise<ProjectState> {
  const now = new Date().toISOString();

  // Mark previous phase complete
  state.phases[String(fromPhase)] = {
    ...state.phases[String(fromPhase)],
    status: 'completed',
    completedAt: now
  };

  // Start new phase
  state.phases[String(toPhase)] = {
    ...state.phases[String(toPhase)],
    status: 'in_progress',
    startedAt: now
  };

  state.currentPhase = toPhase;
  state.updatedAt = now;

  await saveState(state);
  return state;
}
```

#### Record Decision

```typescript
async function recordDecision(
  state: ProjectState,
  topic: string,
  choice: string,
  source: string,
  reasoning?: string
): Promise<ProjectState> {
  const decision: Decision = {
    id: `D${String(state.decisions.length + 1).padStart(3, '0')}`,
    topic: topic,
    choice: choice,
    source: source,
    reasoning: reasoning,
    date: new Date().toISOString()
  };

  state.decisions.push(decision);
  state.updatedAt = new Date().toISOString();

  await saveState(state);
  return state;
}
```

#### Define Module

```typescript
async function defineModule(
  state: ProjectState,
  name: string,
  description: string,
  dependencies: string[]
): Promise<ProjectState> {
  const module: Module = {
    name: name,
    description: description,
    status: 'defined',
    dependencies: dependencies,
    definedAt: new Date().toISOString()
  };

  state.modules.push(module);
  state.updatedAt = new Date().toISOString();

  await saveState(state);
  return state;
}
```

#### Assign Agent

```typescript
async function assignAgent(
  state: ProjectState,
  name: string,
  llm: string,
  tasks: string[],
  capabilities?: string[]
): Promise<ProjectState> {
  const agent: Agent = {
    name: name,
    llm: llm,
    tasks: tasks,
    capabilities: capabilities,
    status: 'assigned',
    assignedAt: new Date().toISOString()
  };

  state.agents.push(agent);
  state.updatedAt = new Date().toISOString();

  await saveState(state);
  return state;
}
```

#### Record Handoff

```typescript
async function recordHandoff(
  state: ProjectState,
  fromAgent: string,
  toAgent: string,
  artifact: string,
  notes?: string
): Promise<ProjectState> {
  const handoff: Handoff = {
    id: `HO-${String(state.handoffs.length + 1).padStart(3, '0')}`,
    from: fromAgent,
    to: toAgent,
    artifact: artifact,
    notes: notes,
    status: 'pending',
    initiatedAt: new Date().toISOString()
  };

  state.handoffs.push(handoff);
  state.updatedAt = new Date().toISOString();

  await saveState(state);
  return state;
}
```

#### Register Risk

```typescript
async function registerRisk(
  state: ProjectState,
  description: string,
  severity: 'low' | 'medium' | 'high' | 'critical',
  mitigation: string,
  owner?: string
): Promise<ProjectState> {
  const risk: Risk = {
    id: `R${String(state.risks.length + 1).padStart(3, '0')}`,
    description: description,
    severity: severity,
    mitigation: mitigation,
    owner: owner,
    status: 'open',
    identifiedAt: new Date().toISOString()
  };

  state.risks.push(risk);
  state.updatedAt = new Date().toISOString();

  await saveState(state);
  return state;
}
```

### Atomic Updates

Always ensure updates are atomic:

```typescript
async function saveState(state: ProjectState): Promise<void> {
  const statePath = '.framework-dev/00-project-state.json';
  const tempPath = '.framework-dev/00-project-state.tmp.json';

  // Write to temp file first
  await writeFile(tempPath, JSON.stringify(state, null, 2));

  // Validate the written file
  const written = await readFile(tempPath);
  const parsed = JSON.parse(written);
  if (!validateState(parsed)) {
    throw new Error('State validation failed after write');
  }

  // Atomic rename
  await rename(tempPath, statePath);
}
```

---

## 3. Resuming from Saved State

### Resume Process

When the user returns to continue work:

```typescript
async function resumeProject(): Promise<ProjectState | null> {
  const statePath = '.framework-dev/00-project-state.json';

  if (!await fileExists(statePath)) {
    return null; // No state to resume
  }

  const state = await loadState(statePath);

  // Validate state integrity
  const validationResult = validateState(state);
  if (!validationResult.valid) {
    await handleCorruptState(state, validationResult.errors);
    return null;
  }

  // Display resume summary
  await displayResumeSummary(state);

  // Confirm with user
  const confirmed = await askUser(
    `Resume from Phase ${state.currentPhase}? (yes/no)`
  );

  if (confirmed === 'yes') {
    return state;
  }

  return null;
}
```

### Resume Summary Display

```typescript
async function displayResumeSummary(state: ProjectState): Promise<void> {
  const summary = `
## Project Resume: ${state.projectName}

**Last Updated:** ${formatDate(state.updatedAt)}
**Current Phase:** ${state.currentPhase} - ${getPhaseTitle(state.currentPhase)}

### Phase Progress
${Object.entries(state.phases).map(([num, phase]) =>
  `- Phase ${num}: ${phase.status}${phase.completedAt ? ` (completed ${formatDate(phase.completedAt)})` : ''}`
).join('\n')}

### Key Decisions (${state.decisions.length})
${state.decisions.slice(-3).map(d =>
  `- **${d.topic}**: ${d.choice} (Source: ${d.source})`
).join('\n')}

### Modules Defined (${state.modules.length})
${state.modules.map(m =>
  `- ${m.name} [${m.status}] - depends on: ${m.dependencies.join(', ') || 'none'}`
).join('\n')}

### Agents Assigned (${state.agents.length})
${state.agents.map(a =>
  `- ${a.name} (${a.llm}) - ${a.tasks.length} tasks [${a.status}]`
).join('\n')}

### Open Risks (${state.risks.filter(r => r.status === 'open').length})
${state.risks.filter(r => r.status === 'open').map(r =>
  `- [${r.severity.toUpperCase()}] ${r.description}`
).join('\n')}
`;

  console.log(summary);
}
```

### Phase Titles Reference

```typescript
function getPhaseTitle(phase: number): string {
  const titles: Record<number, string> = {
    1: 'Discovery & Broad Outlines',
    2: 'Sub-points & Relationships',
    3: 'Full Plan & API Design',
    4: 'Agent Assignment',
    5: 'Execution & Orchestration',
    6: 'Integration & Review'
  };
  return titles[phase] || 'Unknown';
}
```

---

## 4. State Validation Rules

### Validation Schema

Use JSON Schema for validation. See `references/state-schema.json` for the complete schema.

### Validation Function

```typescript
interface ValidationResult {
  valid: boolean;
  errors: string[];
  warnings: string[];
}

async function validateState(state: unknown): Promise<ValidationResult> {
  const errors: string[] = [];
  const warnings: string[] = [];

  // Required fields
  if (!state || typeof state !== 'object') {
    return { valid: false, errors: ['State must be an object'], warnings: [] };
  }

  const s = state as Record<string, unknown>;

  // Check required top-level fields
  const requiredFields = [
    'projectName', 'version', 'createdAt', 'updatedAt',
    'currentPhase', 'phases', 'decisions', 'modules', 'agents', 'handoffs', 'risks'
  ];

  for (const field of requiredFields) {
    if (!(field in s)) {
      errors.push(`Missing required field: ${field}`);
    }
  }

  // Validate currentPhase
  if (typeof s.currentPhase !== 'number' || s.currentPhase < 1 || s.currentPhase > 6) {
    errors.push('currentPhase must be a number between 1 and 6');
  }

  // Validate phases object
  if (typeof s.phases === 'object' && s.phases !== null) {
    const phases = s.phases as Record<string, unknown>;
    for (let i = 1; i <= 6; i++) {
      if (!(String(i) in phases)) {
        errors.push(`Missing phase ${i} in phases object`);
      }
    }

    // Check phase status consistency
    const currentPhase = s.currentPhase as number;
    for (let i = 1; i < currentPhase; i++) {
      const phase = phases[String(i)] as Record<string, unknown>;
      if (phase?.status !== 'completed') {
        warnings.push(`Phase ${i} should be 'completed' since current phase is ${currentPhase}`);
      }
    }
  }

  // Validate arrays
  const arrayFields = ['decisions', 'modules', 'agents', 'handoffs', 'risks'];
  for (const field of arrayFields) {
    if (!Array.isArray(s[field])) {
      errors.push(`${field} must be an array`);
    }
  }

  // Validate decisions have required fields
  if (Array.isArray(s.decisions)) {
    (s.decisions as unknown[]).forEach((d, i) => {
      const decision = d as Record<string, unknown>;
      if (!decision.id) errors.push(`Decision ${i} missing id`);
      if (!decision.topic) errors.push(`Decision ${i} missing topic`);
      if (!decision.choice) errors.push(`Decision ${i} missing choice`);
      if (!decision.source) warnings.push(`Decision ${i} missing source (should cite sources)`);
    });
  }

  // Validate module dependencies exist
  if (Array.isArray(s.modules)) {
    const moduleNames = (s.modules as Array<{ name: string }>).map(m => m.name);
    (s.modules as Array<{ name: string; dependencies: string[] }>).forEach((m, i) => {
      if (m.dependencies) {
        m.dependencies.forEach(dep => {
          if (!moduleNames.includes(dep)) {
            warnings.push(`Module ${m.name} depends on undefined module: ${dep}`);
          }
        });
      }
    });
  }

  // Validate agent task references
  // Validate handoff agent references exist
  if (Array.isArray(s.handoffs) && Array.isArray(s.agents)) {
    const agentNames = (s.agents as Array<{ name: string }>).map(a => a.name);
    (s.handoffs as Array<{ from: string; to: string }>).forEach((h, i) => {
      if (!agentNames.includes(h.from)) {
        warnings.push(`Handoff ${i} references unknown source agent: ${h.from}`);
      }
      if (!agentNames.includes(h.to)) {
        warnings.push(`Handoff ${i} references unknown target agent: ${h.to}`);
      }
    });
  }

  // Validate risk severity
  const validSeverities = ['low', 'medium', 'high', 'critical'];
  if (Array.isArray(s.risks)) {
    (s.risks as Array<{ severity: string }>).forEach((r, i) => {
      if (!validSeverities.includes(r.severity)) {
        errors.push(`Risk ${i} has invalid severity: ${r.severity}`);
      }
    });
  }

  // Validate timestamps
  const timestampFields = ['createdAt', 'updatedAt'];
  for (const field of timestampFields) {
    if (s[field] && isNaN(Date.parse(s[field] as string))) {
      errors.push(`${field} is not a valid ISO 8601 timestamp`);
    }
  }

  return {
    valid: errors.length === 0,
    errors,
    warnings
  };
}
```

### Handling Validation Failures

```typescript
async function handleCorruptState(
  state: unknown,
  errors: string[]
): Promise<void> {
  console.log(`
## State Validation Failed

The following errors were found in the state file:
${errors.map(e => `- ${e}`).join('\n')}

**Options:**
1. Attempt automatic repair (may lose some data)
2. Restore from backup
3. Start fresh (archive current state)
`);

  const choice = await askUser('Which option? (1/2/3)');

  switch (choice) {
    case '1':
      await attemptRepair(state);
      break;
    case '2':
      await restoreFromBackup();
      break;
    case '3':
      await archiveAndReset(state);
      break;
  }
}
```

---

## 5. State Summary Display

### Quick Status Command

Display current state at any time:

```typescript
async function showStateSummary(): Promise<void> {
  const state = await loadState('.framework-dev/00-project-state.json');

  console.log(`
+--------------------------------------------------+
|          PROJECT STATE: ${state.projectName.padEnd(24)}|
+--------------------------------------------------+
| Phase: ${state.currentPhase}/6 - ${getPhaseTitle(state.currentPhase).padEnd(35)}|
| Updated: ${formatDate(state.updatedAt).padEnd(38)}|
+--------------------------------------------------+

PHASE PROGRESS:
${'='.repeat(50)}
${generatePhaseProgressBar(state.phases)}

SUMMARY:
- Decisions Made: ${state.decisions.length}
- Modules Defined: ${state.modules.length}
- Agents Assigned: ${state.agents.length}
- Pending Handoffs: ${state.handoffs.filter(h => h.status === 'pending').length}
- Open Risks: ${state.risks.filter(r => r.status === 'open').length}
  - Critical: ${state.risks.filter(r => r.status === 'open' && r.severity === 'critical').length}
  - High: ${state.risks.filter(r => r.status === 'open' && r.severity === 'high').length}
`);
}

function generatePhaseProgressBar(phases: Record<string, Phase>): string {
  const icons = {
    completed: '[x]',
    in_progress: '[>]',
    pending: '[ ]'
  };

  return Object.entries(phases)
    .map(([num, phase]) => `${icons[phase.status]} Phase ${num}`)
    .join(' -> ');
}
```

### Detailed State Report

For comprehensive review:

```typescript
async function generateStateReport(): Promise<string> {
  const state = await loadState('.framework-dev/00-project-state.json');

  return `
# Project State Report: ${state.projectName}

**Generated:** ${new Date().toISOString()}
**Version:** ${state.version}
**Created:** ${state.createdAt}
**Last Updated:** ${state.updatedAt}

---

## Phase Progress

| Phase | Name | Status | Started | Completed |
|-------|------|--------|---------|-----------|
${Object.entries(state.phases).map(([num, phase]) =>
  `| ${num} | ${getPhaseTitle(parseInt(num))} | ${phase.status} | ${phase.startedAt || '-'} | ${phase.completedAt || '-'} |`
).join('\n')}

---

## Decisions Log

| ID | Topic | Choice | Source | Date |
|----|-------|--------|--------|------|
${state.decisions.map(d =>
  `| ${d.id} | ${d.topic} | ${d.choice} | [Link](${d.source}) | ${formatDate(d.date)} |`
).join('\n')}

---

## Module Architecture

\`\`\`mermaid
graph TD
${state.modules.map(m =>
  m.dependencies.map(dep => `  ${dep} --> ${m.name}`).join('\n')
).join('\n')}
\`\`\`

| Module | Status | Dependencies |
|--------|--------|--------------|
${state.modules.map(m =>
  `| ${m.name} | ${m.status} | ${m.dependencies.join(', ') || 'none'} |`
).join('\n')}

---

## Agent Assignments

| Agent | LLM | Tasks | Status |
|-------|-----|-------|--------|
${state.agents.map(a =>
  `| ${a.name} | ${a.llm} | ${a.tasks.join(', ')} | ${a.status} |`
).join('\n')}

---

## Handoff Log

| ID | From | To | Status | Initiated |
|----|------|----|--------|-----------|
${state.handoffs.map(h =>
  `| ${h.id} | ${h.from} | ${h.to} | ${h.status} | ${formatDate(h.initiatedAt)} |`
).join('\n')}

---

## Risk Register

| ID | Severity | Description | Mitigation | Status |
|----|----------|-------------|------------|--------|
${state.risks.map(r =>
  `| ${r.id} | ${r.severity.toUpperCase()} | ${r.description} | ${r.mitigation} | ${r.status} |`
).join('\n')}
`;
}
```

---

## 6. Backup and Version History

### Automatic Backups

Create backups at key moments:

```typescript
const BACKUP_TRIGGERS = [
  'phase_transition',
  'before_destructive_operation',
  'manual_request',
  'every_10_updates'
];

async function createBackup(
  state: ProjectState,
  trigger: string
): Promise<string> {
  const backupDir = '.framework-dev/backups';
  await ensureDir(backupDir);

  const timestamp = new Date().toISOString().replace(/[:.]/g, '-');
  const backupName = `state-${timestamp}-${trigger}.json`;
  const backupPath = `${backupDir}/${backupName}`;

  await writeFile(backupPath, JSON.stringify(state, null, 2));

  // Maintain only last 20 backups
  await pruneOldBackups(backupDir, 20);

  return backupPath;
}
```

### Backup Directory Structure

```
.framework-dev/
├── 00-project-state.json           # Current state
├── backups/
│   ├── state-2025-12-30T10-00-00-000Z-phase_transition.json
│   ├── state-2025-12-30T11-30-00-000Z-phase_transition.json
│   └── state-2025-12-30T12-00-00-000Z-manual_request.json
└── history/
    └── decisions.log               # Append-only decision log
```

### Version History Log

Maintain an append-only log for audit trail:

```typescript
async function appendToHistory(
  action: string,
  details: Record<string, unknown>
): Promise<void> {
  const historyPath = '.framework-dev/history/actions.log';
  await ensureDir('.framework-dev/history');

  const entry = {
    timestamp: new Date().toISOString(),
    action: action,
    details: details
  };

  await appendFile(historyPath, JSON.stringify(entry) + '\n');
}
```

### Restore from Backup

```typescript
async function restoreFromBackup(backupFile?: string): Promise<ProjectState> {
  const backupDir = '.framework-dev/backups';

  if (!backupFile) {
    // List available backups
    const backups = await listFiles(backupDir);
    backups.sort().reverse(); // Most recent first

    console.log('Available backups:');
    backups.forEach((b, i) => console.log(`${i + 1}. ${b}`));

    const choice = await askUser('Select backup number:');
    backupFile = backups[parseInt(choice) - 1];
  }

  const backupPath = `${backupDir}/${backupFile}`;
  const state = await loadState(backupPath);

  // Validate before restoring
  const validation = await validateState(state);
  if (!validation.valid) {
    throw new Error(`Backup is also corrupt: ${validation.errors.join(', ')}`);
  }

  // Create backup of current state before overwriting
  const currentState = await loadState('.framework-dev/00-project-state.json');
  await createBackup(currentState, 'before_restore');

  // Restore
  await saveState(state);

  console.log(`Restored from: ${backupFile}`);
  return state;
}
```

### Pruning Old Backups

```typescript
async function pruneOldBackups(
  backupDir: string,
  keepCount: number
): Promise<void> {
  const backups = await listFiles(backupDir);
  backups.sort(); // Oldest first

  const toDelete = backups.slice(0, Math.max(0, backups.length - keepCount));

  for (const backup of toDelete) {
    await deleteFile(`${backupDir}/${backup}`);
  }
}
```

---

## 7. Integration with Framework Developer Workflow

### Phase 1: Discovery

```typescript
// After user confirms project vision
await recordDecision(state, 'Project Vision', userVision, 'user-input');
await recordDecision(state, 'Target Users', targetUsers, 'user-input');

// After identifying modules
for (const module of identifiedModules) {
  await defineModule(state, module.name, module.description, []);
}

// Phase complete
await transitionPhase(state, 1, 2);
```

### Phase 2: Structure

```typescript
// After breaking down modules
for (const module of state.modules) {
  await updateModuleDependencies(state, module.name, dependencies);
}

// After risk assessment
for (const risk of identifiedRisks) {
  await registerRisk(state, risk.description, risk.severity, risk.mitigation);
}

// Phase complete
await transitionPhase(state, 2, 3);
```

### Phase 3: Planning

```typescript
// After defining APIs
await recordDecision(state, 'API Style', 'REST', 'https://restfulapi.net/');
await recordDecision(state, 'Auth Method', 'JWT', 'https://jwt.io/introduction');

// Phase complete
await transitionPhase(state, 3, 4);
```

### Phase 4: Agent Assignment

```typescript
// After researching and assigning agents
await assignAgent(state, 'Claude-Backend', 'claude-3-opus', ['T001', 'T002']);
await assignAgent(state, 'GPT-Frontend', 'gpt-4', ['T003', 'T004']);

// Phase complete
await transitionPhase(state, 4, 5);
```

### Phase 5: Execution

```typescript
// Update agent status as work progresses
await updateAgentStatus(state, 'Claude-Backend', 'active');

// Record handoffs
await recordHandoff(state, 'Claude-Backend', 'GPT-Frontend', 'API specs', 'Ready for integration');
await updateHandoffStatus(state, 'HO-001', 'completed');

// Phase complete when all agents done
await transitionPhase(state, 5, 6);
```

### Phase 6: Integration

```typescript
// Record integration decisions
await recordDecision(state, 'Integration Approach', 'Continuous Integration', 'team-discussion');

// Mark risks as resolved
await updateRiskStatus(state, 'R001', 'resolved');

// Final phase complete
await completeProject(state);
```

---

## 8. State File Reference

### Complete Example State

```json
{
  "projectName": "TaskMaster Pro",
  "version": "1.0.0",
  "createdAt": "2025-12-30T10:00:00.000Z",
  "updatedAt": "2025-12-30T15:30:00.000Z",
  "currentPhase": 4,
  "phases": {
    "1": {
      "status": "completed",
      "startedAt": "2025-12-30T10:00:00.000Z",
      "completedAt": "2025-12-30T11:00:00.000Z"
    },
    "2": {
      "status": "completed",
      "startedAt": "2025-12-30T11:00:00.000Z",
      "completedAt": "2025-12-30T12:30:00.000Z"
    },
    "3": {
      "status": "completed",
      "startedAt": "2025-12-30T12:30:00.000Z",
      "completedAt": "2025-12-30T14:00:00.000Z"
    },
    "4": {
      "status": "in_progress",
      "startedAt": "2025-12-30T14:00:00.000Z"
    },
    "5": { "status": "pending" },
    "6": { "status": "pending" }
  },
  "decisions": [
    {
      "id": "D001",
      "topic": "Database",
      "choice": "PostgreSQL",
      "source": "https://www.postgresql.org/docs/current/",
      "reasoning": "ACID compliance needed for financial transactions",
      "date": "2025-12-30T11:30:00.000Z"
    },
    {
      "id": "D002",
      "topic": "Backend Framework",
      "choice": "FastAPI",
      "source": "https://fastapi.tiangolo.com/",
      "reasoning": "Async support, automatic OpenAPI docs",
      "date": "2025-12-30T12:00:00.000Z"
    },
    {
      "id": "D003",
      "topic": "Frontend Framework",
      "choice": "Next.js 14",
      "source": "https://nextjs.org/docs",
      "reasoning": "Server components, app router, good DX",
      "date": "2025-12-30T12:15:00.000Z"
    }
  ],
  "modules": [
    {
      "name": "core",
      "description": "Core utilities and shared types",
      "status": "defined",
      "dependencies": [],
      "definedAt": "2025-12-30T11:15:00.000Z"
    },
    {
      "name": "auth",
      "description": "Authentication and authorization",
      "status": "defined",
      "dependencies": ["core"],
      "definedAt": "2025-12-30T11:20:00.000Z"
    },
    {
      "name": "tasks",
      "description": "Task management domain",
      "status": "defined",
      "dependencies": ["core", "auth"],
      "definedAt": "2025-12-30T11:25:00.000Z"
    },
    {
      "name": "notifications",
      "description": "Real-time notifications",
      "status": "defined",
      "dependencies": ["core", "auth"],
      "definedAt": "2025-12-30T11:30:00.000Z"
    }
  ],
  "agents": [
    {
      "name": "Claude-Backend",
      "llm": "claude-3-opus",
      "tasks": ["T001", "T002", "T003"],
      "capabilities": ["complex reasoning", "API design", "database schema"],
      "status": "assigned",
      "assignedAt": "2025-12-30T14:30:00.000Z"
    },
    {
      "name": "Gemini-Frontend",
      "llm": "gemini-pro",
      "tasks": ["T004", "T005"],
      "capabilities": ["UI components", "responsive design"],
      "status": "assigned",
      "assignedAt": "2025-12-30T14:35:00.000Z"
    }
  ],
  "handoffs": [],
  "risks": [
    {
      "id": "R001",
      "description": "Offline sync conflicts may be complex",
      "severity": "high",
      "mitigation": "Use CRDT for conflict resolution",
      "owner": "Claude-Backend",
      "status": "open",
      "identifiedAt": "2025-12-30T12:00:00.000Z"
    },
    {
      "id": "R002",
      "description": "Real-time notifications may have latency",
      "severity": "medium",
      "mitigation": "Implement WebSocket with fallback to polling",
      "status": "open",
      "identifiedAt": "2025-12-30T12:10:00.000Z"
    }
  ]
}
```

---

## 9. Error Handling

### Common Errors and Recovery

| Error | Cause | Recovery |
|-------|-------|----------|
| `ENOENT` | State file missing | Initialize new state |
| `JSON parse error` | Corrupted file | Restore from backup |
| `Validation failed` | Invalid data | Attempt repair or restore |
| `Phase mismatch` | Inconsistent state | Recalculate phase from history |
| `Missing dependencies` | Module references broken | Rebuild dependency graph |

### Recovery Procedures

```typescript
async function recoverState(): Promise<ProjectState> {
  try {
    const state = await loadState('.framework-dev/00-project-state.json');
    const validation = await validateState(state);

    if (validation.valid) {
      return state;
    }

    // Try to repair
    const repairedState = await attemptRepair(state, validation.errors);
    if (await validateState(repairedState).valid) {
      await saveState(repairedState);
      return repairedState;
    }

    // Restore from backup
    return await restoreFromBackup();

  } catch (error) {
    if (error.code === 'ENOENT') {
      // No state file - this is okay for new projects
      return null;
    }

    // Try backup restoration
    console.log('State file corrupted. Attempting backup restoration...');
    return await restoreFromBackup();
  }
}
```

---

## 10. Best Practices

### Do's

1. **Always update state immediately** after user decisions
2. **Cite sources** for every technical decision
3. **Create backups** before phase transitions
4. **Validate state** after loading
5. **Show state summary** at session start
6. **Use atomic writes** to prevent corruption

### Don'ts

1. **Never skip state updates** - even small decisions matter
2. **Never assume previous state** - always load and verify
3. **Never delete without backup** - archive first
4. **Never hardcode paths** - use configuration
5. **Never ignore validation warnings** - they often indicate issues

### State Hygiene

- Review and clean up stale data periodically
- Archive completed projects
- Maintain consistent naming conventions
- Document all manual state modifications

---

## Related Skills

- `handoff-protocol` - Details on agent handoff procedures
- `llm-capability-matching` - How to assign agents based on capabilities
- `architecture-decision-records` - ADR format for decisions

## References

- [JSON Schema Draft-07](https://json-schema.org/draft-07/json-schema-release-notes.html)
- State schema: `references/state-schema.json`
