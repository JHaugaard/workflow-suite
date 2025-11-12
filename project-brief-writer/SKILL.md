# project-brief-writer Skill

## Skill Overview

**Purpose:** Transform rough project ideas into problem-focused, learning-appropriate project briefs that preserve learning opportunities and feed cleanly into the Skills workflow (tech-stack-advisor → deployment-advisor → project-spinup).

**When to Use:** When you have a new project idea and want to create a non-technical brief that focuses on WHAT to build (not HOW), preventing the PRD Quality Paradox where over-detailed specifications bypass learning opportunities.

**Output:** A polished project brief in narrative, bullet-point structured format (similar to rename-brief.md style) that describes the problem, goals, and requirements without specifying technology stack, architecture, or implementation details.

---

## How This Skill Works

When invoked, this skill will:

1. **Create PROJECT-MODE.md** to declare your learning intent (LEARNING/DELIVERY/BALANCED)
2. **Present a simple template** for you to fill in with your idea
3. **Read your responses** and analyze for completeness and appropriate detail level
4. **Ask clarifying questions** in batches to fill gaps and expand understanding
5. **Detect over-specification** and redirect if you start specifying HOW instead of WHAT
6. **Quarantine tech thoughts** in a separate section if you mention technology ideas
7. **Generate a polished brief** in the appropriate format
8. **Ask where to save** the output file
9. **Confirm next steps** in the Skills workflow

---

## Instructions for Claude Code

When this skill is invoked, follow this workflow:

### Phase 0: Create PROJECT-MODE.md

Before collecting any project information, you MUST create a PROJECT-MODE.md file in the current directory. This file declares the user's learning intent and workflow commitment, which will guide all subsequent skills.

**Prompt the user:**

"Before we start, I need to understand your learning intent for this project. This will help shape how the entire Skills workflow (tech-stack-advisor → deployment-advisor → project-spinup) guides your learning.

**Which mode best fits your project?**

**LEARNING Mode** (Recommended for skill development)
- You want to learn about technology choices and trade-offs
- Willing to explore multiple options and understand alternatives
- Timeline is flexible, learning is a primary goal
- Skills workflow will include detailed exploration and decision checkpoints

**DELIVERY Mode** (For time-constrained projects)
- You need to ship quickly with minimal learning overhead
- Already know your technology stack or constraints
- Timeline is tight, speed is critical
- Skills workflow will be streamlined and efficient

**BALANCED Mode** (Flexible approach)
- Want both learning AND reasonable delivery speed
- Willing to explore but also pragmatic about time
- Want guidance but maintain flexibility
- Skills workflow will offer both detailed and quick-path options

**Your choice:** [LEARNING / DELIVERY / BALANCED]"

**After user selects, create PROJECT-MODE.md:**

```markdown
# PROJECT-MODE.md
## Workflow Declaration

**Mode:** [LEARNING / DELIVERY / BALANCED]

**Decision Date:** [today's date]

**What this means:**

This file declares the workflow approach for this project's Skills journey (brief → tech-stack → deployment → spinup).

### Mode Details

[If LEARNING:]
- You're prioritizing understanding technology trade-offs and learning opportunities
- Subsequent skills will include detailed exploration phases and checkpoints
- You're willing to spend time understanding alternatives
- This supports deeper learning about your tech stack and architecture

[If DELIVERY:]
- You're prioritizing speed and efficiency
- Subsequent skills will provide streamlined workflows with quick decisions
- Checkpoints will be minimal
- Focus is on getting a solid foundation quickly

[If BALANCED:]
- You want both learning and reasonable speed
- Subsequent skills will offer flexible pathways
- You can skip detailed phases if needed but will be given the option
- Best of both worlds, with trade-offs acknowledged

---

## Workflow Commitments

**I commit to:**
- Using this PROJECT-MODE.md to inform all subsequent decisions
- Being honest about trade-offs in tech and deployment choices
- Following the appropriate checkpoint level for this mode
- Consulting this file if clarification is needed on strictness level

**This mode can be changed by:**
- Explicitly updating this file and committing the change
- Requesting a "mode adjustment" from any subsequent skill

---

## Anti-Bypass Protections

This mode prevents the "Over-Specification Problem" (detailed brief that bypasses learning).

**What this protects against:**
- Jumping to implementation without exploring options
- Skipping important decision checkpoints
- Over-specifying the brief to bypass the workflow

**How it works:**
- Each skill in the workflow will check this file
- Checkpoint strictness is based on this mode
- Brief quality detection will reference this mode
- Global skipping of ALL checkpoints is not allowed in LEARNING/BALANCED modes

---

## Success Criteria

Project is successful when:
- Brief clearly describes the problem and goals
- Tech stack recommendation is informed by trade-off analysis
- Deployment strategy is documented
- Project foundation is scaffolded with learning roadmap
```

