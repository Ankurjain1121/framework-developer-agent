---
name: llm-capability-matching
description: Match development tasks to optimal LLMs based on capabilities, context windows, cost, and task requirements. Use this skill when assigning work to multiple agents or deciding which LLM to use for specific development tasks.
---

# LLM Capability Matching Skill

## Purpose

This skill enables the Framework Developer Orchestrator to intelligently match development tasks to the most suitable LLM based on:
- Task complexity and type
- LLM strengths and weaknesses
- Context window requirements
- Cost considerations
- Latency requirements
- Local vs cloud trade-offs

## Quick Reference: Capability Matrix

### LLM x Task Type Matrix

| Task Type | Claude Opus | Claude Sonnet | GPT-4o | Gemini Pro | Qwen 2.5-Coder | Local (Llama/Mistral) |
|-----------|-------------|---------------|--------|------------|----------------|----------------------|
| **Frontend Dev** | A | A | A | B+ | B | C |
| **Backend Dev** | A+ | A | A | A | A | B |
| **API Design** | A+ | A | A | A | B+ | C |
| **Database Design** | A+ | A | A | B+ | B | C |
| **Testing/QA** | A | A | A | B+ | A | B |
| **Documentation** | A+ | A | A+ | A | B | C |
| **Code Review** | A+ | A | A | B+ | A | B |
| **Debugging** | A+ | A | A | B | A | B |
| **DevOps/Infra** | A | A | A | B+ | B | C |
| **Refactoring** | A+ | A | A | B+ | A | B |

**Legend:** A+ = Exceptional | A = Excellent | B+ = Good | B = Adequate | C = Limited

---

## Detailed LLM Profiles

### 1. Claude Family (Anthropic)

#### Claude Opus 4.5 (claude-opus-4-5-20251101)
**Context Window:** 200K tokens
**Strengths:**
- Superior reasoning and analysis
- Best-in-class code understanding
- Exceptional at complex architectural decisions
- Strong at maintaining consistency across large codebases
- Excellent documentation generation
- Best for ambiguous requirements interpretation

**Best Uses:**
- System architecture design
- Complex debugging requiring deep reasoning
- Code reviews requiring nuanced feedback
- API design with complex business logic
- Database schema design with optimization
- Technical documentation and ADRs

**Limitations:**
- Higher cost per token
- Slower response times than Sonnet
- May be overkill for simple tasks

**Cost Tier:** $$$$

#### Claude Sonnet 4 (claude-sonnet-4-20250514)
**Context Window:** 200K tokens
**Strengths:**
- Excellent balance of capability and speed
- Strong coding abilities
- Good reasoning at lower cost
- Fast iteration cycles
- Reliable for most development tasks

**Best Uses:**
- Day-to-day coding tasks
- Feature implementation
- Unit test writing
- Bug fixes
- Code refactoring
- Standard API endpoints

**Limitations:**
- Less nuanced reasoning than Opus
- May miss edge cases in complex scenarios

**Cost Tier:** $$

#### Claude Haiku (claude-3-5-haiku-20241022)
**Context Window:** 200K tokens
**Strengths:**
- Very fast responses
- Cost-effective for simple tasks
- Good for high-volume operations
- Sufficient for well-defined tasks

**Best Uses:**
- Simple code generation
- Boilerplate creation
- Quick lookups and summaries
- Routine transformations
- Simple documentation updates

**Limitations:**
- Limited reasoning depth
- May struggle with complex logic
- Not suitable for architectural decisions

**Cost Tier:** $

#### Claude Code (CLI)
**Context Window:** 200K tokens (same as Sonnet/Opus)
**Strengths:**
- Direct file system access
- Can execute commands
- Integrated with development workflow
- Persistent session state
- Multi-file editing capability

**Best Uses:**
- End-to-end feature development
- Project scaffolding
- Multi-file refactoring
- Build and deployment tasks
- Interactive debugging

**Limitations:**
- Requires local setup
- Rate limits apply

**Cost Tier:** $$ (same as underlying model)

---

### 2. GPT-4 Family (OpenAI)

#### GPT-4o
**Context Window:** 128K tokens
**Strengths:**
- Excellent general coding ability
- Strong at creative solutions
- Good documentation generation
- Reliable for most tasks
- Fast multimodal capabilities

