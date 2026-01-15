# PRD Section Templates

Detailed templates for each of the 7 PRD sections.

---

## Section 1: One-Sentence Problem

**Template:**
```
> [User role] struggles to [specific action] because [root cause], resulting in [measurable impact].
```

**Examples:**
```
> Freelancers struggle to track billable hours because switching between apps breaks flow, resulting in 20% unbilled time.

> Team leads struggle to see project blockers because updates are scattered across Slack threads, resulting in delayed decisions.
```

**Rules:**
- Pick ONE problem (most demo-worthy)
- Be specific about the user role
- Root cause should be actionable
- Impact should be tangible

---

## Section 2: Demo Goal

**Template:**
```markdown
### What Success Looks Like
[Describe the "aha moment" - what makes viewers say "I need this"]

### Demo Must Show
- [Capability 1]
- [Capability 2]
- [Capability 3]

### Non-Goals (Out of Scope)
- [Explicitly excluded 1]
- [Explicitly excluded 2]
```

**Rules:**
- Focus on believable demo, not production scale
- Non-goals prevent scope creep
- "Success" is what convinces someone, not feature completeness

---

## Section 3: Target User (Role-Based)

**Template:**
```markdown
### Primary User
- **Role:** [Job title or context]
- **Skill Level:** [Novice / Intermediate / Expert] in [relevant domain]
- **Key Constraint:** [Time-poor / Knowledge-limited / Access-restricted / etc.]
```

**Examples:**
```markdown
### Primary User
- **Role:** Solo founder building MVP
- **Skill Level:** Intermediate in coding, novice in design
- **Key Constraint:** Time-poor, can't learn new tools
```

**Rules:**
- ONE primary user only
- No demographics (age, location)
- No personas (fictional names, backstories)
- Constraint drives UX decisions

---

## Section 4: Core Use Case (Happy Path)

**Template:**
```markdown
### Happy Path

**Start:** [User's initial state/context]

1. User [action]
2. System [response]
3. User [action]
4. System [response]
5. ...

**End:** [User's end state - what they achieved]
```

**Example:**
```markdown
### Happy Path

**Start:** User has a rough app idea and wants to build a demo

1. User types rough idea in natural language
2. System generates structured PRD
3. User reviews, optionally refines
4. System generates UX spec (6 passes)
5. User approves UX foundations
6. System builds production-grade UI

**End:** User has working demo with solid UX foundations
```

**Rules:**
- Single path (no branches)
- If this works, demo works
- Number each step
- Alternate User/System actions

---

## Section 5: Functional Decisions

**Template:**
```markdown
| ID | Function | Notes |
|----|----------|-------|
| F1 | [Capability verb + object] | [Context or constraint] |
| F2 | [Capability verb + object] | [Context or constraint] |
```

**Example:**
```markdown
| ID | Function | Notes |
|----|----------|-------|
| F1 | Accept natural language input | Freeform text, no templates |
| F2 | Generate structured PRD | 7 sections, markdown format |
| F3 | Write output to file | .claude/design/PRD.md |
| F4 | Chain to next stage | Automatic progression |
```

**Rules:**
- Phrase as capabilities ("Accept X", "Generate Y", "Display Z")
- NOT implementation ("Use React", "Call API")
- No nice-to-haves
- Keep list tight (5-10 items max for demo)

---

## Section 6: UX Decisions

**Template:**
```markdown
### 6.1 Entry Point
- **How user starts:** [Trigger/command/action]
- **First thing they see:** [Initial state/prompt]

### 6.2 Inputs
- [Input 1]: [Format/constraints]
- [Input 2]: [Format/constraints]

### 6.3 Outputs
- [Output 1]: [Format, where it goes]
- [Output 2]: [Format, where it goes]

### 6.4 Feedback & States
- **Loading:** [What user sees while processing]
- **Success:** [What confirms completion]
- **Failure:** [What shows when something breaks]
- **Partial:** [What shows for incomplete results]

### 6.5 Errors (Minimum Viable Handling)
- **Invalid input:** [Response]
- **System failure:** [Response]
- **User does nothing:** [Timeout/prompt behavior]
```

**Rules:**
- Nothing left implicit
- Every state has a defined behavior
- Errors are first-class citizens

---

## Section 7: Data & Logic

**Template:**
```markdown
### 7.1 Inputs
| Source | Data | Notes |
|--------|------|-------|
| User | [What they provide] | [Format] |
| API | [External data] | [When fetched] |
| Static | [Hardcoded/mocked] | [Why static] |
| Generated | [AI/computed] | [How generated] |

### 7.2 Processing
```
[Input] → [Transform 1] → [Transform 2] → [Output]
```

### 7.3 Outputs
| Output | Destination | Persistence |
|--------|-------------|-------------|
| [What] | [Where] | UI only / Temp / Logged |
```

**Rules:**
- High-level logic only
- No architecture diagrams
- No tech stack decisions
- Focus on data flow, not implementation
