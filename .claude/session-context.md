# Skills Workflow Refinement - Session Context

**Last Updated:** 2025-11-12
**Current Status:** All Phases Complete - Ready for Real-World Testing
**Model Recommendation:** Sonnet 4.5 (claude-sonnet-4-5) for all phases

---

## Executive Summary

Refining 4 Claude Code skills to implement a robust learning-focused project development workflow. This work incorporates critical lessons from the SmugMug after-action report, particularly addressing the "Over-Specification Problem" where overly detailed briefs bypass learning opportunities.

**Multi-session approach:** Deliberately executing phases across multiple sessions to maintain fresh context, avoid token limits, and ensure highest quality refinements.

---

## The 4 Skills Being Refined

### 1. project-brief-writer
**Current Purpose:** Transform rough ideas into problem-focused briefs
**Refinements:** Add PROJECT-MODE.md auto-creation, workflow visibility, version history

### 2. tech-stack-advisor
**Current Purpose:** Strategic technology selection with rationale
**New Name:** Stays as-is
**Refinements:** Advisory guardrails, checkpoint enforcement, brief quality detection, infrastructure integration

### 3. hosting-advisor � deployment-advisor
**Current Purpose:** Recommend hosting/deployment strategy
**New Name:** deployment-advisor (renamed from hosting-advisor)
**Refinements:** Same as tech-stack-advisor (guardrails, checkpoints, infrastructure context)

### 4. project-starter � project-spinup
**Current Purpose:** Generate project foundations with Learning/Quick Start modes
**New Name:** project-spinup (renamed from project-starter)
**Refinements:** Separate spinup mode from PROJECT-MODE, clean handoff protocol, workflow completion

---

## Background Context

### Key Documents Reviewed

1. **after-action-report.md** (2570 lines)
   - SmugMug project bypass issue: Skills saw detailed brief and jumped to implementation
   - Over-Specification Problem: Better briefs = better delivery BUT worse learning
   - Missing checkpoints allowed skills workflow to be skipped
   - Need for PROJECT-MODE.md to declare learning intent

2. **INFRASTRUCTURE_REPO_README.md** (717 lines)
   - Self-hosted stack: Supabase, Ollama, n8n, Redis, Nginx on Hostinger VPS
   - Docker-based deployment, $40-60/month VPS
   - Learning value in infrastructure management

3. **deployment-recap.md** (575 lines)
   - Five deployment options: Localhost, Shared, Pages, Fly.io, VPS
   - Decision framework based on database needs, global distribution, learning goals

4. **lovable-vs-claude-code.md** (870 lines)
   - Phase 0 meta-skills teach strategic thinking
   - Infrastructure-first approach
   - Skills vs implementation workflow distinction
   - Learning > Speed for educational goals