**Best Uses:**
- General development tasks
- UI/UX implementation with visual context
- Creative problem-solving
- Cross-language translations
- Documentation with diagrams

**Limitations:**
- Smaller context than Claude
- May be more verbose
- Less consistent on very long tasks

**Cost Tier:** $$

#### GPT-4 Turbo
**Context Window:** 128K tokens
**Strengths:**
- More current training data
- Good balance of cost and capability
- JSON mode for structured outputs

**Best Uses:**
- Standard development tasks
- API integration work
- Data transformation
- Structured output generation

**Limitations:**
- Less capable than GPT-4o for complex reasoning
- Context window smaller than Claude

**Cost Tier:** $$

---

### 3. Gemini Family (Google)

#### Gemini 2.0 Pro
**Context Window:** 1M+ tokens (largest available)
**Strengths:**
- Massive context window for large codebases
- Strong at cross-file analysis
- Good multimodal capabilities
- Competitive coding abilities

**Best Uses:**
- Large codebase analysis
- Cross-repository work
- When context window is critical
- Multimodal tasks (code + diagrams)

**Limitations:**
- May be less precise than Claude for complex reasoning
- Availability varies by region
- API stability concerns

**Cost Tier:** $$

#### Gemini Ultra
**Context Window:** 1M+ tokens
**Strengths:**
- Most powerful Gemini model
- Best for complex multimodal tasks
- Strong reasoning capabilities

**Best Uses:**
- Complex architectural analysis
- Large-scale refactoring
- When massive context is needed

**Limitations:**
- Limited availability
- Higher cost
- May have longer latency

**Cost Tier:** $$$$

---

### 4. Qwen Family (Alibaba)

#### Qwen 2.5-Coder (72B)
**Context Window:** 128K tokens
**Strengths:**
- Exceptional code completion
- Strong at multiple programming languages
- Good at code explanation
- Can run locally with sufficient hardware
- Open weights available

**Best Uses:**
- Code completion and generation
- Multi-language projects
- Local/air-gapped environments
- Cost-sensitive projects
- Repetitive coding tasks

**Limitations:**
- Less strong at high-level architecture
- May need fine-tuning for specific domains
- Reasoning less robust than Claude/GPT-4

**Cost Tier:** $ (especially local)

#### Qwen 2.5-Coder (32B/7B)
**Context Window:** 32K-128K tokens
**Strengths:**
- Runnable on consumer hardware
- Good code completion
- Fast inference locally
- No API costs when self-hosted

**Best Uses:**
- Local development assistance
- Code completion
- Simple refactoring
- Privacy-sensitive projects

**Limitations:**
- Less capable than larger models
- Limited reasoning
- Not suitable for complex tasks

**Cost Tier:** Free (local)

---

### 5. Local Models

#### Llama 3.1 (405B/70B/8B)
**Context Window:** 128K tokens
**Strengths:**
- Open source, fully customizable
- No API costs when self-hosted
- Good general capabilities
- Can be fine-tuned

**Best Uses:**
- Air-gapped environments
- Privacy-critical projects
- Custom fine-tuning scenarios
- High-volume, cost-sensitive tasks

**Limitations:**
- Requires significant hardware (405B needs ~8x A100)
- Less capable than frontier models
- No tool use without additional setup

**Cost Tier:** Free (hardware costs apply)

#### Mistral Large / Mixtral
**Context Window:** 32K-128K tokens
**Strengths:**
- Good coding capabilities
- Fast inference
- European data residency option
- MoE architecture for efficiency

**Best Uses:**
- EU compliance requirements
- Cost-sensitive coding tasks
- Local deployment
- When Llama isn't suitable

**Limitations:**
- Less capable than Claude/GPT-4
- Smaller context window
- Limited tool use

**Cost Tier:** $ (API) / Free (local)

#### DeepSeek Coder V2
**Context Window:** 128K tokens
**Strengths:**
- Excellent code generation
- Strong at algorithms
- Can run locally
- Very cost-effective

**Best Uses:**
- Algorithm implementation
- Code generation tasks
- Local development
- Budget-conscious projects

**Limitations:**
- Less strong at architecture
- Limited reasoning capabilities
- May produce verbose code

**Cost Tier:** $ (very low)

---

### 6. Specialized Models

#### GitHub Copilot (based on Codex/GPT-4)
**Context Window:** Limited (IDE context)
**Strengths:**
- IDE integration
- Real-time suggestions
- Good at completing partial code
- Learns from project context