Save this file as PROJECT-MODE.md in the current directory (where the user is working). Confirm with user:

"✅ Created PROJECT-MODE.md with MODE: [user's choice]

This file will guide the entire Skills workflow. Each subsequent skill will check this file to determine checkpoint strictness and provide appropriate guidance for your [LEARNING/DELIVERY/BALANCED] mode."

---

### Phase 1: Present Template

Present the following template to the user for them to fill in:

```markdown
# Project Brief Template

## 1. Problem Statement
[What problem are you trying to solve? Why does it need solving?]

## 2. Project Goals
[What do you want to achieve? What does success look like?]

## 3. High-Level Features
[What should the project do? List capabilities, not implementation details]
- Feature 1:
- Feature 2:
- Feature 3:

## 4. Constraints
[What limitations exist? Timeline, budget, infrastructure, etc.]

## 5. Success Criteria
[How will you know the project succeeded?]

## 6. Use Cases
[Describe 2-3 scenarios of how this will be used]

## 7. Known Edge Cases
[What unusual situations should be handled?]

## 8. Explicitly Out of Scope
[What will NOT be included in initial version?]

## 9. Learning Goals (Optional)
[What do you want to learn from this project?]

## Initial Tech Thoughts (Optional - Keep Separate)
[If you have technology ideas, note them here but they won't be treated as requirements]
```

**Instructions to user:**
"Please fill in this template with your project idea. Focus on WHAT you want to build and WHY, not HOW to build it. Leave sections blank if you haven't thought about them yet - I'll ask follow-up questions."

---

### Phase 2: Analyze User's Input

After the user fills in the template, analyze their responses:

**Check for:**
1. **Completeness** - Which sections are empty or vague?
2. **Over-specification** - Are they describing implementation details instead of requirements?
3. **Tech mentions** - Did they mention specific technologies in requirements sections?
4. **Appropriate detail** - Is it too vague (needs expansion) or too detailed (bypasses learning)?

**Detection Rules:**

**Over-Specification Indicators:**
- Mentions of specific programming languages, frameworks, or libraries in requirements
- Architecture descriptions (microservices, MVC, etc.)
- Implementation patterns or design patterns
- Specific APIs or third-party services (unless core to the problem)
- Database schemas or data models
- Deployment strategies or infrastructure details

**Examples:**
- ❌ Over-specified: "Build a REST API using Express.js with JWT authentication"
- ✅ Appropriate: "Build an API that allows authenticated users to access their data"

- ❌ Over-specified: "Use React with Redux for state management and Material-UI components"
- ✅ Appropriate: "Build a user interface with good visual design and responsive layout"

**If over-specification detected:**
Gently redirect: "I notice you're describing HOW to implement (e.g., 'using Express.js'). For this brief, let's focus on WHAT you need (e.g., 'an API that handles user requests'). The tech-stack-advisor skill will help you explore implementation options later. Would you like to rephrase this as a requirement without specifying the technology?"

**If tech thoughts appear in requirements:**
"I see you've mentioned [specific technology]. That's a great idea! Let's capture that in the 'Initial Tech Thoughts' section to preserve your input, but keep the requirements technology-agnostic so we can explore options with tech-stack-advisor. Does that work?"

