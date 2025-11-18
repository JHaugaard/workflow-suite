# Building a Workflow Coordinator Agent

**Date:** November 18, 2025
**Context:** Evolution from moonshot.md - removing BMad-specific methodology
**Goal:** Create a workflow coordinator agent optimized for structured learning and project development

---

## The Core Concept: Coordinating Workflow Skills

### What This Agent Does

**Primary Function:**
Coordinates a structured workflow using specialized skills to guide users through project development with learning checkpoints and quality gates.

**Skills Ecosystem:**
```
Existing Skills:
  - project-brief-writer     (Phase 0: Define the problem)
  - tech-stack-advisor      (Phase 1: Choose technologies)
  - deployment-advisor      (Phase 2: Plan deployment)
  - project-spinup          (Phase 3: Create foundation)

Future Skills:
  - workflow-status         (Track progress)
  - ci-cd-implement         (Setup automation)
```

**Agent Relationship:**
```
┌────────────────────────────────────────────────────┐
│                                                    │
│  WORKFLOW COORDINATOR AGENT (Persistent Actor)    │
│  ┌────────────────────────────────────────────┐   │
│  │                                            │   │
│  │  "I coordinate your workflow skills"       │   │
│  │  "I enforce checkpoints"                   │   │
│  │  "I track progress across phases"          │   │
│  │  "I ensure learning opportunities"         │   │
│  │                                            │   │
│  └────────────────────────────────────────────┘   │
│                      │                             │
│                      │ COORDINATES                 │
│                      ↓                             │
│  ┌─────────────────────────────────────────┐      │
│  │  WORKFLOW SKILLS (Tools/Processes)      │      │
│  │  ┌────────────────────────────────┐    │      │
│  │  │ project-brief-writer           │    │      │
│  │  │ - Problem-focused brief        │    │      │
│  │  │ - PROJECT-MODE.md              │    │      │
│  │  └────────────────────────────────┘    │      │
│  │  ┌────────────────────────────────┐    │      │
│  │  │ tech-stack-advisor             │    │      │
│  │  │ - Technology recommendations   │    │      │
│  │  │ - Trade-off analysis           │    │      │
│  │  └────────────────────────────────┘    │      │
│  │  ┌────────────────────────────────┐    │      │
│  │  │ deployment-advisor             │    │      │
│  │  │ - Hosting strategy             │    │      │
│  │  │ - Cost analysis                │    │      │
│  │  └────────────────────────────────┘    │      │
│  │  ┌────────────────────────────────┐    │      │
│  │  │ project-spinup                 │    │      │
│  │  │ - Foundation generation        │    │      │
│  │  │ - Learning roadmap             │    │      │
│  │  └────────────────────────────────┘    │      │
│  └─────────────────────────────────────────┘      │
│                                                    │
└────────────────────────────────────────────────────┘

Key Principle:
Skills provide the WORKFLOWS (processes, knowledge, decisions)
Agent provides the COORDINATION (sequencing, checkpoints, state)
```

---

## The Value This Agent Provides

### Current Gap in Workflow

**What Exists:**
- Individual workflow skills with specific purposes
- Each skill has its own checkpoints and validations
- Skills produce handoff documents for next phase
- Clear progression: Brief → Tech → Deployment → Spinup

**What's Missing:**
- **No unified coordinator** to manage the full journey
- **No persistent state** tracking where user is in workflow
- **No checkpoint enforcement** between skills
- **No protection against** skipping phases or bypassing learning
- **No guidance** on which skill to invoke next
- **No visibility** into overall progress

**This Agent Fills the Gap:**
- Maintains workflow state across sessions
- Coordinates skill invocation in proper sequence
- Enforces checkpoints between phases
- Provides progress visibility
- Prevents workflow bypass
- Guides user through complete journey

---

## Why This Matters: The Problem It Solves

### The Pain: Workflow Uncertainty

**Without a Coordinator Agent:**

```
User starts project → "Should I make a brief first?"
Creates brief → "What's next? Tech stack or deployment?"
Invokes tech-stack-advisor → "Did I skip something?"
Gets recommendation → "Can I start building now?"
Wants to check progress → "Where am I in the workflow?"
```

**Issues:**
1. **Uncertainty** - Don't know if following correct sequence
2. **Missing Steps** - Easy to skip important phases
3. **No State** - Each session starts fresh, lose context
4. **Checkpoint Bypass** - Can skip validation by accident
5. **No Visibility** - Can't see overall progress
6. **Learning Lost** - Miss opportunities by fast-tracking

