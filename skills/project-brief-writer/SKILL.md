---
name: project-brief-writer
description: "Transform rough project ideas into problem-focused briefs that preserve learning opportunities and feed into the Skills workflow (tech-stack-advisor → deployment-advisor → project-spinup)."
---

# project-brief-writer

<purpose>
Transform rough project ideas into problem-focused briefs that preserve learning opportunities and feed into the Skills workflow (tech-stack-advisor → deployment-advisor → project-spinup).
</purpose>

<output>
A polished project brief in narrative/bullet format describing problem, goals, and requirements WITHOUT technology stack, architecture, or implementation details.
</output>

---

<workflow>

<phase id="0" name="create-project-mode">
<action>Create PROJECT-MODE.md declaring learning intent before collecting any project information.</action>

<prompt-to-user>
Before we start, I need to understand your learning intent for this project.

**Which mode best fits your project?**

**LEARNING Mode** (Recommended for skill development)
- Want to learn about technology choices and trade-offs
- Willing to explore options and understand alternatives
- Timeline flexible, learning is primary goal

**DELIVERY Mode** (For time-constrained projects)
- Need to ship quickly with minimal learning overhead
- Already know technology stack or constraints
- Timeline tight, speed is critical

**BALANCED Mode** (Flexible approach)
- Want both learning AND reasonable delivery speed
- Willing to explore but pragmatic about time

**Your choice:** [LEARNING / DELIVERY / BALANCED]
</prompt-to-user>

<file-template name="PROJECT-MODE.md">
# PROJECT-MODE.md
## Workflow Declaration

**Mode:** [USER_CHOICE]
**Decision Date:** [TODAY]

### What This Means

[If LEARNING:]
- Prioritizing understanding technology trade-offs
- Subsequent skills include detailed exploration phases
- Willing to spend time understanding alternatives

[If DELIVERY:]
- Prioritizing speed and efficiency
- Streamlined workflows with quick decisions
- Minimal checkpoints

[If BALANCED:]
- Want both learning and reasonable speed
- Flexible pathways with optional detailed phases

### Workflow Commitments

- Using PROJECT-MODE.md to inform all subsequent decisions
- Following appropriate checkpoint level for this mode
- Mode can be changed by updating this file

### Anti-Bypass Protections

Prevents "Over-Specification Problem" (detailed brief that bypasses learning).
- Each skill checks this file
- Checkpoint strictness based on mode
- Global skipping of ALL checkpoints not allowed in LEARNING/BALANCED modes
</file-template>