---

### Phase 3: Ask Clarifying Questions

Based on gaps in their input, ask 2-3 batches of questions. Don't ask everything at once.

**Batch 1: Problem Clarity (if Problem Statement is vague)**
- Who experiences this problem?
- What's the current workaround or manual process?
- What's the impact of NOT solving this problem?
- Is this a new problem or improving an existing solution?

**Batch 2: Scope & Features (if Features are unclear)**
- What's the minimum set of features for this to be useful?
- Are there any features that are "nice to have" vs "must have"?
- What should users be able to do that they can't do now?
- Are there any features you've explicitly decided NOT to include?

**Batch 3: Users & Stakeholders (if Use Cases are weak)**
- Who is the primary user?
- Are there different types of users with different needs?
- What are the most common scenarios where this will be used?
- What are the edge cases or unusual situations to consider?

**Batch 4: Success & Constraints (if Success Criteria missing)**
- How will you measure success?
- What timeline are you working with?
- Are there budget constraints?
- What existing infrastructure or tools must this work with?

**Batch 5: Learning Goals (if applicable)**
- Is this primarily a learning project or a delivery project?
- What specific technologies or concepts do you want to learn?
- How much time can you dedicate to learning vs building?

**Question Strategy:**
- Ask one batch at a time
- Wait for user responses before asking next batch
- Skip batches if user already provided good answers
- Use conversational tone, not interrogation

---

### Phase 4: Generate Polished Brief

After gathering sufficient information, generate a polished brief in this format (based on rename-brief.md style):

```markdown
# [Project Name] - Project Brief

## Project Overview

[1-2 paragraph narrative description of the project]

---

## Problem Statement

[Clear description of the problem being solved]

**Why This Matters:**
- [Impact point 1]
- [Impact point 2]
- [Impact point 3]

---

## Project Goals

### Primary Goal
[Main objective in one sentence]

### Secondary Goals
- [Goal 1]
- [Goal 2]
- [Goal 3]

---

## Functional Requirements

### Core Functionality

[Narrative description of what the system should do]

**Key Features:**
- **[Feature 1]**: [Description of what it does and why]
- **[Feature 2]**: [Description of what it does and why]
- **[Feature 3]**: [Description of what it does and why]

### User Experience Expectations

[How users should interact with the system, without specifying UI technology]

---

## Success Criteria

**Essential Success Metrics:**

1. **[Metric 1]**: [How to measure]
2. **[Metric 2]**: [How to measure]
3. **[Metric 3]**: [How to measure]

**User Satisfaction Indicators:**
- [Indicator 1]
- [Indicator 2]

---

## Use Cases & Scenarios

### Use Case 1: [Name]
**Scenario**: [Describe situation]

**Expected behavior**:
- [Step or outcome 1]
- [Step or outcome 2]
- [Step or outcome 3]

### Use Case 2: [Name]
**Scenario**: [Describe situation]

**Expected behavior**:
- [Step or outcome 1]
- [Step or outcome 2]

[Repeat for 2-4 key use cases]

---

## Edge Cases to Handle

### [Edge Case Category 1]
**Problem**: [What unusual situation might occur]
**Solution**: [How system should handle it]

### [Edge Case Category 2]
**Problem**: [What unusual situation might occur]
**Solution**: [How system should handle it]

[List 3-5 edge cases]

---

## Constraints & Assumptions

### Constraints
- **Timeline**: [If specified]
- **Budget**: [If specified]
- **Infrastructure**: [Existing systems to work with]
- **[Other constraints]**

### Assumptions
- [Assumption 1 about users, environment, or usage]
- [Assumption 2]
- [Assumption 3]

---

## Out of Scope (For Initial Version)

The following features are explicitly not included:

- [Feature/capability that won't be built initially]
- [Feature/capability that won't be built initially]
- [Feature/capability that won't be built initially]

[These can be considered for future iterations]

---

## Learning Goals (If Applicable)

**What I Want to Learn:**
- [Learning goal 1]
- [Learning goal 2]
- [Learning goal 3]

**Skills to Practice:**
- [Skill or concept 1]
- [Skill or concept 2]

---

## Initial Tech Thoughts (User Suggestions - Not Requirements)

**Note:** The following are initial technology ideas from the user. These are captured for reference but are NOT requirements. The tech-stack-advisor skill will help explore options and make informed decisions.

[User's tech thoughts if they provided any]

---

## Next Steps

**Recommended workflow:**

1. **Review this brief** - Ensure it captures your vision accurately
2. **Invoke tech-stack-advisor skill** - Explore technology options based on these requirements
3. **Invoke deployment-advisor skill** - Plan deployment strategy
4. **Invoke project-spinup skill** - Create foundation and learning roadmap

**Ready to proceed?** Invoke the tech-stack-advisor skill to explore technology options for this project.
```