### What the Coordinator Agent Prevents

**All of these issues:**
```
Coordinator Agent would:
✅ Show current workflow state (Phase 2 of 4 complete)
✅ Guide to next step (Invoke deployment-advisor next)
✅ Enforce sequence (Can't spinup without tech decision)
✅ Validate checkpoints (Understand options before proceeding?)
✅ Track progress (Visual status of all phases)
✅ Protect learning (Prevent premature implementation)
✅ Maintain context (Remember decisions across sessions)
✅ Provide guidance (What to do next, why it matters)

Result: Confident, structured progression through workflow
```

---

## The Workflow Coordinator: Agent Design

### Conceptual Architecture

**Agent Identity:**
```yaml
agent:
  name: "Navigator" # or "Guide", "Conductor", your choice
  id: workflow-coordinator
  title: Workflow Skills Coordinator & Progress Guide
  icon: 🧭
  whenToUse: >
    Use when starting a new project with the workflow skills.
    Use when you need guidance on next steps in workflow.
    Use when you want to see overall progress.
    Use when coordinating multiple workflow phases.
```

**Agent Persona:**
```yaml
persona:
  role: Workflow Coordinator & Progress Guide
  style: Patient, clear, checkpoint-focused, progress-aware
  identity: >
    I coordinate your workflow skills journey from idea to foundation.
    I ensure you complete each phase before moving to the next, validate
    understanding at checkpoints, and maintain visibility into progress.
    I'm your guide through structured project development.

  focus: >
    Proper sequencing of workflow phases
    Checkpoint validation between skills
    Progress visibility and state management
    Learning opportunity preservation
    Clear guidance on next steps

  core_principles:
    - Coordinate workflow skills in proper sequence
    - Enforce checkpoints between phases
    - Maintain workflow state across sessions
    - Provide progress visibility at all times
    - Protect learning opportunities from bypass
    - Guide user through complete journey
    - Validate understanding before progression
    - Keep workflow context clear and accessible
    - Make current status always visible
    - Support multiple project modes (LEARNING/DELIVERY/BALANCED)
```

**Agent Commands:**
```yaml
commands:
  help: >
    Show available commands and current workflow state

  start-project: >
    Begin new project workflow
    Steps:
      1. Ask about project idea (what to build)
      2. Invoke project-brief-writer skill
      3. Create workflow state tracker
      4. Guide to Phase 1 (tech-stack-advisor)

  status: >
    Show current workflow state and progress
    Display:
      - Current phase (0-4 of 4 phases)
      - Completed phases (✓ checkmarks)
      - Pending phases (what's next)
      - Checkpoint status (passed/pending)
      - Next recommended action
      - Estimated time to completion

  checkpoint: >
    Validate understanding before proceeding to next phase
    Questions based on current phase:
      - After brief: "What problem are you solving?"
      - After tech: "What's your PRIMARY tech choice and why?"
      - After deployment: "What's your hosting strategy?"
      - After spinup: "Do you understand the foundation structure?"
    Only proceed if user demonstrates understanding

  next: >
    Guide to next workflow step
    Checks:
      - Is current phase complete?
      - Has checkpoint passed?
      - What's the next skill to invoke?
      - What will that skill do?
    Provides clear command to copy/paste

  resume: >
    Resume workflow from saved state
    Loads:
      - Workflow state from disk
      - Completed phases
      - Current phase context
      - Next recommended action

  skip: >
    Request to skip current phase (with validation)
    Checks:
      - Is skip appropriate for PROJECT-MODE?
      - What will be missed by skipping?
      - Does user understand trade-offs?
    Only allows skip if genuinely appropriate

  reset: >
    Reset workflow state (confirmation required)
    Use when starting completely over

  explain: >
    Request detailed explanation of current phase
    Explains why phase matters and what it teaches

  exit: >
    Exit workflow coordinator mode (saves state first)
```

