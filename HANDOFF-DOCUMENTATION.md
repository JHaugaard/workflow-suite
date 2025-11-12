# Skills Workflow Handoff Documentation

## Overview

The skills workflow consists of 4 skills across 3 phases, with each phase creating **handoff documents** that serve as session bridges for the next skill. This ensures that decisions and context are preserved across multiple Claude sessions.

## Complete Handoff Chain

```
Phase 0: project-brief-writer
    ↓
    Creates: PROJECT-MODE.md + brief.md
    ↓
Phase 1: tech-stack-advisor
    ↓
    Reads: PROJECT-MODE.md + brief.md
    Creates: tech-stack-decision.md
    ↓
Phase 2: deployment-advisor
    ↓
    Reads: PROJECT-MODE.md + tech-stack-decision.md
    Creates: deployment-strategy.md
    ↓
Phase 3: project-spinup
    ↓
    Reads: PROJECT-MODE.md + tech-stack-decision.md + deployment-strategy.md
    Creates: [project-dir]/claude.md + complete project foundation
```

## Handoff Documents

### 1. PROJECT-MODE.md (Phase 0)

**Created by:** project-brief-writer
**Location:** Current working directory
**Purpose:** Declares workflow commitment and checkpoint strictness

**Contains:**
- Workflow mode (LEARNING/BALANCED/DELIVERY)
- Decision date and context
- Mode implications for subsequent skills
- Workflow commitments
- Anti-bypass protections
- Success criteria

**Used by:** All subsequent skills (tech-stack-advisor, deployment-advisor, project-spinup)

---

### 2. Project Brief File (Phase 0)

**Created by:** project-brief-writer
**Location:** User-specified (typically `brief-[name].md`)
**Purpose:** Problem-focused project requirements without technical implementation details

**Contains:**
- Project overview (narrative)
- Problem statement
- Project goals (primary + secondary)
- Functional requirements
- Success criteria
- Use cases & scenarios
- Edge cases to handle
- Constraints & assumptions
- Out of scope items
- Learning goals (optional)
- Initial tech thoughts (quarantined)
- Next steps

**Used by:** tech-stack-advisor (for requirements analysis)

---

### 3. tech-stack-decision.md (Phase 1)

**Created by:** tech-stack-advisor
**Location:** Current working directory (same as PROJECT-MODE.md)
**Purpose:** Complete technology stack recommendation with detailed rationale

**Contains:**
- Primary tech stack recommendation with rationale
  - Why it fits the project
  - Why it fits learning goals
  - Why it fits available infrastructure
- Complete tech stack breakdown
  - Frontend, backend, database, auth, styling, file storage, deployment, testing
- Learning opportunities (frontend, backend, architecture, transferable skills)
- Cost analysis (setup + monthly costs)
- Alternative options (2-3 alternatives with trade-offs)
- Ruled-out options (with explanations)
- Next steps

**Used by:** deployment-advisor (to understand tech requirements), project-spinup (for scaffolding)

**Format:** Uses structured markdown with clear sections matching the output template in tech-stack-advisor/SKILL.md

---

### 4. deployment-strategy.md (Phase 2)

**Created by:** deployment-advisor
**Location:** Current working directory (same as PROJECT-MODE.md)
**Purpose:** Complete hosting and deployment strategy with concrete workflows

**Contains:**
- Primary hosting recommendation
  - Why it fits the project
  - Why it fits the infrastructure
- Hosting details (provider, server type, container approach, database, file storage, CDN, SSL, DNS)
- Deployment workflow
  - Initial setup (one-time steps)
  - Regular deployment (for updates)
  - Rollback procedure
- Cost breakdown (setup + monthly + scaling costs)
- Scaling path (multiple phases with triggers)
- Alternative hosting options (with trade-offs)
- Monitoring & maintenance (setup, schedule, tools)
- Backup strategy (database, files, configuration, verification, disaster recovery)
- Security considerations
- Next steps

**Used by:** project-spinup (for deployment integration in claude.md and README)

**Format:** Uses structured markdown with clear sections matching the output template in deployment-advisor/SKILL.md

---

