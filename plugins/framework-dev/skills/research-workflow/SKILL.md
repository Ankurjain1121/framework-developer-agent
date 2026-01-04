---
description: Use this skill to standardize the research process for technology recommendations. Provides research checklists, source verification criteria, and comparison templates.
---

# Research Workflow for Technology Decisions

This skill standardizes how the Framework Developer Orchestrator researches technology options before making recommendations.

---

## Research Principles

1. **Never Assume** - Always verify with official sources
2. **Cite Everything** - Every claim needs a URL
3. **Present Options** - Give user choices, don't dictate
4. **Stay Current** - Prefer recent sources (within 2 years)
5. **Consider Context** - Match recommendations to project needs

---

## Research Workflow

### Step 1: Identify the Question

```markdown
**Research Topic:** [e.g., "Database selection for task management app"]

**Key Criteria:**
- [ ] Relational vs document data model
- [ ] Scale requirements
- [ ] Team familiarity
- [ ] Cost constraints
- [ ] Hosting options
```

### Step 2: Gather Options

For each technology category:

```markdown
**Options to Research:**
1. [Option A] - [Brief description]
2. [Option B] - [Brief description]
3. [Option C] - [Brief description]
```

### Step 3: Research Each Option

```markdown
### Research: [Option Name]

**Official Sources:**
- Documentation: [URL]
- Getting Started: [URL]
- Use Cases: [URL]

**Key Facts:**
- Latest stable version: [version]
- License: [license]
- Pricing: [pricing model]

**Claimed Strengths:**
- [Strength 1] (Source: [URL])
- [Strength 2] (Source: [URL])

**Known Limitations:**
- [Limitation 1] (Source: [URL])
- [Limitation 2] (Source: [URL])

**Fit for Our Project:**
- [Analysis of how it fits our needs]
```

### Step 4: Compare Options

```markdown
## Comparison Matrix

| Criteria | Option A | Option B | Option C |
|----------|----------|----------|----------|
| [Criterion 1] | ✅ Excellent | ⚠️ Good | ❌ Poor |
| [Criterion 2] | ⚠️ Good | ✅ Excellent | ⚠️ Good |
| [Criterion 3] | ✅ Excellent | ✅ Excellent | ⚠️ Good |
| **Overall Fit** | ⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ | ⭐⭐⭐ |
```

### Step 5: Make Recommendation

```markdown
## Recommendation

**Recommended:** [Option]

**Rationale:**
Based on our project requirements:
1. [Reason 1 with source]
2. [Reason 2 with source]
3. [Reason 3 with source]

**Trade-offs Accepted:**
- [What we give up by choosing this]

**Alternative for Consideration:**
If [condition], consider [alternative] instead because [reason].
```

---

## Source Quality Criteria

### Tier 1: Most Reliable

| Source Type | Example | Trust Level |
|-------------|---------|-------------|
| Official docs | postgresql.org/docs | Highest |
| Official blog | blog.react.dev | High |
| Official benchmarks | [vendor benchmarks] | High* |

*Note: Vendor benchmarks may be biased

### Tier 2: Generally Reliable

| Source Type | Example | Trust Level |
|-------------|---------|-------------|
| Established tech blogs | martinfowler.com | High |
| Conference talks | Strange Loop, QCon | High |
| Peer-reviewed comparisons | VLDB, SIGMOD papers | High |

### Tier 3: Use with Caution

| Source Type | Example | Trust Level |
|-------------|---------|-------------|
| Medium articles | medium.com/@author | Medium |
| Stack Overflow | stackoverflow.com | Medium |
| Dev.to | dev.to | Medium |
| Reddit | r/programming | Low-Medium |

### Avoid

- Anonymous blog posts
- Outdated content (>2 years for fast-moving tech)
- Clearly sponsored content without disclosure
- Sources without dates

---

## Research Templates

### Database Selection

```markdown
## Database Research: [Database Name]

**Category:** Relational | Document | Graph | Time-series | Key-Value

**Official Resources:**
- Documentation: [URL]
- Use Cases: [URL]

**Technical Specs:**
- Query Language: [SQL/NoSQL/Custom]
- ACID Compliance: Full | Document-level | None
- Scaling: Vertical | Horizontal | Both
- Max Connections: [number]
- Storage Engine: [engine]

**Performance (from official benchmarks):**
- Read throughput: [ops/sec]
- Write throughput: [ops/sec]
- Latency: [p99 latency]

**Deployment Options:**
- Self-hosted: [Yes/No]
- Managed: [List providers]
- Serverless: [Yes/No]

**Pricing:**
- Open Source: [Yes/No]
- Managed pricing: [Starting from $X/month]

**Team Familiarity:**
- Ask user: "What's your experience with [database]?"
```

### Framework Selection

```markdown
## Framework Research: [Framework Name]

**Category:** Backend | Frontend | Full-stack

**Official Resources:**
- Documentation: [URL]
- Tutorial: [URL]
- Examples: [URL]

**Technical Specs:**
- Language: [Language]
- Latest Version: [version]
- Release Date: [date]
- License: [license]

**Features:**
- [Feature 1]
- [Feature 2]
- [Feature 3]

**Ecosystem:**
- Package Manager: [npm/pip/etc]
- Number of packages: [count]
- Community size: [GitHub stars, npm downloads]

**Learning Curve:**
- Beginner-friendly: [Yes/No]
- Documentation quality: [Excellent/Good/Fair/Poor]
- Community support: [Active/Moderate/Limited]
```

---

## Search Strategies

### For Official Information

```
site:postgresql.org performance benchmarks
site:react.dev hooks best practices
```

### For Comparisons

```
"PostgreSQL vs MongoDB" site:*.org OR site:*.edu
"React vs Vue 2024" comparison
```

### For Real-World Usage

```
"we chose PostgreSQL because" site:github.com
"migrated from MongoDB to" case study
```

---

## Presenting Research to User

```markdown
## Research Results: [Topic]

I've researched [topic] using official documentation and reliable sources.

### Summary

| Option | Best For | Source |
|--------|----------|--------|
| [A] | [Use case] | [URL] |
| [B] | [Use case] | [URL] |
| [C] | [Use case] | [URL] |

### My Recommendation

Based on your project requirements:
- [Requirement 1]
- [Requirement 2]

I recommend **[Option]** because:
1. [Reason with source]
2. [Reason with source]

### Questions for You

1. [Clarifying question about requirements]
2. [Question about team experience]

What are your thoughts?
```

---

## Updating Stale Research

If research is more than 6 months old:

1. Re-check official sources for version updates
2. Look for any major changes or deprecations
3. Verify pricing is current
4. Update comparison matrix

---

## Integration with Framework Developer

Use this workflow in:
- **Phase 1**: Technology stack decisions
- **Phase 2**: Architecture pattern selection
- **Phase 3**: API framework choices
- **Phase 4**: LLM capability research

Always:
1. Research before recommending
2. Cite all sources
3. Present options to user
4. Get user approval before proceeding