<confirmation>
✅ Created PROJECT-MODE.md with MODE: [user's choice]

This file will guide the entire Skills workflow.
</confirmation>
</phase>

<phase id="1" name="present-template">
<action>Present template for user to fill in.</action>

<template name="project-brief-input">
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
</template>

<instruction-to-user>
Please fill in this template with your project idea. Focus on WHAT you want to build and WHY, not HOW to build it. Leave sections blank if you haven't thought about them yet - I'll ask follow-up questions.
</instruction-to-user>
</phase>

<phase id="2" name="analyze-input">
<action>Analyze user responses for completeness, over-specification, and appropriate detail level.</action>

<checks>
- Completeness: Which sections are empty or vague?
- Over-specification: Are they describing implementation instead of requirements?
- Tech mentions: Did they mention specific technologies in requirements sections?
- Appropriate detail: Too vague (needs expansion) or too detailed (bypasses learning)?
</checks>

<detection-rules name="over-specification">
<indicators>
- Specific programming languages, frameworks, or libraries in requirements
- Architecture descriptions (microservices, MVC, etc.)
- Implementation patterns or design patterns
- Specific APIs or third-party services (unless core to problem)
- Database schemas or data models
- Deployment strategies or infrastructure details
</indicators>

<examples>
<bad>Build a REST API using Express.js with JWT authentication</bad>
<good>Build an API that allows authenticated users to access their data</good>

<bad>Use React with Redux for state management and Material-UI components</bad>
<good>Build a user interface with good visual design and responsive layout</good>
</examples>
</detection-rules>

<response-if-overspecified>
I notice you're describing HOW to implement (e.g., 'using Express.js'). For this brief, let's focus on WHAT you need (e.g., 'an API that handles user requests'). The tech-stack-advisor skill will help you explore implementation options later. Would you like to rephrase this as a requirement without specifying the technology?
</response-if-overspecified>

<response-if-tech-in-requirements>
I see you've mentioned [specific technology]. That's a great idea! Let's capture that in the 'Initial Tech Thoughts' section to preserve your input, but keep the requirements technology-agnostic so we can explore options with tech-stack-advisor. Does that work?
</response-if-tech-in-requirements>
</phase>

<phase id="3" name="clarifying-questions">
<action>Ask 2-3 batches of questions based on gaps. Don't ask everything at once.</action>

<batch id="1" name="problem-clarity" trigger="Problem Statement is vague">
- Who experiences this problem?
- What's the current workaround or manual process?
- What's the impact of NOT solving this problem?
- Is this a new problem or improving an existing solution?
</batch>

<batch id="2" name="scope-features" trigger="Features are unclear">
- What's the minimum set of features for this to be useful?
- Are there features that are "nice to have" vs "must have"?
- What should users be able to do that they can't do now?
- Are there features you've explicitly decided NOT to include?
</batch>

<batch id="3" name="users-stakeholders" trigger="Use Cases are weak">
- Who is the primary user?
- Are there different types of users with different needs?
- What are the most common scenarios where this will be used?
- What are the edge cases or unusual situations to consider?
</batch>

<batch id="4" name="success-constraints" trigger="Success Criteria missing">
- How will you measure success?
- What timeline are you working with?
- Are there budget constraints?
- What existing infrastructure or tools must this work with?
</batch>

<batch id="5" name="learning-goals" trigger="Learning mode selected or learning goals unclear">
- Is this primarily a learning project or a delivery project?
- What specific technologies or concepts do you want to learn?
- How much time can you dedicate to learning vs building?
</batch>

<strategy>
- Ask one batch at a time
- Wait for responses before next batch
- Skip batches if already answered
- Use conversational tone
</strategy>
</phase>

<phase id="4" name="generate-brief">
<action>Generate polished brief after gathering sufficient information.</action>

<output-template>
# [Project Name] - Project Brief

## Project Overview
[1-2 paragraph narrative description]

---

## Problem Statement
[Clear description of problem being solved]

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
[Narrative description of what system should do]

**Key Features:**
- **[Feature 1]**: [Description of what it does and why]
- **[Feature 2]**: [Description of what it does and why]
- **[Feature 3]**: [Description of what it does and why]

### User Experience Expectations
[How users should interact with system, without specifying UI technology]

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

---

## Edge Cases to Handle

### [Edge Case Category 1]
**Problem**: [What unusual situation might occur]
**Solution**: [How system should handle it]

### [Edge Case Category 2]
**Problem**: [What unusual situation might occur]
**Solution**: [How system should handle it]

---

## Constraints & Assumptions

### Constraints
- **Timeline**: [If specified]
- **Budget**: [If specified]
- **Infrastructure**: [Existing systems to work with]

### Assumptions
- [Assumption 1]
- [Assumption 2]
- [Assumption 3]

---

## Out of Scope (For Initial Version)

The following features are explicitly not included:
- [Feature that won't be built initially]
- [Feature that won't be built initially]
- [Feature that won't be built initially]

---

## Learning Goals (If Applicable)

**What I Want to Learn:**
- [Learning goal 1]
- [Learning goal 2]

**Skills to Practice:**
- [Skill 1]
- [Skill 2]

---

## Initial Tech Thoughts (User Suggestions - Not Requirements)

**Note:** These are initial technology ideas from the user, captured for reference but NOT requirements. The tech-stack-advisor skill will help explore options.

[User's tech thoughts if provided]

---

## Next Steps

**Recommended workflow:**
1. Review this brief
2. Invoke tech-stack-advisor skill
3. Invoke deployment-advisor skill
4. Invoke project-spinup skill

Ready to proceed? Invoke tech-stack-advisor to explore technology options.
</output-template>

<formatting-rules>
- Use narrative prose in overviews and descriptions
- Use bullet points for lists
- Use bold for emphasis on key terms
- Keep sections clear and scannable
- NO technology specifications in requirements sections
- NO implementation details or "how-to" instructions
</formatting-rules>
</phase>

<phase id="5" name="save-location">
<action>Ask user where to save the brief.</action>

<prompt-to-user>
Where would you like to save this brief?

Suggestions:
- `brief-[project-name].md` in current directory
- A specific project folder path
- Let me know your preference, or I can save it as `brief.md` in the current directory.
</prompt-to-user>
</phase>

<phase id="6" name="confirm-completion">
<action>Confirm save and show workflow status.</action>

<confirmation-template>
✅ Project brief saved to [location]

---

## Workflow Status

**Phase 0: Project Brief** ✓ COMPLETE
- PROJECT-MODE.md created (Mode: [mode])
- Project brief refined and polished

**Phase 1 of 3: Tech Stack Selection** ⏳ PENDING
- Invoke tech-stack-advisor to explore technology options

**Phase 2 of 3: Deployment Planning** ⏳ PENDING
- Invoke deployment-advisor to plan deployment strategy

**Phase 3 of 3: Project Spinup** ⏳ PENDING
- Invoke project-spinup to create foundation

---

Would you like to invoke tech-stack-advisor now, or review/refine the brief first?
</confirmation-template>
</phase>

</workflow>

---

<guardrails>

<must-do>
- Keep brief focused on problem space (WHAT, not HOW)
- Redirect over-specification attempts
- Quarantine tech thoughts in separate section
- Preserve learning opportunities (appropriate detail level)
- Generate narrative, scannable output
- Guide user to next skill in workflow
</must-do>

<must-not-do>
- Specify technologies, frameworks, or libraries in requirements
- Include architecture or design patterns in requirements
- Make implementation decisions
- Skip clarifying questions when gaps exist
- Allow hyper-detailed specifications that bypass learning
</must-not-do>

<quality-checks>
Before generating brief, verify:
1. Problem clarity: Clear what problem is being solved and why?
2. Feature completeness: Core features described at appropriate level?
3. Success criteria: Can success be measured?
4. Use cases: Realistic scenarios described?
5. Boundaries: Scope clear (in-scope and out-of-scope)?
6. Tech neutrality: Requirements free of implementation details?

If any check fails, ask more questions before generating.
</quality-checks>

</guardrails>

---

<examples>

<example name="user-over-specifies">
<user-input>Build a REST API using Node.js and Express with MongoDB database</user-input>
<response>
I notice you're specifying technologies (Node.js, Express, MongoDB). For this brief, let's describe what the API needs to do without specifying the technology.

Try rephrasing as: 'Build an API that stores and retrieves data'

I'll capture 'Node.js, Express, MongoDB' in the Initial Tech Thoughts section so we don't lose your ideas. The tech-stack-advisor skill will help us explore whether these are the best options for your specific needs. Does that work?
</response>
</example>

<example name="user-too-vague">
<user-input>Build a tool to organize photos</user-input>
<response>
Great starting point! Let me ask some clarifying questions:

**Problem Clarity**
- What specific problem with photo organization are you trying to solve?
- Who will use this tool?
- What's your current workflow, and what's frustrating about it?
- What would 'organized' mean for your photos?
</response>
</example>

<example name="good-user-input">
<user-input>Build a migration tool that transfers images from SmugMug to BackBlaze B2 storage while preserving metadata like dates, titles, and albums.</user-input>
<response>
Excellent! This is a well-scoped problem. Let me ask a few questions:

**Scope & Success**
- Approximately how many images will you be migrating?
- Do you need this to run once, or repeatedly for ongoing sync?
- How will you know the migration was successful?
- What should happen if the migration is interrupted?
</response>
</example>

</examples>