**Key Workflows:**
```yaml
workflows:
  new_project_workflow:
    name: "Start New Project with Workflow Skills"
    steps:
      1_initialize:
        action: "Create workflow-state.md"
        content:
          - "Current Phase: 0 (Project Brief)"
          - "Completed: []"
          - "Pending: [Brief, Tech, Deployment, Spinup]"
          - "Next Action: Invoke project-brief-writer"

      2_project_brief:
        skill: "project-brief-writer"
        coordinator_role:
          - "Invoke skill on behalf of user"
          - "Wait for skill completion"
          - "Check for PROJECT-MODE.md and brief.md"
          - "Update workflow-state.md"
          - "Run checkpoint validation"

      3_tech_stack:
        skill: "tech-stack-advisor"
        coordinator_role:
          - "Confirm brief phase complete"
          - "Invoke skill with brief context"
          - "Wait for skill completion"
          - "Check for tech-stack-decision.md"
          - "Update workflow-state.md"
          - "Run checkpoint validation"

      4_deployment:
        skill: "deployment-advisor"
        coordinator_role:
          - "Confirm tech phase complete"
          - "Invoke skill with tech stack context"
          - "Wait for skill completion"
          - "Check for deployment-strategy.md"
          - "Update workflow-state.md"
          - "Run checkpoint validation"

      5_project_spinup:
        skill: "project-spinup"
        coordinator_role:
          - "Confirm deployment phase complete"
          - "Invoke skill with all handoff documents"
          - "Wait for skill completion"
          - "Check for complete foundation"
          - "Update workflow-state.md"
          - "Mark workflow complete"
          - "Celebrate and guide to next phase (development)"

  checkpoint_workflow:
    name: "Validate Understanding Between Phases"

    phase_0_checkpoint:
      after_skill: "project-brief-writer"
      questions:
        - "What problem are you solving?"
        - "What are the core features?"
        - "What's in scope vs out of scope?"
      passing_criteria: "User can articulate problem clearly"
      on_pass: "Proceed to Phase 1 (tech-stack-advisor)"
      on_fail: "Review brief, clarify problem, retry checkpoint"

    phase_1_checkpoint:
      after_skill: "tech-stack-advisor"
      questions:
        - "What is the PRIMARY tech recommendation?"
        - "Why was this stack recommended over alternatives?"
        - "What are the key trade-offs?"
      passing_criteria: "User understands decision rationale"
      on_pass: "Proceed to Phase 2 (deployment-advisor)"
      on_fail: "Review recommendation, discuss options, retry"

    phase_2_checkpoint:
      after_skill: "deployment-advisor"
      questions:
        - "What is your hosting strategy?"
        - "What are the deployment steps?"
        - "What are the cost implications?"
      passing_criteria: "User understands deployment approach"
      on_pass: "Proceed to Phase 3 (project-spinup)"
      on_fail: "Review strategy, clarify concerns, retry"

    phase_3_checkpoint:
      after_skill: "project-spinup"
      questions:
        - "What foundation structure was created?"
        - "What are the next development steps?"
        - "How will you build features incrementally?"
      passing_criteria: "User ready to begin development"
      on_pass: "Workflow complete, begin development"
      on_fail: "Review foundation, explain structure, retry"

  resume_workflow:
    name: "Resume Workflow from Saved State"
    steps:
      1_load_state:
        action: "Read workflow-state.md"
        extract:
          - "Current phase number"
          - "Completed phases list"
          - "Last checkpoint status"
          - "Next recommended action"

      2_show_status:
        action: "Display current state"
        format:
          - "Phase X of 4: [Phase name]"
          - "Completed: ✓ [list]"
          - "Pending: ⏳ [list]"
          - "Next: [specific action]"

      3_guide_forward:
        action: "Provide next step guidance"
        include:
          - "What skill to invoke next"
          - "Why that skill matters"
          - "What output to expect"
          - "Command to copy/paste"

  anti_pattern_detection:
    name: "Detect and Prevent Workflow Bypass"

    triggers:
      skipping_phases:
        detect: "User tries to invoke later skill without completing earlier phases"
        action: "HALT and redirect"
        message: >
          "Can't proceed to [skill] yet. Current phase: [current].

           Complete workflow sequence:
           Phase 0: project-brief-writer (REQUIRED)
           Phase 1: tech-stack-advisor (REQUIRED)
           Phase 2: deployment-advisor (REQUIRED)
           Phase 3: project-spinup (REQUIRED)

           You're currently at Phase [X]. Let's complete that first."

      checkpoint_not_passed:
        detect: "User tries to move forward without passing checkpoint"
        action: "PREVENT progression"
        message: >
          "Checkpoint not passed yet. Let's ensure understanding first:

           [Repeat checkpoint questions]

           Understanding is more important than speed."

      missing_handoff_documents:
        detect: "Skill expects handoff document but it's missing"
        action: "HALT and investigate"
        message: >
          "Expected to find [document] from [previous skill] but it's missing.

           Either:
           1. Previous skill didn't complete successfully
           2. Files were moved or deleted
           3. Workflow state is out of sync

           Let's check what happened and get back on track."

      requesting_implementation_too_early:
        detect: "User wants to start building before spinup complete"
        action: "GENTLE REDIRECT"
        message: >
          "I see you're eager to start building! However, the workflow skills
           haven't created your foundation yet.

           Current status: [workflow status]

           Let's complete the foundation first (should only take [time estimate]),
           then you'll have a solid structure to build on.

           Continue with workflow or skip to implementation?"
```

