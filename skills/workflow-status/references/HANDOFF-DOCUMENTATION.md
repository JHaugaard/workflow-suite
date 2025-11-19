# Skills Workflow Handoff Documentation

## Overview

The skills workflow consists of 4 skills across 4 phases (Phase 0-3), with each phase creating **handoff documents** that serve as session bridges for the next skill. This ensures that decisions and context are preserved across multiple Claude sessions.

## Complete Handoff Chain

```
Phase 0: project-brief-writer
    ↓
    Creates: PROJECT-MODE.md + brief-[name].md
    ↓
Phase 1: tech-stack-advisor (Skills Phase 1 of 3)
    ↓
    Reads: PROJECT-MODE.md + brief file
    Creates: tech-stack-decision.md (after collaborative refinement)
    ↓
Phase 2: deployment-advisor (Skills Phase 2 of 3)
    ↓
    Reads: PROJECT-MODE.md + tech-stack-decision.md
    Creates: deployment-strategy.md (after collaborative refinement)
    ↓
Phase 3: project-spinup (Skills Phase 3 of 3)
    ↓
    Reads: PROJECT-MODE.md + tech-stack-decision.md + deployment-strategy.md
    Creates: [project-dir]/claude.md + complete project foundation
```

## Handoff Documents

### 1. PROJECT-MODE.md (Phase 0)

**Created by:** project-brief-writer (automatically, before gathering project information)

**Location:** Current working directory

**Purpose:** Declares workflow commitment and checkpoint strictness for the entire Skills workflow

**Contains:**

- Workflow mode (LEARNING/BALANCED/DELIVERY)
- Decision date and context
- Mode implications for subsequent skills
- Workflow commitments
- Anti-bypass protections
- Success criteria

**Used by:** All subsequent skills (tech-stack-advisor, deployment-advisor, project-spinup)

**Key Feature:** Governs strategic learning (advisory workflow) - determines how much exploration, discussion, and checkpoint validation happens during tech stack and deployment decisions

---

### 2. Project Brief File (Phase 0)

**Created by:** project-brief-writer

**Location:** User-specified (typically `brief-[project-name].md` in current working directory)

**Purpose:** Problem-focused project requirements without technical implementation details (prevents Over-Specification Problem)

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
- Initial tech thoughts (quarantined in separate section)
- Next steps

**Used by:** tech-stack-advisor (for requirements analysis)

**Key Feature:** Technology-agnostic description focusing on WHAT to build, not HOW. Over-specification detection ensures implementation details are quarantined

---

### 3. tech-stack-decision.md (Phase 1 - Skills Phase 1 of 3)

**Created by:** tech-stack-advisor (AFTER collaborative refinement and user convergence)

**Location:** Current working directory (same as PROJECT-MODE.md)

**Purpose:** Complete technology stack recommendation with detailed rationale serving as session bridge

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

**Key Feature:** Created AFTER collaborative discussion, not before. Includes infrastructure evaluation (Supabase vs PocketBase decision framework, ancillary tools like n8n/Ollama/Wiki.js)

---

### 4. deployment-strategy.md (Phase 2 - Skills Phase 2 of 3)

**Created by:** deployment-advisor (AFTER collaborative refinement and user convergence)

**Location:** Current working directory (same as PROJECT-MODE.md)

**Purpose:** Complete hosting and deployment strategy with concrete workflows serving as session bridge

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

**Key Feature:** Created AFTER collaborative discussion, not before. Evaluates 5 deployment options (Localhost, Hostinger Shared, Cloudflare Pages, Fly.io, VPS with Docker) with honest infrastructure trade-off analysis

---

### 5. claude.md + Project Foundation (Phase 3 - Skills Phase 3 of 3)

**Created by:** project-spinup

**Location:** New project directory (`[project-name]/claude.md`)

**Purpose:** Final handoff document serving as complete project context for all future Claude Code development sessions

**Contains:**