### 5. claude.md + Project Foundation (Phase 3)

**Created by:** project-spinup
**Location:** New project directory
**Purpose:** Final handoff document serving as complete project context for future development sessions

**Contains:**
- Developer profile (John's context)
- Project overview (description, tech stack, architecture decisions)
- Development environment (setup, tools, workflow)
- Infrastructure & hosting (from deployment-strategy.md)
- Development workflow (git, commits, testing, deployment)
- Code conventions (structure, naming, style)
- Common commands (dev, docker, database)
- Project-specific notes (auth, schema, APIs)
- Deployment (workflow from deployment-strategy.md, checklist, rollback)
- Resources & references
- Troubleshooting
- Next steps (Guided Setup prompts OR Quick Start instructions)

**Used by:** All future Claude Code sessions working on the project

**Also creates:** docker-compose.yml, directory structure, .gitignore, README.md, .env.example, initial git setup

---

## Session Bridge Pattern

Each handoff document is designed to be **session-independent**, meaning:

1. **Self-contained**: Contains all context needed to understand the decision
2. **Rationale-rich**: Explains WHY decisions were made, not just WHAT was decided
3. **Action-ready**: Provides concrete next steps and workflows
4. **Cross-session**: Can be loaded in a fresh Claude session months later

### Example Usage

**Scenario:** User completes Phase 0-1, then starts a new session a week later for Phase 2.

```
User: "Use deployment-advisor skill"

Claude: [Reads PROJECT-MODE.md] ✓
        [Reads tech-stack-decision.md] ✓
        → Loads complete context
        → Proceeds with deployment recommendations
        → Creates deployment-strategy.md
```

**Result:** No manual context re-entry needed. All prior decisions preserved.

---

## Benefits of Handoff Documents

### 1. Session Continuity
- Work on the workflow across multiple days/weeks
- No need to manually re-explain decisions
- Context preserved accurately

### 2. Decision Record
- Complete audit trail of project decisions
- Rationale documented for future reference
- Can revisit choices later if project evolves

### 3. Consistency
- All skills reference same source of truth
- Prevents contradictions between phases
- Ensures coherent final project foundation

### 4. Flexibility
- Can switch between LEARNING/BALANCED/DELIVERY modes
- Can explore alternatives without losing primary recommendation
- Can pause/resume workflow at any phase

### 5. Learning Artifact
- Documents serve as learning resources
- Can review trade-offs and alternatives
- Understand WHY decisions were made

---

## File Organization

Typical workflow directory structure:

```
project-workspace/
├── PROJECT-MODE.md                    # Phase 0 output
├── brief-my-project.md                # Phase 0 output
├── tech-stack-decision.md             # Phase 1 output
├── deployment-strategy.md             # Phase 2 output
└── my-project/                        # Phase 3 output
    ├── claude.md                      # Comprehensive project context
    ├── docker-compose.yml
    ├── README.md
    ├── .env.example
    ├── .gitignore
    ├── src/
    ├── tests/
    └── docs/
```

**Important:** All handoff documents (PROJECT-MODE.md, tech-stack-decision.md, deployment-strategy.md) should be in the same directory where you run the skills.

---

## Validation

To verify your handoff chain is complete, check:

- [ ] PROJECT-MODE.md exists and declares mode
- [ ] Project brief exists and is problem-focused
- [ ] tech-stack-decision.md exists with complete recommendation
- [ ] deployment-strategy.md exists with complete strategy
- [ ] Project directory exists with claude.md and foundation files

If any handoff document is missing, the next skill will ask for that information interactively, but having the documents ensures consistency.

---

## Version History

### v1.0 (2025-11-12)
**Initial handoff documentation system**

- Established 4-document handoff chain
- Integrated handoff reading into all downstream skills
- Created session bridge pattern for workflow continuity

---

## Related Documentation

- **project-brief-writer/SKILL.md** - How PROJECT-MODE.md is created
- **tech-stack-advisor/SKILL.md** - How tech-stack-decision.md is created
- **deployment-advisor/SKILL.md** - How deployment-strategy.md is created
- **project-spinup/SKILL.md** - How all handoffs are consumed to create claude.md