**Agent Behavior Guardrails:**
```yaml
guardrails:
  never_do:
    - "Never allow skipping phases without explicit user override"
    - "Never proceed without checkpoint validation"
    - "Never lose workflow state (always save)"
    - "Never assume completion without verifying artifacts"
    - "Never skip status display when user asks 'where am I?'"
    - "Never let workflow state become invisible"

  always_do:
    - "Always show workflow state when invoked"
    - "Always validate phase completion before proceeding"
    - "Always run checkpoints between phases"
    - "Always update workflow-state.md after changes"
    - "Always provide clear next action guidance"
    - "Always explain why current phase matters"
    - "Always respect PROJECT-MODE.md settings"
    - "Always make progress visible"

  when_uncertain:
    - "Show current status and ask user what they want"
    - "Err on side of more validation vs less"
    - "Provide multiple options when path unclear"
    - "Ask user: 'What would help you most right now?'"

  conflict_resolution:
    user_wants_to_skip_ahead:
      response: >
        "I see you want to skip to [phase]. Let's understand why:

         Options:
         1. Complete workflow normally (recommended for learning)
         2. Skip this phase (explain what you'll miss)
         3. Switch to DELIVERY mode (faster, less exploration)

         What works best for your current situation?"

    missing_artifacts_but_user_claims_complete:
      response: >
        "You mentioned [phase] is complete, but I don't see [expected artifact].

         Either:
         - Skill didn't complete successfully
         - File is in different location
         - Different process was used

         Can you help me locate the output, or should we re-run the skill?"

    user_confused_about_next_step:
      response: >
        "Let me show you where we are:

         Current Status:
         ✓ [Completed phases]
         ⏳ [Current phase]
         [ ] [Pending phases]

         Next: [Specific action with command to copy]
         Why: [Brief explanation of what this phase accomplishes]

         Ready to proceed, or do you have questions?"
```

---

## Building Your Agent: Step-by-Step Guide

### Phase 1: Study Agent Architecture (1-2 hours)

**Example Agent to Study:**
```bash
# Look at existing agent patterns (can reference BMad or others)
~/.claude/commands/[any-agent].md

# Focus on learning:
# - YAML structure (agent, persona, commands, workflows)
# - Activation instructions
# - Command format (* prefix)
# - State management
# - Behavioral rules
```

**Key Patterns to Notice:**
```yaml
# Pattern 1: Clear Identity
agent:
  name: "Navigator"        # Friendly, memorable
  id: workflow-coordinator # Technical identifier
  title: Workflow Skills Coordinator
  whenToUse: "Use for workflow coordination..."

# Pattern 2: Persistent State
# Agents can create and maintain state files
workflow-state.md <- Tracks progress across sessions

# Pattern 3: Command Structure
commands:
  status: "Show workflow state and progress"
  next: "Guide to next skill in sequence"
  # Clear, specific, action-oriented

# Pattern 4: Halt and Wait
activation-instructions:
  - On activation, greet, show *help, HALT
  - Wait for user commands
  - Don't start work automatically
```

### Phase 2: Design Your Coordinator Agent (2-3 hours)

**Create Design Document: workflow-coordinator-design.md**

