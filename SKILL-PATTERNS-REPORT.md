# Skill Authoring Guide

Rules and patterns for creating effective builder skills.

---

## Directory Structure

```
skill-name/
├── SKILL.md              # Required - main entry point
├── LICENSE.txt           # License file (optional)
├── scripts/              # Executable helper scripts
│   └── helper.py
├── references/           # Additional documentation loaded on-demand
│   └── advanced-topic.md
└── assets/               # Templates, boilerplate (not loaded to context)
    └── template.md
```

---

## SKILL.md Frontmatter

```yaml
---
name: skill-name
description: Clear, trigger-focused description that explains WHEN to use this skill. This is what determines automatic activation.
allowed-tools: Read, Grep, Glob, Bash, Edit, Write
---
```

**Critical:** The `description` field triggers skill activation. Write it from the perspective of "Use when..." scenarios.

---

## Core Rules

### 1. Progressive Disclosure

Load information in tiers to minimize context usage:

| Tier | Location      | Size       | When Loaded                        |
| ---- | ------------- | ---------- | ---------------------------------- |
| 1    | Frontmatter   | ~100 words | Always (triggers activation)       |
| 2    | SKILL.md body | <5k words  | After trigger                      |
| 3    | References    | Unlimited  | On-demand via `skill/resource.md` |

**Rule:** Keep SKILL.md lean. Push deep knowledge into reference files.

### 2. Reference Tables

Use lookup tables to guide contextual loading:

```markdown
## Specialized Resources

| Domain | Resource           | When to Use                   |
| ------ | ------------------ | ----------------------------- |
| Type X | `skill/type-x.md` | Trigger condition description |
| Type Y | `skill/type-y.md` | Different trigger condition   |
```

### 3. Quick-Start Section

Begin with immediate actionable steps, not philosophy:

```markdown
## Quick Start

1. **First action**: What to do immediately
2. **Classify**: How to categorize the problem
3. **Load specialist**: Which reference to pull in
4. **Execute**: Follow the methodology
```

### 4. Scripts for Deterministic Operations

Bundle scripts for repeatable tasks. Users run them with `--help` first.

**Good candidates for scripts:**

- Data gathering (audits, scans, profiling)
- Repetitive transformations
- Validation checks

**Script rules:**

- Document expected inputs/outputs
- Use `--help` for usage info
- Return structured output builder can parse

### 5. Assets for Templates

Assets are output resources—not loaded into context automatically, but available for builder to read and customize.

**Good candidates for assets:**

- Document templates
- Checklists
- Spreadsheet structures
- Questionnaires

### 6. Define a Methodology

Every skill should prescribe a structured approach:

```markdown
## Methodology

### Phase 1: [Name]

- Step details...

### Phase 2: [Name]

- Step details...
```

Examples:

- **Debugging**: Gather → Classify → Analyze → Solve → Verify
- **Security**: Scope → Threat Model → Scan → Review → Report
- **Review skills**: Audit → Categorize → Prioritize → Recommend

### 7. Output Format Templates

Prescribe structured output to ensure consistency:

```markdown
## Output Format

**Finding:** [Clear statement]
**Evidence:** [Supporting details]
**Recommendation:** [Actionable fix]
**Priority:** [Severity/importance]
```

### 8. Anti-Patterns Section

Explicitly call out common mistakes:

```markdown
## What NOT to Do

- Don't [common mistake 1]
- Avoid [common mistake 2]
- Never [dangerous pattern]
```

### 9. Degrees of Freedom

Match specificity to task fragility:

| Task Type      | Freedom Level | Example                    |
| -------------- | ------------- | -------------------------- |
| Creative       | High          | Art generation, design     |
| Advisory       | Medium        | Reviews, recommendations   |
| Data integrity | Low           | Form filling, calculations |

**Rule:** Error-prone operations need stricter guardrails.

### 10. Tool Scoping

Restrict tools to match skill intent:

| Skill Type       | Suggested Tools                         |
| ---------------- | --------------------------------------- |
| Review-only      | `Read, Grep, Glob`                      |
| Research         | `Read, Grep, Glob, WebFetch, WebSearch` |
| Document-focused | `Read, Write, Glob`                     |
| Full development | `Read, Grep, Glob, Bash, Edit, Write`   |

**Trade-off:** Broader access = more capability; narrower access = clearer intent.

### 11. Cross-Domain Awareness

When issues span multiple domains, instruct loading multiple references:

```markdown
## Multi-Domain Issues

When problems span categories, load all relevant resources:

- **Type + Async**: Load both `skill/types.md` and `skill/async.md`
- **Security + Performance**: Load both specialists
```

---

## Checklist for New Skills

- [ ] Frontmatter has trigger-focused `description`
- [ ] SKILL.md body is under 5k words
- [ ] Quick-start section with immediate actions
- [ ] Reference table for specialist resources
- [ ] Methodology with clear phases
- [ ] Output format template
- [ ] Anti-patterns / "what not to do" section
- [ ] Scripts for any repeatable operations
- [ ] Assets for any reusable templates
- [ ] Tool scope matches skill intent