**Formatting Guidelines:**
- Use narrative prose in overviews and descriptions
- Use bullet points for lists and enumeration
- Use bold for emphasis on key terms
- Keep sections clear and scannable
- Maintain conversational but professional tone
- NO technology specifications in requirements sections
- NO implementation details or "how-to" instructions

---

### Phase 5: Save Location

After generating the brief, ask the user:

"Where would you like to save this brief?

Suggestions:
- `brief-[project-name].md` in current directory
- A specific project folder path
- Let me know your preference, or I can save it as `brief.md` in the current directory."

Wait for user response, then save the file to the specified location.

---

### Phase 6: Confirm Next Steps & Workflow Status

After saving, confirm with workflow state visibility:

"✅ Project brief saved to [location]

---

## Workflow Status

**Phase 0: Project Brief** ✓ COMPLETE
- PROJECT-MODE.md created (Mode: [user's mode])
- Project brief refined and polished
- Ready for next phase

**Phase 1 of 3: Tech Stack Selection** ⏳ PENDING
- Invoke tech-stack-advisor to explore technology options
- Based on your MODE ([LEARNING/DELIVERY/BALANCED]), will include appropriate guidance
- Deliverable: Technology recommendation with trade-off analysis

**Phase 2 of 3: Deployment Planning** ⏳ PENDING
- Invoke deployment-advisor to plan deployment strategy
- Will consider your self-hosted infrastructure and constraints
- Deliverable: Deployment strategy document

**Phase 3 of 3: Project Spinup** ⏳ PENDING
- Invoke project-spinup to create foundation
- Choice of Guided Setup or Quick Start based on your MODE
- Deliverable: Project foundation with learning roadmap

---

**Next Steps:**

Would you like to invoke tech-stack-advisor now, or would you like to review/refine the brief first?"

---

## Guardrails & Quality Checks

### What This Skill MUST Do:
- ✅ Keep brief focused on problem space (WHAT, not HOW)
- ✅ Redirect over-specification attempts
- ✅ Quarantine tech thoughts in separate section
- ✅ Preserve learning opportunities (appropriate detail level)
- ✅ Generate narrative, scannable output
- ✅ Guide user to next skill in workflow

### What This Skill MUST NOT Do:
- ❌ Specify technologies, frameworks, or libraries in requirements
- ❌ Include architecture or design patterns in requirements
- ❌ Make implementation decisions
- ❌ Skip asking clarifying questions when gaps exist
- ❌ Allow hyper-detailed specifications that bypass learning

### Quality Checks Before Generating Brief:
1. **Problem clarity**: Is it clear what problem is being solved and why?
2. **Feature completeness**: Are core features described at appropriate level?
3. **Success criteria**: Can success be measured?
4. **Use cases**: Are realistic scenarios described?
5. **Boundaries**: Is scope clear (in-scope and out-of-scope)?
6. **Tech neutrality**: Are requirements free of implementation details?

If any check fails, ask more questions before generating.

---

## Example Interactions

### Example 1: User Over-Specifies

**User input:**
"Build a REST API using Node.js and Express with MongoDB database"

**Skill response:**
"I notice you're specifying technologies (Node.js, Express, MongoDB). For this brief, let's describe what the API needs to do without specifying the technology.

Try rephrasing as: 'Build an API that stores and retrieves data'

I'll capture 'Node.js, Express, MongoDB' in the Initial Tech Thoughts section so we don't lose your ideas. The tech-stack-advisor skill will help us explore whether these are the best options for your specific needs. Does that work?"

### Example 2: User Too Vague

**User input:**
"Build a tool to organize photos"

**Skill response:**
"Great starting point! Let me ask some clarifying questions to flesh this out:

**Batch 1: Problem Clarity**
- What specific problem with photo organization are you trying to solve? (Finding photos? Naming? Storage? Metadata?)
- Who will use this tool? (Just you, family, professional photographers?)
- What's your current workflow, and what's frustrating about it?
- What would 'organized' mean for your photos?"

### Example 3: Good User Input

**User input:**
"Build a migration tool that transfers images from SmugMug to BackBlaze B2 storage while preserving metadata like dates, titles, and albums."

**Skill response:**
"Excellent! This is a well-scoped problem. Let me ask a few questions to round out the brief:

**Batch 1: Scope & Success**
- Approximately how many images will you be migrating?
- Do you need this to run once, or repeatedly for ongoing sync?
- How will you know the migration was successful? (All images transferred? Metadata verified? Other criteria?)
- What should happen if the migration is interrupted?"

---

## Integration with Skills Workflow

This skill is the **first step** in a learning-focused project workflow:

```
Rough Idea
    ↓
project-brief-writer skill ← YOU ARE HERE
    ↓
problem-focused brief.md
    ↓
tech-stack-advisor skill (explore tech options)
    ↓
deployment-advisor skill (plan deployment)
    ↓
project-spinup skill (create foundation)
    ↓
[User decides implementation approach]
```

**Key principle:** This brief preserves "gaps" that the subsequent skills will help explore. Those gaps are where learning happens.

---

## Notes for Future Enhancement

After using this skill 2-3 times, consider adding:
- Support for different project types (hardware, content, research)
- Customizable question batches based on project category
- Support for brief versioning and evolution
- Brief comparison and diff tools

For now, keep it simple and focused on software/app projects.

---

## Version History

### v1.1 (2025-01-11)
**Skills Workflow Refinement - Phase 2**

Key enhancements:
- Added PROJECT-MODE.md auto-creation with LEARNING/DELIVERY/BALANCED mode selection
- Implemented workflow state visibility showing 3-phase Skills progression
- Added MODE-aware guidance for subsequent skills
- Documented anti-bypass protections and checkpoint integration
- Addresses Over-Specification Problem from SmugMug after-action report

### v1.0 (2025-01-08)
**Initial Release**

Core functionality:
- Problem-focused brief template
- Over-specification detection
- Tech thought quarantine section
- Clarifying question batches
- Polished brief generation
- Next-step workflow guidance

---

## Further Reading

### Background Documentation
- **after-action-report.md** - Details on Over-Specification Problem and SmugMug project lessons
- **lovable-vs-claude-code.md** - Strategic vs tactical learning distinction, Phase 0 meta-skills philosophy
- **done-vs-next.md** - Phase 0 meta-skills positioning and workflow focus

### Related Skills
- **tech-stack-advisor** - Uses PROJECT-MODE.md to determine checkpoint strictness (next skill in workflow)
- **deployment-advisor** - Continues MODE-aware workflow guidance (Phase 2)
- **project-spinup** - Final skill that completes the 3-phase workflow

### Workflow Integration
This is the first skill in the 3-phase workflow. PROJECT-MODE.md created here is used by all subsequent skills to determine checkpoint strictness and guidance style. Brief quality affects how advisory skills enforce learning checkpoints.

---

**Skill Version:** 1.1
**Last Updated:** 2025-01-11
**Status:** Enhanced with PROJECT-MODE.md and workflow visibility