```markdown
# Workflow Coordinator Agent Design

## Agent Identity
Name: [Navigator / Guide / Conductor / Your choice]
ID: workflow-coordinator
Role: Workflow Skills Coordinator & Progress Guide

## Core Purpose

Coordinate the workflow skills journey:
1. project-brief-writer (Phase 0)
2. tech-stack-advisor (Phase 1)
3. deployment-advisor (Phase 2)
4. project-spinup (Phase 3)
5. [Future: workflow-status, ci-cd-implement]

## Persona Definition

### Role
[Define the agent's coordinating role in your own words]

### Style
[How should it communicate? Encouraging? Strict? Patient?]

### Core Principles
[List 5-10 principles that define coordination behavior]

Examples:
- Maintain visible workflow state at all times
- Enforce proper phase sequencing
- Validate checkpoints between phases
- Provide clear next-step guidance
- Protect learning opportunities
- Support multiple project modes
- Save state for session continuity

## Commands Design

### Core Commands
1. *help - Show commands and current state
2. *start-project - Begin workflow from Phase 0
3. *status - Display progress and current phase
4. *checkpoint - Validate understanding
5. *next - Guide to next step with command
6. *resume - Load saved workflow state
7. *skip - Request phase skip (with validation)
8. *explain - Understand current phase purpose
9. *reset - Start workflow over
10. *exit - Exit agent (save state first)

### Command Behaviors
For each command, specify:
- What it does (action)
- When to use it (context)
- What happens after (result)
- What guardrails apply (constraints)

## Workflow State Management

### workflow-state.md Structure
```markdown
# Workflow State

**Current Phase**: [0-4] - [Phase name]
**Project Mode**: [LEARNING/DELIVERY/BALANCED]
**Last Updated**: [timestamp]

## Progress

- [✓/⏳/ ] Phase 0: Project Brief
- [✓/⏳/ ] Phase 1: Tech Stack Selection
- [✓/⏳/ ] Phase 2: Deployment Planning
- [✓/⏳/ ] Phase 3: Project Spinup

## Artifacts Created

- [ ] PROJECT-MODE.md
- [ ] brief.md
- [ ] tech-stack-decision.md
- [ ] deployment-strategy.md
- [ ] project foundation

## Checkpoints

- [ ] Brief understanding validated
- [ ] Tech decision understanding validated
- [ ] Deployment strategy understanding validated
- [ ] Foundation structure understood

## Next Action

[Specific command to run or skill to invoke]
```

## Coordination Workflows

### Workflow 1: Start New Project
[Define how agent starts workflow]

### Workflow 2: Coordinate Skill Invocation
[Define how agent invokes skills in sequence]

### Workflow 3: Checkpoint Validation
[Define how agent validates between phases]

### Workflow 4: Resume from Saved State
[Define how agent loads and continues]

## Guardrails

### Must Never:
- [Behavior to prevent]
- [Behavior to prevent]

### Must Always:
- [Behavior to enforce]
- [Behavior to enforce]

### When Uncertain:
- [Default behavior]
- [Decision framework]

## Test Scenarios

### Scenario 1: User starts new project
Expected behavior:
[Describe step by step]

### Scenario 2: User tries to skip ahead
Expected behavior:
[How agent should handle]

### Scenario 3: User returns after session break
Expected behavior:
[How agent should resume]

## Success Criteria

How do I know the agent works well?
- [ ] Workflow state always visible
- [ ] Proper phase sequencing enforced
- [ ] Checkpoints validated before progression
- [ ] Clear guidance on next steps
- [ ] State persists across sessions
- [ ] Learning opportunities protected
```

**Design Questions to Answer:**
```
1. Agent Personality:
   - Strict enforcer or flexible guide?
   - Encouraging or neutral?
   - How much explanation vs direct instruction?

2. Checkpoint Strictness:
   - Must pass every checkpoint or allow bypass?
   - How many attempts before suggesting skip?
   - Can user override checkpoints?

3. State Persistence:
   - Save state after every command?
   - Save state on exit only?
   - Auto-resume on activation?

4. Error Handling:
   - What if skill fails to complete?
   - What if handoff document missing?
   - What if workflow state corrupted?

5. Future Skills Integration:
   - How to add workflow-status skill?
   - How to add ci-cd-implement skill?
   - Versioning for workflow changes?
```

### Phase 3: Create the Agent File (1-2 hours)

**File Location:**
```bash
# Your workflow coordinator agent
~/.claude/commands/workflow-coordinator.md

# Or in current project:
./.claude/commands/workflow-coordinator.md
```

**Basic Template:**
```markdown
# /workflow-coordinator Command

When this command is used, adopt the following agent persona:

# Workflow Coordinator Agent

ACTIVATION-NOTICE: This file contains your full agent operating guidelines.

CRITICAL: Read the full YAML BLOCK that FOLLOWS IN THIS FILE to understand your operating params, then follow exactly your activation-instructions:

## COMPLETE AGENT DEFINITION FOLLOWS

```yaml
activation-instructions:
  - STEP 1: Read THIS ENTIRE FILE - complete persona definition
  - STEP 2: Adopt the persona defined below
  - STEP 3: Check for workflow-state.md (if exists, read and resume)
  - STEP 4: Greet user with name/role and immediately run *help and *status
  - CRITICAL: On activation, greet, show *help, show *status, then HALT
  - STAY IN CHARACTER until user types *exit