**Best Uses:**
- Line-by-line code completion
- Boilerplate reduction
- In-IDE assistance
- Quick snippets

**Limitations:**
- Not suitable for architectural decisions
- Limited context awareness
- Can suggest insecure patterns

**Cost Tier:** $ (subscription)

#### StarCoder 2
**Context Window:** 16K tokens
**Strengths:**
- Open source
- Trained specifically on code
- Good at many languages
- Can run locally

**Best Uses:**
- Code completion
- Local development
- Open source projects
- When licensing matters

**Limitations:**
- Smaller context window
- Less capable than frontier models
- Limited reasoning

**Cost Tier:** Free (local)

---

## Task Matching Decision Framework

### Step 1: Categorize the Task

```
Task Categories:
1. ARCHITECTURE - System design, module planning, tech stack selection
2. IMPLEMENTATION - Writing new code, features, functions
3. REVIEW - Code review, security audit, quality check
4. DEBUG - Finding and fixing bugs, error analysis
5. TEST - Writing tests, test strategy, coverage
6. DOCS - Documentation, comments, READMEs
7. DEVOPS - CI/CD, infrastructure, deployment
8. REFACTOR - Improving existing code structure
```

### Step 2: Assess Complexity

```
Complexity Levels:
- LOW: Well-defined, single-file, clear requirements
- MEDIUM: Multi-file, some ambiguity, moderate logic
- HIGH: Cross-module, complex logic, architectural impact
- CRITICAL: System-wide, security-sensitive, performance-critical
```

### Step 3: Check Context Requirements

```
Context Size Estimation:
- Files to read: Count * avg lines * 4 (tokens per line estimate)
- Files to modify: Count * avg lines * 4
- Documentation needed: Estimated tokens
- Buffer for conversation: 20% of limit

If total > 80% of context window, consider:
1. Using Gemini (1M+ context)
2. Splitting task into smaller chunks
3. Using RAG/retrieval approach
```

### Step 4: Apply Matching Rules

```python
def match_llm(task_type, complexity, context_size, budget, latency_req):
    # Critical tasks always use best available
    if complexity == "CRITICAL":
        return "Claude Opus" if budget != "LOW" else "Claude Sonnet"

    # Architecture tasks need strong reasoning
    if task_type == "ARCHITECTURE":
        if complexity == "HIGH":
            return "Claude Opus"
        return "Claude Sonnet" or "GPT-4o"

    # Large context needs Gemini
    if context_size > 150000:
        return "Gemini Pro"

    # Code-heavy tasks with medium complexity
    if task_type in ["IMPLEMENTATION", "REFACTOR", "TEST"]:
        if budget == "LOW":
            return "Qwen 2.5-Coder" or "Local Llama"
        if latency_req == "FAST":
            return "Claude Sonnet"
        return "Claude Sonnet" or "GPT-4o"

    # Documentation
    if task_type == "DOCS":
        return "Claude Opus" if quality_critical else "Claude Sonnet"

    # Debugging
    if task_type == "DEBUG":
        return "Claude Opus" if complexity == "HIGH" else "Claude Sonnet"

    # Default
    return "Claude Sonnet"
```

---

## Session Planning: How Many Sessions?

### Determining Session Count

**Factors to Consider:**
1. **Task Independence:** Can tasks run in parallel?
2. **Context Overlap:** Do tasks share significant context?
3. **Handoff Complexity:** How much state transfers between tasks?
4. **Time Constraints:** Parallel = faster total time
5. **Cost Budget:** More sessions = higher cost

### Session Division Strategy

```
Single Session When:
- Tasks are sequential and dependent
- Heavy context sharing (same files)
- Total tokens < 50% of context window
- Consistency is critical

Multiple Sessions When:
- Tasks are independent
- Different domains (frontend/backend)
- Total context > 60% of window
- Time is critical (parallel execution)
```

### Work Division Between Same-LLM Sessions

**Horizontal Division (by layer):**
```
Session 1 (Claude Sonnet): Frontend components
Session 2 (Claude Sonnet): Backend API endpoints
Session 3 (Claude Sonnet): Database operations
```

**Vertical Division (by feature):**
```
Session 1 (Claude Sonnet): User authentication (all layers)
Session 2 (Claude Sonnet): Product catalog (all layers)
Session 3 (Claude Sonnet): Order management (all layers)
```

