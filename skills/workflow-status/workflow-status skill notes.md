## Question 1: The `workflow-status` Skill

### How It Would Work

**Core Function:** A simple skill that reads all handoff documents in the current directory and presents a clear picture of where you are in the workflow.

**Example Invocation:**

```
User: "Check workflow status"
or
User: "Where am I in the project setup?"
```

### What It Would Display

```markdown
📍 Project Workflow Status
Project: [recipe-sharing-app] (detected from handoff files)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
✅ Phase 0: Project Brief (COMPLETE)
   Mode: LEARNING
   Brief: brief-recipe-app.md
   Created: 2025-11-10
✅ Phase 1: Tech Stack Selection (COMPLETE)
   Stack: Next.js + Supabase
   Decision: tech-stack-decision.md
   Created: 2025-11-12
✅ Phase 2: Deployment Strategy (COMPLETE)
   Hosting: VPS with Docker
   Strategy: deployment-strategy.md
   Created: 2025-11-14
⏳ Phase 3: Project Foundation (PENDING)
   Next: Invoke project-spinup skill
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🎯 NEXT STEP:
Say: "Use project-spinup skill"
This will create your project foundation with:
- Complete directory structure
- Docker development environment
- claude.md project context
- Choice of Guided Setup or Quick Start
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
📄 Handoff Documents Found:
- PROJECT-MODE.md (LEARNING mode)
- brief-recipe-app.md
- tech-stack-decision.md
- deployment-strategy.md
💡 TIP: You can revise any phase by deleting its handoff file
    and re-running that skill. See HANDOFF-DOCUMENTATION.md
    for revision guidance.
```

### Where It Fits in the Workflow

### **Positioning:**

- **Standalone utility skill** - not part of the linear chain
- Can be invoked at ANY point in the workflow
- Particularly useful when:
  - Returning after days/weeks away
  - Unsure what phase you're in
  - Want to verify decisions before proceeding
  - Checking if all prerequisites are met

**Integration Points:**

```
    ┌─────────────────────────┐
    │  workflow-status skill  │ ← Callable anytime
    │   (utility/helper)      │
    └─────────────────────────┘
              │
              ↓ (reads all handoff docs)            
project-brief-writer
    ↓ (creates PROJECT-MODE.md + brief)
tech-stack-advisor
    ↓ (creates tech-stack-decision.md)
deployment-advisor
    ↓ (creates deployment-strategy.md)
project-spinup
    ↓ (creates project foundation)
```

### Would Claude Code "Just Know" to Use It?

**Short answer:** It depends on how you design it. 

**Option 1: User-Invoked (Explicit)**

- User says: "Check my workflow status"
- Claude recognizes the intent and invokes the skill
- **Pros:** Clear, explicit, user control
- **Cons:** User must remember it exists

**Option 2: Auto-Triggered (Implicit)** You could design each main skill to automatically check status first:

```markdown
# tech-stack-advisor/SKILL.md
## Workflow
### Step 0: Check Prerequisites and Workflow Status
Before proceeding, automatically invoke workflow-status to:
1. Verify PROJECT-MODE.md exists
2. Verify brief exists
3. Show user where they are in the workflow
4. Confirm they're ready for this phase
[Continue with normal tech-stack-advisor workflow...]
```

**My Recommendation:** **Hybrid Approach**

1. **User can invoke explicitly** when they want status
2. **Each skill auto-checks prerequisites** at start but presents it conversationally:

```
User: "Use tech-stack-advisor skill"
Claude: "I can see you've completed the project brief phase 
(LEARNING mode, brief-recipe-app.md created Nov 10). 
Ready to explore technology options for this project?
[Proceeds with tech-stack-advisor...]"
```

This gives users awareness without requiring them to remember a separate command.