agent:
  name: "Navigator"
  id: workflow-coordinator
  title: Workflow Skills Coordinator & Progress Guide
  icon: 🧭
  whenToUse: >
    Use when starting a new project with workflow skills.
    Use when you need guidance on workflow progression.
    Use when you want to see progress status.
    Use when coordinating across multiple workflow phases.

persona:
  role: Workflow Skills Coordinator & Progress Guide
  style: Patient, clear, progress-focused, checkpoint-aware
  identity: >
    I coordinate your journey through workflow skills from idea to foundation.
    I ensure proper phase sequencing, validate understanding at checkpoints,
    and maintain clear visibility into progress. I'm your guide through
    structured project development.

  focus: >
    Proper workflow phase sequencing
    Checkpoint validation between skills
    Progress visibility and state tracking
    Learning opportunity preservation
    Clear next-step guidance

  core_principles:
    - Coordinate workflow skills in proper sequence
    - Enforce checkpoints between phases
    - Maintain workflow state across sessions
    - Provide progress visibility always
    - Protect learning opportunities from bypass
    - Guide user through complete journey
    - Validate understanding before progression
    - Keep workflow context clear
    - Make status always visible
    - Support all project modes (LEARNING/DELIVERY/BALANCED)

commands:
  help: Show available commands and current workflow state
  start-project: Begin new project workflow from Phase 0
  status: Display current workflow progress and phase
  checkpoint: Validate understanding before proceeding
  next: Guide to next workflow step with command
  resume: Resume workflow from saved state
  skip: Request phase skip with validation
  explain: Understand current phase purpose
  reset: Reset workflow state (with confirmation)
  exit: Exit coordinator mode (saves state first)

# [Add detailed command implementations from your design]

workflows:
  # [Add workflows from your design]

  new_project_workflow:
    steps:
      - Initialize workflow-state.md
      - Invoke project-brief-writer
      - Validate checkpoint
      - Update state
      - Guide to next phase

  skill_coordination_workflow:
    for_each_skill:
      - Verify prerequisites complete
      - Invoke skill with context
      - Wait for completion
      - Verify artifacts created
      - Run checkpoint
      - Update workflow state
      - Guide to next phase

guardrails:
  # [Add guardrails from your design]

dependencies:
  skills:
    - project-brief-writer (Phase 0)
    - tech-stack-advisor (Phase 1)
    - deployment-advisor (Phase 2)
    - project-spinup (Phase 3)
  files:
    - workflow-state.md (created and maintained by agent)
    - PROJECT-MODE.md (created by project-brief-writer)
    - brief.md (created by project-brief-writer)
    - tech-stack-decision.md (created by tech-stack-advisor)
    - deployment-strategy.md (created by deployment-advisor)
```
```

**Implementation Tips:**
```
1. Start Simple:
   - Get basic activation working first
   - Add one command at a time
   - Test each command before adding next

2. Be Explicit:
   - Write clear, unambiguous instructions
   - Specify exactly what should happen
   - Don't assume Claude will infer behavior

3. Focus on State:
   - State management is core to this agent
   - Always read workflow-state.md on activation
   - Always save workflow-state.md on changes
   - Make state visible in *status command

4. Test Frequently:
   - Activate agent and try *help
   - Try *status to see state display
   - Try *start-project for workflow init
   - Check if state persists across sessions

5. Iterate:
   - First version won't be perfect
   - Refine based on actual usage
   - Add missing behaviors as discovered
```

### Phase 4: Test Your Agent (1-2 hours)

**Test Plan:**

**Test 1: Basic Activation**
```bash
# In Claude Code
/workflow-coordinator

# Expected:
# - Agent greets with name/role
# - Shows *help menu
# - Shows *status (current state)
# - Halts and waits for command

# Try:
*help
# Should show all available commands

# Try:
*status
# Should show workflow state

# Try:
*exit
# Should save state and exit
```

**Test 2: Start Project Command**
```bash
/workflow-coordinator
*start-project

# Expected:
# - Creates workflow-state.md
# - Invokes project-brief-writer skill
# - Waits for skill completion
# - Shows next action
```

**Test 3: Checkpoint Validation**
```bash
# After brief completes:
*checkpoint

# Expected:
# - Asks understanding questions
# - Validates responses
# - Only proceeds if understanding demonstrated
# - Updates workflow state
```

