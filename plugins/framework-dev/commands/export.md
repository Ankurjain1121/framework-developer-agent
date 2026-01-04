---
description: Export blueprints to shareable formats like PDF, HTML, or Notion-compatible markdown.
argument-hint: "[pdf|html|notion|markdown] [output-path]"
allowed-tools: Read, Write, Bash, Glob
---

# Export Command

Export framework blueprints to shareable formats for documentation, presentations, or external collaboration.

## Usage

```
/export pdf                    # Export to PDF (requires pandoc)
/export html                   # Export to standalone HTML
/export notion                 # Export Notion-compatible markdown
/export markdown ./docs/       # Export combined markdown to directory
```

## Mode: $ARGUMENTS

---

## Export: PDF

Generate a professional PDF document from all blueprints.

### Prerequisites

```bash
# Check if pandoc is installed
which pandoc || echo "Please install pandoc: https://pandoc.org/installing.html"

# Optional: Install for better PDFs
which pdflatex || echo "For better PDFs, install LaTeX"
```

### Step 1: Gather All Blueprints

```bash
find .framework-blueprints -name "*.md" -type f | sort
```

### Step 2: Create Combined Markdown

```markdown
---
title: "[Project Name] Framework Blueprint"
author: "Framework Developer Orchestrator"
date: "[Generated Date]"
---

# Table of Contents

1. Project Overview
2. Phase 1: Discovery
3. Phase 2: Structure
4. Phase 3: API Planning
5. Phase 4: Agent Assignment
6. Phase 5: Execution
7. Phase 6: Integration

---

[Content from all blueprint files...]
```

### Step 3: Generate PDF

```bash
pandoc combined-blueprint.md \
  -o "[project-name]-blueprint.pdf" \
  --toc \
  --toc-depth=3 \
  --pdf-engine=pdflatex \
  -V geometry:margin=1in \
  -V fontsize=11pt
```

### Step 4: Report

```markdown
✅ **PDF Export Complete**

**Output:** `[project-name]-blueprint.pdf`
**Pages:** ~[N]
**Size:** [X]KB

**Contents:**
- Project overview
- Architecture diagrams (as images)
- API specifications
- Decision log
- Final report

**Open with:** Your default PDF viewer
```

---

## Export: HTML

Generate a standalone HTML file with embedded styles.