5. **done-vs-next.md** (159 lines)
   - Confirms Phase 0 meta-skills focus (what we're refining)
   - Phase 1 implementation-specific skills come later
   - Current skills should be general-purpose and stack-agnostic

6. **claude-code-agent-skills.md** (607 lines)
   - Official skill documentation
   - YAML frontmatter structure
   - allowed-tools for restricting capabilities
   - Progressive disclosure patterns

### Core Problem: The Over-Specification Problem

**What happened in SmugMug project:**
- User created very detailed brief (29,218 bytes, implementation-ready)
- Invoked tech-stack-advisor skill
- Skill bypassed advisory workflow and started implementing
- Files created 15 minutes after brief
- Learning opportunities lost - no alternatives explored, no trade-offs discussed

**The paradox:**
- Detailed briefs enable fast delivery
- But they bypass the learning workflow the skills are designed to teach
- Need mechanism to preserve learning even when brief is detailed

---

## The 10 Decision Points & User Answers

###  Question 1: Naming
**Decision:**
- `hosting-advisor` � `deployment-advisor` 
- `project-starter` � `project-spinup` 
- `project-brief-writer` stays as-is 
- `tech-stack-advisor` stays as-is 

###  Question 2: PROJECT-MODE.md Creation
**Decision:** Option A - Auto-create by project-brief-writer
- Mandatory file creation (accept cons for learning benefits)
- Forces upfront mode declaration
- Enables other skills to check intent and adjust behavior
- Contains: MODE (LEARNING/DELIVERY/BALANCED), workflow commitments, anti-bypass protections

###  Question 3: Checkpoint Enforcement
**Decision:** Use strictness levels from Summary table with modifications

**Three strictness levels:**
- **LEARNING mode:**
  - Short but complete answers acceptable (not essays)
  - Question-by-question SKIP allowed with acknowledgment
  - NO global bypass - can't skip all checkpoints at once
  - Mode change required for global loosening

- **BALANCED mode:**
  - Self-assessment checklist
  - Simple confirmation to proceed
  - Easy progression with review options

- **DELIVERY mode:**
  - Acknowledgment only
  - Simple yes/no to continue
  - Minimal friction

**Control flow:** PROJECT-MODE.md determines baseline, user has granular skip control

###  Question 4: Brief Quality Detection (Over-Specification Problem)
**Decision:** Detect and redirect WITH human in loop

**Pattern:** Detect � Inform � Discuss � User Decides

**Not:**
- L Passive warning only
- L Automatic redirect without discussion

**Yes:**
-  Active conversation about detection
-  Show what was detected and why it matters
-  Present options (continue/revise/restart/discuss)
-  Explain trade-off in context of MODE
-  Wait for user decision

###  Question 5: Advisory-Only Guardrails
**Decision:** Both technical restriction AND explicit instructions

**Implementation:**
- Use `allowed-tools: [Read, Grep, Glob, WebSearch, Write]` in frontmatter
- Write tool included for documentation creation when user explicitly requests
- Add explicit "Advisory Mode" instructions in skill prompt
- Prevents automatic implementation/code generation
- Can create reference documents when requested

**Applies to:** tech-stack-advisor, deployment-advisor

###  Question 6: BMad Integration
**Decision:** Remove all BMad references and considerations
- Keep workflow endpoint open-ended ("ready for development")
- No assumption about what comes after skills workflow
- Future integration can be addressed later if appropriate

###  Question 7: Self-Hosted Infrastructure Integration
**Decision:** Infrastructure as Trade-Off Context, Not Constraint

**Key principle:** "Self-hosted infrastructure is a valuable asset in the trade-off analysis, not a constraint on recommendations."

**Framework:**
1. Discover what's available (check infrastructure-repo)
2. Evaluate all relevant options (self-hosted + alternatives)
3. Analyze trade-offs WITH infrastructure context
4. Recommend honestly what's best for THIS project
5. Explain how self-hosted fits into decision
6. Trust user to weigh cost vs quality vs learning

**Behavior:**
-  Auto-detect self-hosted infrastructure
-  Consider as valid options in trade-off analysis
-  Do NOT artificially prioritize self-hosted
-  Present honest assessment of best option
-  Factor in marginal cost ($0 for self-hosted)
-  Don't hide superior alternatives
-  Suggest hybrid approaches when applicable

**Examples:**
- Database: If user has Supabase self-hosted and project needs PostgreSQL � recommend self-hosted (genuinely best)
- LLM: If user has Ollama but project needs high-quality content generation � recommend OpenAI API honestly, explain quality gap
- Workflow: If user has n8n self-hosted � recommend it (equivalent to Zapier, $0 cost)

###  Question 8: Learning Mode vs Quick Start Default
**Decision:** Option B - Spinup approach is separate from PROJECT-MODE.md

**Nuance recognized:** Strategic learning ` tactical learning

**Two different modes at play:**
- **PROJECT-MODE.md:** Workflow commitment (LEARNING/DELIVERY/BALANCED) - governs advisory skills
- **Spinup approach:** Scaffolding style (Guided/Quick Start) - depends on stack familiarity

**Implementation:**
- By spinup phase, tech stack and deployment decisions finalized
- Ask at spinup time: "Guided Setup or Quick Start?"
- Provide MODE-informed suggestion (LEARNING � suggest Guided)
- Allow override based on user's familiarity with chosen stack
- Acknowledge that LEARNING mode for strategy ` necessarily learning stack basics

###  Question 9: Scope of Refinements
**Decision:** Tackle ALL improvements (High + Medium + Low) in this session

**Rationale:**
- High Priority: 6-8 hours (essential fixes)
- Medium Priority: +3-4 hours (valuable improvements)
- Low Priority: +1 hour (quick polish)
- Total: 10-13 hours for complete, production-ready skills
- Incremental cost to go from "High only" to "Everything" is only 4-5 hours
- Prevents items from getting lost in shuffle
- Clean slate to move forward

###  Question 10: Testing Strategy
**Decision:** Real-world testing with actual projects

**Approach:**
- Multiple small test projects ready
- Mix of brand new projects (test full workflow)
- Re-do of existing learning project (comparison/validation)
- Learn-by-doing approach
- Iterate and improve based on real usage

---

## Key Principles & Frameworks Established

### Terminology Standards
-  Use "brief" or "project brief" (NOT "PRD")
-  "Over-Specification Problem" (NOT "PRD Quality Paradox")
-  Briefs are problem-focused, not implementation-ready
-  Detection language: "over-specified brief"

### Checkpoint Philosophy
- Checkpoints validate understanding, not just acknowledgment
- Strictness based on declared MODE
- Question-by-question flexibility in LEARNING mode
- Educational feedback, not just gates
- Trust user judgment in BALANCED/DELIVERY modes

### Infrastructure Integration Pattern
```
Self-hosted infrastructure = Context, not Constraint

Process:
1. Discover (what does user have?)
2. Evaluate (all options including self-hosted)
3. Analyze (factor in $0 marginal cost, quality, capabilities)
4. Recommend (honest best choice for THIS project)
5. Explain (how self-hosted fits in)
6. Empower (user decides with full picture)
```

### Advisory vs Implementation Separation
- Advisory skills (tech-stack, deployment) = Consultant role
- Implementation phase (after skills) = Builder role
- Guardrails enforce separation
- Clean handoff between phases

---

## Execution Approach

### Multi-Session Strategy

**Why multi-session:**
- Avoid context window limits
- Prevent unexpected context compacting
- Maintain highest resource levels
- Fresh context each session
- Deliberate, high-quality approach

**Session pattern:**
1. Load session-context.md at start
2. Execute specified phase(s)
3. Update session-context.md and todo.md with progress
4. Review deliverables
5. Move to next session for next phase

**Model:** Sonnet 4.5 (claude-sonnet-4-5) for all phases
- Complexity justifies capable model
- Quality over speed
- Better context management
- Reduced iteration needs

---

## The 6 Refinement Phases

### Phase 1: Naming Updates (30 minutes)
- Rename hosting-advisor � deployment-advisor
- Rename project-starter � project-spinup
- Update all cross-references in SKILL.md files

### Phase 2: project-brief-writer Refinements (2-3 hours)
- HIGH: Add PROJECT-MODE.md auto-creation
- MEDIUM: Add workflow state visibility
- LOW: Version history & documentation

### Phase 3: tech-stack-advisor Refinements (3-4 hours)
- HIGH: Advisory guardrails (allowed-tools + instructions)
- HIGH: Checkpoint enforcement (3 levels)
- HIGH: Brief quality detection (Over-Specification Problem)
- MEDIUM: Self-hosted infrastructure integration
- MEDIUM: Workflow state visibility
- LOW: Documentation & formalization

### Phase 4: deployment-advisor Refinements (2-3 hours)
- HIGH: Advisory guardrails
- HIGH: Checkpoint enforcement
- MEDIUM: Infrastructure integration
- MEDIUM: Workflow state visibility
- LOW: Documentation

### Phase 5: project-spinup Refinements (3-4 hours)
- MEDIUM: Separate spinup mode from PROJECT-MODE
- MEDIUM: Next-step handoff protocol (no BMad references)
- MEDIUM: Workflow state visibility
- LOW: Documentation

### Phase 6: Cross-Cutting Polish (1 hour)
- LOW: Consistent formatting
- LOW: Documentation cross-references
- LOW: Version history standardization

**Total estimated: 11-15 hours across all phases**

---

## Current Status

**Completed:**
-  Requirements gathering and analysis
-  Background document review
-  10 decision points discussed and confirmed
-  Comprehensive refinement plan created
-  Context files created (session-context.md, todo.md)

-  Phase 1: Naming Updates (2025-01-11)
-  Phase 2: project-brief-writer Refinements (2025-01-11)
-  Phase 3: tech-stack-advisor Refinements (2025-11-11)
-  Phase 4: deployment-advisor Refinements (2025-11-11)
-  Phase 5: project-spinup Refinements (2025-11-12)
-  Phase 6: Cross-Cutting Polish (2025-11-12)

**Phase 6 Accomplishments:**
- Standardized "Further Reading" section structure across all 4 skills
- Unified version history format with consistent structure (version, date, description, bullets)
- Enhanced cross-references with Background Documentation, Related Skills, and Workflow Integration subsections
- Changed "Cross-References & Further Reading" to "Further Reading" in project-brief-writer
- Ensured consistent formatting conventions throughout all skills
- All 4 skills now have cohesive documentation style

**Project Status:**
-  ALL 6 PHASES COMPLETE
-  All refinements implemented successfully
-  Skills ready for real-world testing with actual projects

**Files Ready:**
- 4 refined skills in `/home/user/skill-builder/`
- Background docs in `/home/user/skill-builder/background-docs/`
- Context files in `/home/user/skill-builder/.claude/`

---

## Usage Instructions for Future Sessions

### Starting a New Session

1. **Load this context:**
   ```
   Read .claude/session-context.md
   Read .claude/todo.md
   ```

2. **Specify which phase(s) to execute:**
   - "Execute Phase 1"
   - "Execute Phases 2 and 3"
   - "Continue with next incomplete phase"

3. **Model selection:**
   - Use Sonnet 4.5 (claude-sonnet-4-5) for all phases

### After Phase Completion

1. **Update todo.md:**
   - Check off completed tasks
   - Add any notes or observations
   - Update phase status

2. **Update session-context.md:**
   - Update "Current Status" section
   - Add "Completed" section entry with date and deliverables
   - Note any issues or deviations from plan

3. **Review deliverables:**
   - Verify refinements match decisions
   - Test if applicable
   - Confirm ready for next phase

---

## Reference: Brief Detail Level

Briefs should contain:
-  Problem statement (what needs solving)
-  User/audience description
-  Success criteria (how we know it works)
-  Constraints (budget, timeline, must-haves)
-  Context (why now, why this matters)
- L Specific technologies (unless constraint)
- L Implementation details
- L Functional requirement lists
- L Technical architecture

---

## Contact & Questions

If questions arise during execution:
- Refer to this context document first
- Check background docs for additional context
- When in doubt, ask user for clarification
- Preserve the 10 decision points as authoritative

---

**End of Session Context Document**