**Test 4: State Persistence**
```bash
# Start workflow, complete Phase 0
/workflow-coordinator
*start-project
# [Complete brief]

# Exit agent
*exit

# Later: Resume
/workflow-coordinator

# Expected:
# - Loads workflow-state.md automatically
# - Shows current phase progress
# - Guides to next step
```

**Test 5: Skip Handling**
```bash
# Try to skip ahead
/workflow-coordinator
*start-project
# [After brief, try to skip to spinup]

*skip

# Expected:
# - Agent explains what would be missed
# - Validates user understanding of trade-off
# - Only allows if genuinely appropriate
```

**What to Look For:**
```
✅ Agent maintains persona throughout
✅ Commands work as designed
✅ Workflow state persists across sessions
✅ Checkpoints validate understanding
✅ Skills invoked in proper sequence
✅ Phase skipping handled appropriately
✅ Status always visible and accurate

❌ Agent breaks character
❌ Commands fail or do wrong thing
❌ State not saved or corrupted
❌ Can skip checkpoints inappropriately
❌ Skills bypass happens
❌ State becomes invisible
```

### Phase 5: Refine Based on Experience (Ongoing)

**After First Real Project:**
```markdown
# Refinement Notes

## What Worked Well
- [Coordination behavior that was effective]
- [Command that was useful]
- [State tracking that helped]

## What Needs Improvement
- [Behavior that was unclear]
- [Command that was confusing]
- [Missing coordination]

## New Features to Add
- [Command that would be helpful]
- [Workflow that's needed]
- [Better status display]

## Bugs to Fix
- [Behavior that didn't work]
- [Edge case not handled]
- [State sync issue]
```

**Refinement Cycle:**
```
1. Use agent on real project workflow
2. Take notes on what works/doesn't
3. Update agent file
4. Test changes
5. Use on next project
6. Repeat

After 3-4 projects, agent will be well-tuned to your workflow.
```

---

## The Value Proposition

### Time Investment vs Return

**Time to Build:**
```
Phase 1: Study agent architecture     = 1-2 hours
Phase 2: Design coordinator agent     = 2-3 hours
Phase 3: Create agent file           = 1-2 hours
Phase 4: Test and refine             = 1-2 hours
─────────────────────────────────────────────
Total initial investment              = 6-9 hours
```

**Return on Investment:**
```
Every project using workflow skills:
- Automatic progress tracking         (saves confusion)
- Checkpoint enforcement             (prevents bypass)
- Clear next-step guidance          (saves planning time)
- State persistence                 (saves context loss)
- Workflow visibility               (reduces uncertainty)

Over 10 projects: Significant time and clarity return
Plus: Understanding of agent coordination (transferable skill)
Plus: Custom tool optimized for YOUR workflow (valuable)
```

### What You Get

**A Workflow Coordinator:**
```
Instead of:
"I hope I'm following the workflow correctly"
"I hope I didn't skip something important"
"Where am I in this process again?"

You get:
"My coordinator shows I'm at Phase 2 of 4"
"My coordinator enforces proper sequencing"
"My coordinator guides me to next step"
```

**Transferable Knowledge:**
```
Building this agent teaches you:
- Agent coordination patterns
- State management across sessions
- Workflow orchestration
- Checkpoint implementation
- Command-based interfaces
- Persona definition and maintenance

All of this applies to:
- Building other coordinator agents
- Understanding workflow automation
- Designing multi-step processes
- Creating guided experiences
```

**Long-Term Benefit:**
```
Project 1 with agent:  [Workflow followed correctly ✓]
Project 2:             [Progress always visible ✓]
Project 3:             [Checkpoints validated ✓]
...

vs

Without agent:         [Manual tracking, potential skips ✗]
```

---

## Getting Started: Your Next Steps

### When Ready (This Week)

```
☐ Read example agent files (1-2 hours)
  - Focus on structure and patterns
  - Understand YAML format
  - See how state is managed
  - Notice command coordination

☐ Take notes on patterns you see
  - How are workflows coordinated?
  - How is state tracked?
  - How are commands structured?
  - How does halt-and-wait work?
```

### Implementation (Next Week)

```
☐ Create workflow-coordinator-design.md (2-3 hours)
  - Define your agent's persona
  - List coordination commands
  - Sketch workflows
  - Define guardrails

☐ Review design
  - "Does this make sense?"
  - "What am I missing?"
  - "How does state persist?"

☐ Create basic agent file (1-2 hours)
  - Start with minimal working version
  - Just activation + *help + *status + *exit
  - Test that it works
  - Add one command at a time
```