**Hybrid Division:**
```
Session 1 (Claude Opus): Core architecture + critical paths
Session 2 (Claude Sonnet): Feature A implementation
Session 3 (Claude Sonnet): Feature B implementation
Session 4 (Qwen): Boilerplate and tests
```

### Session Handoff Protocol

```markdown
## Handoff Document Template

### From: [Session ID] - [LLM]
### To: [Session ID] - [LLM]

**Completed:**
- [ ] List of completed tasks
- [ ] Files created/modified

**Context for Next Session:**
- Key decisions made: [list]
- Patterns established: [list]
- API contracts defined: [list]

**Files to Read:**
- `path/to/file1.ts` - Main component
- `path/to/file2.ts` - Types

**Dependencies:**
- Expects: [what this session needs from previous]
- Provides: [what this session outputs for next]

**Warnings:**
- Known issues: [list]
- Edge cases: [list]
```

---

## Context Window Management

### Token Estimation Guide

```
Rough Estimates:
- 1 line of code = 3-5 tokens
- 1 word of English = 1.3 tokens
- 1 JSON object = 10-50 tokens
- 1 function (avg) = 50-200 tokens
- 1 file (avg) = 500-2000 tokens
```

### Context Budget Allocation

```
For 200K context window:

Recommended Allocation:
- System prompt: 2K (1%)
- Project context: 40K (20%)
- Current task files: 80K (40%)
- Conversation history: 40K (20%)
- Response buffer: 38K (19%)

Warning Zones:
- 60% used: Consider summarizing history
- 80% used: Start new session or trim context
- 90% used: Critical - must reduce context
```

### Context Optimization Techniques

1. **Selective File Loading:**
   - Only load files directly relevant to current task
   - Use summaries for peripheral context

2. **Progressive Disclosure:**
   - Start with file structure overview
   - Load full files only when needed

3. **Context Compression:**
   - Summarize completed discussions
   - Keep only relevant code sections

4. **RAG for Large Codebases:**
   - Index codebase externally
   - Retrieve relevant chunks on demand

---

## Cost Considerations

### Pricing Tiers (Relative)

| Model | Input (per 1M tokens) | Output (per 1M tokens) | Relative Cost |
|-------|----------------------|------------------------|---------------|
| Claude Opus | $15 | $75 | 5x |
| Claude Sonnet | $3 | $15 | 1x (baseline) |
| Claude Haiku | $0.25 | $1.25 | 0.1x |
| GPT-4o | $2.50 | $10 | 0.8x |
| GPT-4 Turbo | $10 | $30 | 3x |
| Gemini Pro | $1.25 | $5 | 0.5x |
| Qwen (API) | $0.50 | $2 | 0.2x |
| Local Models | Hardware only | Hardware only | 0x (amortized) |

### Cost Optimization Strategies

1. **Model Tiering:**
   ```
   Tier 1 (Opus): Architecture, critical decisions
   Tier 2 (Sonnet): Main development work
   Tier 3 (Haiku/Qwen): Boilerplate, simple tasks
   ```

2. **Batch Similar Tasks:**
   - Group related tasks to reduce context loading overhead

3. **Cache Reusable Context:**
   - System prompts, project structure, coding standards

4. **Use Cheaper Models for Validation:**
   - Generate with Sonnet, validate with Haiku

### Cost Estimation Formula

```
Estimated Cost =
  (input_tokens * input_price) +
  (output_tokens * output_price) *
  estimated_iterations

Example:
- Task: Implement REST API (5 endpoints)
- Estimated: 50K input, 20K output, 3 iterations
- Using Sonnet: ($3 * 0.05) + ($15 * 0.02) * 3 = $1.05
```

---

## Local vs Cloud Decision Matrix

### Use Local Models When:

| Scenario | Recommended Local Model |
|----------|------------------------|
| Air-gapped environment | Llama 3.1 70B |
| Privacy-sensitive code | Llama 3.1 / Mistral |
| High-volume, simple tasks | Qwen 2.5-Coder 7B |
| Zero marginal cost needed | Any local model |
| EU data residency | Mistral (EU servers) |
| Custom fine-tuning needed | Llama / Mistral |

### Use Cloud Models When:

| Scenario | Recommended Cloud Model |
|----------|------------------------|
| Complex reasoning | Claude Opus |
| Fast iteration | Claude Sonnet |
| Massive context | Gemini Pro |
| Best-in-class quality | Claude Opus |
| Multimodal needs | GPT-4o / Gemini |
| Production reliability | Claude / GPT-4 |

### Hybrid Approach

```
Development Phase:
- Architecture: Cloud (Opus)
- Core logic: Cloud (Sonnet)
- Boilerplate: Local (Qwen)
- Tests: Mixed (Sonnet + Qwen)

CI/CD Phase:
- Code review: Cloud (Sonnet)
- Static analysis: Local (fast, free)
- Documentation: Cloud (quality matters)
```

---

## Task-Specific Recommendations

### Frontend Development

**Primary:** Claude Sonnet / GPT-4o
**Alternative:** Qwen 2.5-Coder

```
Best Practices:
- Use Claude for React/Vue/Angular logic
- GPT-4o good for CSS and styling
- Qwen for repetitive component creation
- Always include design system context
```

### Backend Development

**Primary:** Claude Sonnet
**Complex Logic:** Claude Opus
**Simple CRUD:** Qwen 2.5-Coder

```
Best Practices:
- Claude excels at API design
- Use Opus for security-critical code
- Qwen for boilerplate endpoints
- Include API specs in context
```

### API Design

**Primary:** Claude Opus
**Alternative:** Claude Sonnet

```
Best Practices:
- Opus for initial design and contracts
- Sonnet for implementation
- Always include existing API patterns
- Reference OpenAPI specs
```

### Database Design

**Primary:** Claude Opus
**Implementation:** Claude Sonnet

```
Best Practices:
- Opus for schema design and optimization
- Include data volume estimates
- Reference existing schemas
- Consider query patterns
```

### Testing/QA

**Primary:** Claude Sonnet
**High-Volume:** Qwen 2.5-Coder

```
Best Practices:
- Sonnet for test strategy and edge cases
- Qwen for generating many similar tests
- Include existing test patterns
- Reference coverage requirements
```

### Documentation

**Primary:** Claude Opus (quality) / Sonnet (speed)

```
Best Practices:
- Opus for architecture docs and ADRs
- Sonnet for API docs and READMEs
- Include existing doc style guides
- Generate diagrams with Mermaid
```

### Code Review

**Primary:** Claude Opus
**Quick Review:** Claude Sonnet

```
Best Practices:
- Opus for security and architecture review
- Sonnet for style and bug checking
- Include coding standards
- Reference past review feedback
```

### Debugging

**Primary:** Claude Opus (complex) / Sonnet (standard)

```
Best Practices:
- Opus for mysterious bugs
- Sonnet for clear error messages
- Include full stack traces
- Provide reproduction steps
```

### DevOps/Infrastructure

**Primary:** Claude Sonnet
**Complex Decisions:** Claude Opus

```
Best Practices:
- Sonnet for CI/CD pipelines
- Opus for architecture decisions
- Include existing infra context
- Reference cloud provider docs
```

---

## Implementation Checklist

When assigning LLMs to tasks:

- [ ] Categorized all tasks by type
- [ ] Assessed complexity level for each
- [ ] Estimated context requirements
- [ ] Checked budget constraints
- [ ] Identified parallel vs sequential tasks
- [ ] Selected primary LLM for each task
- [ ] Planned session division strategy
- [ ] Created handoff documents
- [ ] Set up context management plan
- [ ] Established quality checkpoints

---

## Quick Decision Flowchart

```
START
  |
  v
Is task architecture/critical?
  |
  YES --> Claude Opus
  |
  NO
  |
  v
Context > 150K tokens?
  |
  YES --> Gemini Pro
  |
  NO
  |
  v
Budget constrained?
  |
  YES --> Is task simple?
  |         |
  |         YES --> Qwen/Haiku
  |         |
  |         NO --> Claude Sonnet
  |
  NO
  |
  v
Need fast iteration?
  |
  YES --> Claude Sonnet
  |
  NO
  |
  v
Complex reasoning needed?
  |
  YES --> Claude Opus
  |
  NO --> Claude Sonnet
```

---

## References

For detailed capability breakdowns, see:
- [LLM Strengths Reference](./references/llm-strengths.md)

For latest pricing and capabilities:
- Always verify with official documentation before finalizing assignments
- Use WebSearch to confirm current capabilities
- Check for model updates and new releases