- Developer profile (John's context, experience level, development approach)
- Project overview (description, tech stack, architecture decisions)
- Development environment (setup, tools, workflow)
- Infrastructure & hosting (from deployment-strategy.md, self-hosted tools integration)
- Development workflow (git branching strategy, conventional commits, testing, deployment)
- Code conventions (structure, naming, style)
- Common commands (dev, docker, database)
- Project-specific notes (auth, schema, APIs, environment variables)
- Deployment (workflow from deployment-strategy.md, checklist, rollback)
- Resources & references
- Troubleshooting
- Next steps (Guided Setup prompts OR Quick Start instructions based on spinup_approach)

**Used by:** All future Claude Code sessions working on the project

**Also creates:**

- docker-compose.yml (local development environment)
- Directory structure (src/, tests/, docs/)
- .gitignore (tech-stack-appropriate)
- README.md (setup and development instructions)
- .env.example (required environment variables)
- Git initialization guidance
- *If Quick Start:* Complete project scaffolding with starter code

**Key Feature:** Separates spinup approach (Guided/Quick Start - tactical implementation familiarity) from PROJECT-MODE (strategic learning). Loads ALL handoff documents to preserve complete context chain

---

## Session Bridge Pattern

Each handoff document is designed to be **session-independent**, meaning:

1. **Self-contained**: Contains all context needed to understand the decision
2. **Rationale-rich**: Explains WHY decisions were made, not just WHAT was decided
3. **Action-ready**: Provides concrete next steps and workflows
4. **Cross-session**: Can be loaded in a fresh Claude session months later
5. **Created after convergence**: Documents are written AFTER collaborative refinement with the user, not before

### When Are Handoff Documents Created?

**Critical timing rule:** Handoff files (tech-stack-decision.md, deployment-strategy.md) are created **after the user has discussed and confirmed the primary recommendation** in each skill phase.

**The workflow for advisory skills (Phase 1-2):**
1. Skill presents recommendations in console (primary + alternatives + ruled-out)
2. User asks questions, explores alternatives, discusses trade-offs
3. User confirms choice or converges on final decision
4. **THEN** skill creates handoff document with complete analysis
5. Skill confirms document location and next steps

**Why this timing matters:**
- Ensures document reflects actual decision, not preliminary recommendation
- Captures any modifications made during discussion
- Prevents premature file creation that might need rewriting
- Guarantees accuracy of handoff to next skill

### Example Usage

**Scenario:** User completes Phase 0-1, then starts a new session a week later for Phase 2.

```text
User: "Use deployment-advisor skill"

Claude: [Reads PROJECT-MODE.md] ✓
        [Reads tech-stack-decision.md] ✓
        → Loads complete context (mode, tech decisions, infrastructure)
        → Proceeds with deployment recommendations
        → Collaborative discussion with user
        → Creates deployment-strategy.md
```

**Result:** No manual context re-entry needed. All prior decisions preserved.

**Scenario:** User completes all phases, then starts new session 2 months later to begin development.

```text
User: "I want to start building feature X"

Claude: [Reads project-dir/claude.md] ✓
        → Loads complete project context
        → Understands tech stack, infrastructure, conventions
        → Proceeds with feature implementation
```

**Result:** Claude Code has full context about project decisions, infrastructure, and workflow preferences.

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

```text
project-workspace/
├── PROJECT-MODE.md                    # Phase 0 output (created first)
├── brief-my-project.md                # Phase 0 output
├── tech-stack-decision.md             # Phase 1 output (after collaborative refinement)
├── deployment-strategy.md             # Phase 2 output (after collaborative refinement)
└── my-project/                        # Phase 3 output
    ├── claude.md                      # Comprehensive project context
    ├── docker-compose.yml             # Local development environment
    ├── README.md                      # Setup instructions
    ├── .env.example                   # Environment variables template
    ├── .gitignore                     # Tech-stack-appropriate
    ├── src/                           # Source code
    ├── tests/                         # All tests (unit, integration, e2e)
    └── docs/                          # Documentation
```

**Important:** All handoff documents (PROJECT-MODE.md, brief file, tech-stack-decision.md, deployment-strategy.md) should be in the same directory where you run the skills. The project foundation (phase 3) creates a new subdirectory.

---

## Validation Checklist

To verify your handoff chain is complete at each phase:

**After Phase 0 (project-brief-writer):**

- [ ] PROJECT-MODE.md exists and declares mode (LEARNING/BALANCED/DELIVERY)
- [ ] Project brief file exists (brief-[name].md) and is problem-focused (technology-agnostic)
- [ ] Brief includes quarantined tech thoughts section (if user mentioned technologies)

**After Phase 1 (tech-stack-advisor):**

- [ ] tech-stack-decision.md exists with complete recommendation
- [ ] Includes primary recommendation, alternatives, and ruled-out options
- [ ] Infrastructure evaluation present (Supabase vs PocketBase, ancillary tools)
- [ ] Created AFTER collaborative discussion (not before)

**After Phase 2 (deployment-advisor):**

- [ ] deployment-strategy.md exists with complete strategy
- [ ] Includes deployment workflow, cost breakdown, scaling path
- [ ] Evaluates all 5 deployment options (Localhost, Shared, Pages, Fly.io, VPS)
- [ ] Created AFTER collaborative discussion (not before)

**After Phase 3 (project-spinup):**

- [ ] Project directory exists with claude.md and foundation files
- [ ] docker-compose.yml configured for local development
- [ ] README.md has setup instructions
- [ ] .env.example lists all required environment variables
- [ ] Git initialization guidance provided
- [ ] Next steps clear (Guided Setup prompts OR Quick Start ready to run)

**Note:** If any handoff document is missing, the next skill will ask for that information interactively, but having the documents ensures consistency and preserves context across sessions.

---

## Revising Decisions

If you need to change a decision made in a previous phase, follow these safe revision procedures:

### Revising Tech Stack (After Phase 1)

**Scenario:** You've completed tech-stack-advisor but want to explore a different stack.

**Procedure:**
1. Delete `tech-stack-decision.md` and `deployment-strategy.md` (if it exists)
2. Re-invoke tech-stack-advisor skill
3. Make new tech stack decision
4. Proceed with deployment-advisor (it will read new tech stack decision)

**Why delete deployment-strategy.md?** Deployment strategy depends on tech stack. Deleting ensures consistency.

---

### Revising Deployment Strategy (After Phase 2)

**Scenario:** You've completed deployment-advisor but want to use a different hosting approach.

**Procedure:**
1. Delete `deployment-strategy.md`
2. Re-invoke deployment-advisor skill
3. Make new deployment decision
4. Proceed with project-spinup (it will read new deployment strategy)

**No need to delete tech-stack-decision.md** - tech stack choice is still valid.

---

### Revising Project Mode (Any Phase)

**Scenario:** You started in LEARNING mode but want to switch to BALANCED or DELIVERY.

**Procedure:**
1. Edit `PROJECT-MODE.md` directly
2. Change the mode line: `**Mode:** BALANCED` (or DELIVERY)
3. Update the "Mode Details" section to match
4. Save the file
5. Continue with next skill (will read updated mode)

**Alternative:** Delete PROJECT-MODE.md and re-run project-brief-writer (will recreate with new mode choice).

---

### Revising Project Brief (Phase 0)

**Scenario:** Your project scope or requirements changed.

**Procedure:**
1. Edit the brief file directly (brief-[name].md)
2. Update relevant sections
3. Consider whether tech stack still fits (may need to re-run tech-stack-advisor)
4. Save changes

**Major changes:** If requirements change significantly, consider deleting all handoff documents and restarting from Phase 1 to ensure alignment.

---

### Starting Over Completely

**Scenario:** You want to completely restart the workflow for this project.

**Procedure:**
1. Delete all handoff documents:
   - `PROJECT-MODE.md`
   - `brief-[name].md`
   - `tech-stack-decision.md`
   - `deployment-strategy.md`
2. Re-invoke project-brief-writer skill
3. Proceed through all phases again

**Note:** Project foundation directory (if created) is independent - you can keep or delete it as needed.

---

### Best Practices for Revisions

✅ **Do:**
- Delete downstream handoff documents when revising upstream decisions
- Document why you're revising (in commit message or notes)
- Re-read handoff documents after revision to verify consistency
- Test that next skill reads revised documents correctly

❌ **Don't:**
- Edit handoff documents mid-skill execution
- Leave orphaned handoff documents from old decisions
- Revise without deleting dependent documents (creates inconsistency)
- Skip re-running skills after major changes

---

## Key Concepts

### Strategic Learning vs Tactical Learning

The workflow separates two types of learning:

**Strategic Learning (PROJECT-MODE.md):**
- Governs advisory skills (tech-stack-advisor, deployment-advisor)
- Determines checkpoint strictness and exploration depth
- About understanding trade-offs, evaluating options, making informed decisions
- LEARNING mode = detailed checkpoints, BALANCED mode = moderate checkpoints, DELIVERY mode = minimal checkpoints

**Tactical Learning (spinup_approach in project-spinup):**
- Governs implementation scaffolding style
- Determines whether to build incrementally (Guided Setup) or all at once (Quick Start)
- About familiarity with the chosen tech stack
- Independent of PROJECT-MODE - can be in LEARNING mode but use Quick Start if familiar with stack

### Collaborative Refinement

Handoff documents (tech-stack-decision.md, deployment-strategy.md) are created AFTER collaborative discussion with the user, not before. This ensures:

- User convergence on recommendations
- Opportunity to explore alternatives
- Questions answered before documenting
- Accurate representation of final decision

### Infrastructure as Context, Not Constraint

Skills evaluate self-hosted infrastructure (Supabase, PocketBase, n8n, Ollama, Wiki.js on VPS) as context in trade-off analysis, not as a constraint:

- Marginal cost is $0 if infrastructure already running
- But skills honestly recommend cloud alternatives when genuinely better
- Framework: Discover → Evaluate → Analyze → Recommend → Explain → Empower

---

## Version History

### v2.1 (2025-01-17)
**v1.0-ready refinements based on comprehensive Gemini Pro 2.5 review**

Changes implemented from Gemini review recommendations:
- **Reduced checkpoint questions from 5 to 3** in LEARNING mode (tech-stack-advisor, deployment-advisor)
  - New focused questions validate understanding without creating fatigue
  - 40% reduction in checkpoint burden (10 total → 6 total questions)
- **Added "When Are Handoff Documents Created?" section** with explicit timing rules
  - Clarifies that handoff files created AFTER user confirms recommendation
  - Prevents premature or delayed file creation
- **Added comprehensive "Revising Decisions" section** with safe revision procedures
  - Clear guidance for revising tech stack, deployment strategy, project mode, brief
  - Best practices for maintaining handoff chain consistency
  - Starting over completely procedure documented

**Status:** v1.0-ready - ready for real-world usage and user feedback collection

### v2.0 (2025-01-17)
**Major update aligned with current SKILL.md implementations**

- Updated to reflect Phase 0-3 structure (4 phases total)
- Added "Skills Phase X of 3" labeling for phases 1-3
- Documented handoff document creation timing (AFTER collaborative refinement)
- Added key concepts: Strategic vs Tactical Learning, Collaborative Refinement, Infrastructure as Context
- Expanded validation checklist with phase-specific items
- Updated infrastructure details (VPS8, Caddy, PocketBase, removed Redis/Nginx)
- Added second example usage scenario (development session)
- Clarified spinup approach independence from PROJECT-MODE

### v1.0 (2025-11-12)
**Initial handoff documentation system**

- Established 4-document handoff chain
- Integrated handoff reading into all downstream skills
- Created session bridge pattern for workflow continuity

---

## Related Documentation

- **skills/project-brief-writer/SKILL.md** - How PROJECT-MODE.md is created
- **skills/tech-stack-advisor/SKILL.md** - How tech-stack-decision.md is created
- **skills/deployment-advisor/SKILL.md** - How deployment-strategy.md is created
- **skills/project-spinup/SKILL.md** - How all handoffs are consumed to create claude.md