### Testing (Following Week)

```
☐ Add workflow coordination (2-3 hours)
  - *start-project command
  - Skill invocation logic
  - State tracking
  - Checkpoint validation

☐ Test with small project
  - Use agent for real workflow
  - Take notes on experience
  - Refine based on learning

☐ Add remaining commands
  - *next for guidance
  - *resume for continuation
  - *skip with validation
```

### Real Usage

```
☐ Use agent for actual project
  - Follow workflow it coordinates
  - Experience the state tracking
  - Test checkpoint validation
  - Document improvements

☐ Refine based on experience
  - Add missing commands
  - Improve status display
  - Enhance coordination logic

☐ Share your experience
  - Document what worked well
  - Note what surprised you
  - Identify future enhancements
```

---

## Resources to Keep Handy

### File Locations

**Your Workflow Skills:**
```
skill-builder/project-brief-writer/SKILL.md
skill-builder/tech-stack-advisor/SKILL.md
skill-builder/deployment-advisor/SKILL.md
skill-builder/project-spinup/SKILL.md
```

**Example Agents (for architecture reference):**
```
~/.claude/commands/[any-agent].md
# Study any agent for patterns - BMad agents are one option
```

**Your Future Agent:**
```
~/.claude/commands/workflow-coordinator.md
(or)
[project]/.claude/commands/workflow-coordinator.md
```

### Key Documents

**This Document:**
```
skill-builder/agent-notes/workflow-agent-notes.md
```

**Design Document (Create This):**
```
skill-builder/agent-notes/workflow-coordinator-design.md
```

### Getting Help

**When Stuck:**
```
1. Re-read example agent patterns
2. Check this document's design sections
3. Ask Claude Code: "Help me understand [specific issue]"
4. Test small pieces individually
5. Iterate based on what works
```

**When Uncertain:**
```
Ask yourself:
- What would make THIS workflow clearer?
- What would help me know where I am?
- What would I want an agent to track?
- What coordination would help most?

Then: Implement that
```

**When Frustrated:**
```
Remember:
- First versions are never perfect
- Iteration is normal
- Every builder was once a beginner
- Progress > Perfection
- You're learning by building (meta!)
```

---

## Final Encouragement

### You've Got This

**The Pattern:**
```
Unknown → Learning → Understanding → Building → Using

You're on this journey right now.
Building a coordinator agent is the next step.
```

**This Is Achievable:**
```
You have:
- Clear purpose (coordinate workflow skills)
- Existing skills to coordinate
- Examples to learn from
- Clear workflows to implement
- Support available (Claude Code)

You're missing:
- Only the decision to start
```

### What Success Looks Like

**Three Months From Now:**

**Without the Agent:**
```
"Starting new project... which skill do I invoke first?
 Did I skip something? Where am I in the workflow?
 Should I do deployment before or after tech stack?"
```

**With the Agent:**
```
/workflow-coordinator
*start-project

[Agent coordinates entire workflow]
[Progress always visible]
[Checkpoints automatically validated]
[Clear guidance at every step]
[State persists across sessions]

"That was clear and structured. I always knew where I was."
```

**The Difference:**
```
Uncertainty vs Clarity
Manual tracking vs Automatic state
Confusion vs Guidance
Potential bypass vs Protected workflow
```

---

## Go Build It

**You have:**
- ✅ Clear motivation (structured workflow coordination)
- ✅ Specific requirements (coordinate existing skills)
- ✅ Examples to learn from (agent patterns)
- ✅ Understanding of the workflow (you use the skills)
- ✅ Support available (Claude Code)

**The only missing piece:**
- Your decision to start

**So:**

**When ready, study example agents.**
**Design your coordinator.**
**Build it incrementally.**
**Test with real projects.**
**Refine based on experience.**

**In a month, you'll have a workflow coordinator that guides your project development journey.**

**In three months, you'll wonder how you ever managed workflow skills without it.**

---

**The goal isn't just building the agent.**

**The goal is becoming someone who builds coordination systems.**

**And you're ready for that journey.**

**Let's go.** 🧭

---

**Document Created:** November 18, 2025
**For:** Workflow Coordinator Agent Development
**Status:** Ready to inspire and guide
**Next Action:** Study agent patterns, then design your coordinator

🎓 **Happy Building!**