### Step 1: Create HTML Template

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>[Project Name] - Framework Blueprint</title>
  <style>
    :root {
      --primary: #4F46E5;
      --bg: #ffffff;
      --text: #1f2937;
      --border: #e5e7eb;
    }
    body {
      font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif;
      line-height: 1.6;
      max-width: 900px;
      margin: 0 auto;
      padding: 2rem;
      color: var(--text);
    }
    h1, h2, h3 { color: var(--primary); }
    h1 { border-bottom: 2px solid var(--primary); padding-bottom: 0.5rem; }
    h2 { margin-top: 2rem; }
    pre {
      background: #f3f4f6;
      padding: 1rem;
      border-radius: 0.5rem;
      overflow-x: auto;
    }
    code {
      background: #f3f4f6;
      padding: 0.2rem 0.4rem;
      border-radius: 0.25rem;
    }
    table {
      width: 100%;
      border-collapse: collapse;
      margin: 1rem 0;
    }
    th, td {
      border: 1px solid var(--border);
      padding: 0.75rem;
      text-align: left;
    }
    th { background: #f9fafb; }
    .toc { background: #f9fafb; padding: 1rem; border-radius: 0.5rem; }
    .toc a { color: var(--primary); text-decoration: none; }
    .toc a:hover { text-decoration: underline; }
    .mermaid { text-align: center; margin: 1rem 0; }
    @media (prefers-color-scheme: dark) {
      :root {
        --bg: #1f2937;
        --text: #f3f4f6;
        --border: #374151;
      }
      body { background: var(--bg); }
      pre, code, th, .toc { background: #374151; }
    }
  </style>
  <script src="https://cdn.jsdelivr.net/npm/mermaid/dist/mermaid.min.js"></script>
  <script>mermaid.initialize({startOnLoad:true});</script>
</head>
<body>
  [CONTENT]
</body>
</html>
```

### Step 2: Convert Markdown to HTML

```bash
pandoc combined-blueprint.md \
  -o content.html \
  --standalone \
  --toc
```

### Step 3: Embed in Template

Combine template with content.

### Step 4: Report

```markdown
✅ **HTML Export Complete**

**Output:** `[project-name]-blueprint.html`
**Size:** [X]KB

**Features:**
- Dark mode support
- Interactive Mermaid diagrams
- Responsive design
- Standalone (no external dependencies)

**Open with:** Any web browser
```

---

## Export: Notion

Generate Notion-compatible markdown with proper formatting.

### Notion Formatting Rules

| Standard Markdown | Notion Markdown |
|-------------------|-----------------|
| `- [ ]` checkbox | Same (works) |
| `> quote` | Same (works) |
| `---` divider | Same (works) |
| Tables | Same (works) |
| Mermaid | Needs code block with `mermaid` |

### Step 1: Process Each File

For each blueprint file:
1. Read content
2. Ensure mermaid blocks are formatted correctly
3. Add page separators

### Step 2: Create Notion-Ready Files

```markdown
# [Project Name] Blueprint

> Exported from Framework Developer Orchestrator
> Generated: [Date]

---

## Quick Links
- [Phase 1: Discovery](#phase-1-discovery)
- [Phase 2: Structure](#phase-2-structure)
- [API Contracts](#api-contracts)

---

## Phase 1: Discovery

[Content with Notion-compatible formatting...]

```

### Step 3: Report

```markdown
✅ **Notion Export Complete**

**Output Directory:** `./notion-export/`

**Files Created:**
- 01-project-overview.md
- 02-discovery.md
- 03-structure.md
- 04-api-planning.md
- 05-agent-assignment.md
- 06-execution.md
- 07-integration.md
- 08-final-report.md

**Import to Notion:**
1. Open Notion
2. Create new page
3. Click ⋮ menu → Import → Markdown
4. Select files from `./notion-export/`

**Note:** Mermaid diagrams may need manual re-creation in Notion.
```

---

## Export: Markdown

Combine all blueprints into a single organized markdown file or directory.

### Step 1: Gather Content

Read all blueprint files in order.

### Step 2: Create Structure

```markdown
# [Project Name] - Complete Blueprint

**Generated:** [Date]
**Version:** [from state file]

---

## Table of Contents

1. [Project State](#project-state)
2. [Phase 1: Discovery](#phase-1-discovery)
   - [Outline](#outline)
   - [Architecture](#architecture)
   - [Decisions](#decisions)
3. [Phase 2: Structure](#phase-2-structure)
...

---

# Project State

[Content from 00-project-state.json formatted nicely]

---

# Phase 1: Discovery

## Outline

[Content from outline-v1.md]

## Architecture

[Content from architecture-diagram.md]

...
```

### Step 3: Output

If directory specified:
```bash
mkdir -p [output-path]
# Copy each file to output directory
```

If single file:
```bash
# Write combined content to single file
```

### Step 4: Report

```markdown
✅ **Markdown Export Complete**

**Output:** `[path]`

**Contents:**
- Combined documentation (~[N] lines)
- All diagrams included
- Cross-references preserved

**Use Cases:**
- GitHub Wiki
- Documentation site
- Sharing with team
```

---

## Common Options

### Include/Exclude Phases

```
/export pdf --phases 1,2,3      # Only phases 1-3
/export html --exclude-prompts  # Skip agent prompts
```

### Custom Styling

```
/export html --theme dark       # Dark theme
/export pdf --template custom   # Custom template
```

---

## Error Handling

### Missing Tools

```markdown
⚠️ **Export requires additional tools**

For PDF export, please install:
- pandoc: https://pandoc.org/installing.html
- LaTeX (optional): For better PDF quality

For now, try:
- `/export html` (no dependencies)
- `/export markdown` (no dependencies)
```

### Empty Blueprints

```markdown
⚠️ **Some blueprint files are empty or missing**

Missing files:
- 03-api-planning/api-contracts.md
- 06-integration/final-report.md

Export will proceed with available files.
Continue? [yes/no]
```
