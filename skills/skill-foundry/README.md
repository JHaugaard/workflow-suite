# skill-foundry Skill

## Overview

Transform skill ideas into optimized, XML-structured SKILL.md files with minimal README documentation. Acts as a BUILDER with CONSULTANT touch—asks clarifying questions to understand the skill requirements, then generates both files.

**Use when:** You want to create a new Claude Code skill with the optimized XML format.

**Output:** SKILL.md (XML-structured) and README.md (minimal documentation), saved to /Volumes/dev/skill-foundry/work/{skill-name}/

---

## How It Works

When invoked, this skill will:

1. **Gather requirements** - Collect skill purpose, triggers, outputs
2. **Design structure** - Determine phases and identify domain-specific XML tags
3. **Draft SKILL.md** - Generate XML-structured instructions
4. **Draft README.md** - Generate minimal human documentation
5. **Review and refine** - Present drafts, incorporate feedback
6. **Save to foundry** - Save to work/ directory for testing

---

## Workspace Model

```
/Volumes/dev/skill-foundry/          # The foundry (workshop)
├── skill-foundry/                   # This meta-skill
├── work/                            # Active skill development
│   └── {skill-in-progress}/
└── archive/                         # Completed skill snapshots

~/.claude/skills/                    # Production deployment (manual)
```

---

## The XML Format

Skills use XML tags for semantic structure instead of markdown headers:

- **Core tags:** `<purpose>`, `<output>`, `<workflow>`, `<phase>`, `<action>`, `<guardrails>`
- **Domain-specific tags:** Create tags that fit the skill (e.g., `<detection-rules>`, `<template>`, `<examples>`)

This format reduces token usage and improves Claude's parsing reliability.

---

## Version History

### v1.0 (2025-11-19)
**Initial Release**

- XML-structured skill generation
- Flexible tag vocabulary
- Five-phase workflow (gather → design → draft → review → save)
- Minimal README by default
- Work directory staging before production deployment

---

**Version:** 1.0
**Last Updated:** 2025-11-19
