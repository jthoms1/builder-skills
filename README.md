# Builder Skills

A collection of reusable AI skills for Builder.io Fusion that optimize AI-driven development workflows.

## What are Skills?

Skills are specialized knowledge and workflows that you or the AI agent can invoke during code generation sessions. Each skill defines specialized instructions for a domain such as processing PDFs, analyzing data, or reviewing code.

Use skills when you have recurring tasks that benefit from consistent, specialized behavior. Skills help ensure the AI agent follows your preferred patterns every time.

## Quick Start

Create a skill directory in your project. In this case the skill is named `pdf-processor`:

```
.builder/skills/pdf-processor/
```

Add a `SKILL.md` file with your instructions:

```markdown
---
name: pdf-processor
description: Extract and analyze PDF documents
---

Instructions...
```

The skill is now available to invoke during your session.

## Creating Skills

### Directory Structure

Skills are discovered from three locations:

- `.builder/skills/` - Primary location for skills
- `.claude/skills/` - Alternative
- `.cursor/skills/` - Alternative

```
project/
└── .builder/
    └── skills/
        ├── pdf/
        │   └── SKILL.md
        ├── excel/
        │   └── SKILL.md
        └── office/
            └── word/
                └── SKILL.md     # Nested skills supported
```

The file must be named `SKILL.md` (case-insensitive). Other files in the skill directory are ignored, so you can include helper files, READMEs, examples or even scripts alongside your skill.

### Skills Format

Skills use a Markdown file with frontmatter that contains agent instructions. The `name` can be used as a direct reference in a prompt and the `description` provides context to the agent so that it can decide when to make use of the skill.

```markdown
---
name: skill-name
description: Brief description of what the skill does
---

Your skill instructions go here.

This content defines how the AI should behave when using this skill.
```

#### Frontmatter Fields

| Field | Required | Type | Description |
|-------|----------|------|-------------|
| `name` | No | string | Skill identifier. Defaults to parent directory name |
| `description` | No | string | Brief description shown when listing skills |

You can read more about the skills format at the official specification: https://agentskills.io/

### Organizing Skills

#### Grouping Related Skills

Use nested directories to organize related skills:

```
.builder/skills/
├── office/
│   ├── pdf/
│   │   └── SKILL.md
│   ├── excel/
│   │   └── SKILL.md
│   └── word/
│       └── SKILL.md
├── database/
│   ├── migrations/
│   │   └── SKILL.md
│   └── queries/
│       └── SKILL.md
└── testing/
    └── SKILL.md
```

#### Including Helper Files

Only `SKILL.md` files are loaded—other files are ignored:

```
.claude/skills/pdf/
├── SKILL.md           # The skill definition (required)
├── README.md          # Documentation (ignored)
├── examples/          # Example files (ignored)
│   └── sample.pdf
└── scripts/           # Helper scripts (ignored, but can be referenced)
    └── convert.sh
```

This lets you include supporting materials alongside your skill without affecting discovery. The agent loads these on demand, so smaller files mean less use of context.

### Skills vs Rules

Skills and Rules serve different purposes:

| Aspect | Rules | Skills |
|--------|-------|--------|
| Location | `.builder/rules/`, `.cursor/rules/` | `.builder/skills/`, `.claude/skills/` |
| File naming | Any `.mdc` or `.md` file | Must be `SKILL.md` |
| Purpose | Passive context and guidelines | Active, invocable capabilities |
| Invocation | Automatic based on context | Explicit invocation |

Use **Rules** for project-wide guidelines (coding standards, file conventions). Use **Skills** for specialized tasks you or the agent can invoke on demand (processing specific file types, running audits).

### Best Practices

- **Keep skills focused** — Each skill should handle one domain or task type well
- **Use descriptive names** — The name should clearly indicate what the skill does
- **Document capabilities** — Be explicit about what the skill can and cannot do
- **Include examples** — Show example usage or expected output in the instructions
- **Consider safety** — For destructive operations, include confirmation steps in the instructions

---

## Skills In this Repo

### AI Rules Reviewer

Audit, fix, and create Builder.io Fusion rules files (`.builderrules`, `.mdc`, `agents.md`).

**Features:**
- File size analysis (detects rules exceeding recommended limits)
- Frontmatter validation for `.mdc` files
- Quality checks for specificity, conflicts, and verbosity
- Structured review reports with actionable fixes

[View Documentation](./ai-rules-reviewer/SKILL.md)

### agents.md Generator

Generate comprehensive AI instruction files that capture project conventions and coding standards.

**Features:**
- Automatic project analysis (package manager, framework, design system, testing)
- Specialized templates for monorepos and Next.js App Router projects
- Systematic repository scanning to identify conventions
- Validation checklist to ensure accuracy

[View Documentation](./agents-md-generator/SKILL.md)

## Repository Structure

```
builder-skills/
├── SKILL-PATTERNS-REPORT.md           # Authoring guide for creating skills
├── ai-rules-reviewer/                 # Rules file auditing skill
│   ├── SKILL.md                       # Main skill definition
│   ├── common-issues.md               # Diagnostic patterns and fixes
│   ├── file-organization.md           # Restructuring guidance
│   └── assets/
│       ├── examples.md                # Good vs. bad examples
│       └── review-template.md         # Output format template
│
└── agents-md-generator/               # AI instruction file generator
    ├── SKILL.md                       # Main skill definition
    ├── monorepo-template.md           # Turborepo/Nx/pnpm template
    ├── nextjs-app-router-template.md  # Next.js 13+ template
    └── assets/
        └── complete-example.md        # Full reference example
```

## Advanced Skill Patterns

This repository includes a comprehensive authoring guide with advanced patterns for creating effective skills:

- **Progressive Disclosure**: Organize information in tiers (frontmatter → body → references)
- **Template-Driven Approach**: Provide specialized templates for different scenarios
- **Clear Quality Metrics**: Define thresholds and validation criteria
- **Workflow Focus**: Guide users through specific tasks step-by-step

See [SKILL-PATTERNS-REPORT.md](./SKILL-PATTERNS-REPORT.md) for the complete authoring guide.
