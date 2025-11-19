# workflow-status

<metadata>
name: workflow-status
description: Display project workflow progress by reading handoff documents in .docs/ directory. This skill should be used when users want to check their workflow status, see what phase they're in, or when other workflow skills need to verify prerequisites. Provides reusable prerequisite-checking templates for integration with other workflow skills. (user)
</metadata>

<purpose>
Read all handoff documents in .docs/ subdirectory and present a clear picture of workflow progress, including completed phases, pending phases, and actionable next steps. Also provide reusable prerequisite-checking logic for other workflow skills to use in their Step 0.
</purpose>

<output>
- Formatted status display showing workflow progress
- Project name detection from directory
- List of found handoff documents
- Clear next step recommendation
- Reusable templates for other skills' prerequisite checks
</output>

---

<workflow>

<phase id="0" name="detect-context">
<action>Read project directory name and scan .docs/ for handoff documents.</action>

<detection-process>
1. Get current working directory name as project name
2. Check for .docs/ subdirectory existence
3. Scan .docs/ for all handoff documents
4. Note which documents are present vs missing
</detection-process>

<handoff-documents>
| Document | Phase | Indicates |
|----------|-------|-----------|
| PROJECT-MODE.md | 0 | Workflow mode established (LEARNING or QUICK) |
| brief-{project}.md | 0 | Project brief completed |
| tech-stack-decision.md | 1 | Technology stack selected |
| deployment-strategy.md | 2 | Deployment approach defined |
| project-foundation-complete.md | 3 | Project scaffolding generated |
</handoff-documents>

<detection-rules>
- PROJECT-MODE.md: Contains mode selection (LEARNING/QUICK)
- Brief file: Matches pattern brief-*.md
- All documents expected in .docs/ subdirectory
- Missing .docs/ directory indicates workflow not started
</detection-rules>
</phase>

<phase id="1" name="analyze-progress">
<action>Determine completion status of each workflow phase based on found documents.</action>

<phase-mapping>
| Phase | Name | Required Documents | Status Logic |
|-------|------|-------------------|--------------|
| 0 | Project Brief | PROJECT-MODE.md + brief-*.md | Both present = COMPLETE |
| 1 | Tech Stack Selection | tech-stack-decision.md | Present = COMPLETE |
| 2 | Deployment Strategy | deployment-strategy.md | Present = COMPLETE |
| 3 | Project Foundation | project-foundation-complete.md | Present = COMPLETE |
</phase-mapping>

<status-determination>
For each phase:
- COMPLETE: All required documents present
- PENDING: Previous phase complete, this phase not started
- BLOCKED: Previous phase incomplete
</status-determination>

<mode-detection>
Read PROJECT-MODE.md to determine:
- LEARNING mode: Full guided workflow
- QUICK mode: Streamlined generation
Display mode prominently in status output.
</mode-detection>
</phase>

<phase id="2" name="determine-next-step">
<action>Identify what the user should do next based on current progress.</action>

<next-step-logic>
If no .docs/ directory:
  → "Start workflow with project-brief-writer skill"

If missing PROJECT-MODE.md or brief:
  → "Complete project brief with project-brief-writer skill"

If missing tech-stack-decision.md:
  → "Select technology stack with tech-stack-advisor skill"

If missing deployment-strategy.md:
  → "Define deployment approach with deployment-advisor skill"

If missing project-foundation-complete.md:
  → "Generate project foundation with project-spinup skill"

If all complete:
  → "Workflow complete! Begin development."
</next-step-logic>

<next-step-details>
For each recommendation, include:
- The skill to invoke
- What it will produce
- Key decisions it will help make
</next-step-details>
</phase>

<phase id="3" name="display-status">
<action>Present formatted status output with all findings.</action>

<status-template>
Project Workflow Status
Project: [{project-name}] (from directory)
Mode: {LEARNING|QUICK} (from PROJECT-MODE.md)
---

{phase-status-list}

---
NEXT STEP:
Say: "Use {next-skill} skill"
{next-step-description}

---
Handoff Documents Found:
{document-list}

TIP: Revise any phase by deleting its handoff file and re-running
that skill. See HANDOFF-DOCUMENTATION.md for guidance.
</status-template>

<phase-status-format>
For each phase, display:
- Status icon: [COMPLETE] or [PENDING] or [BLOCKED]
- Phase number and name
- If complete: Key details (stack chosen, mode selected, etc.)
- If complete: Document name and creation date
- If pending: What this phase will accomplish
</phase-status-format>

<empty-state>
If .docs/ directory not found:

No workflow detected in current directory.

To start a new project workflow:
1. Create or navigate to your project directory
2. Say: "Use project-brief-writer skill"

This will establish your project brief and workflow mode.
</empty-state>
</phase>

</workflow>

---

<reusable-templates>

<prerequisite-check-template>
Other workflow skills should include this in their Phase 0 to auto-check status:

<phase id="0" name="check-prerequisites">
<action>Verify required handoff documents exist and report workflow context.</action>

<required-documents>
{List documents this skill requires}
</required-documents>

<check-process>
1. Scan .docs/ for required documents
2. If missing, inform user which prerequisite skill to run first
3. If present, summarize current workflow state conversationally
4. Proceed with skill workflow
</check-process>

<conversational-status>
When prerequisites are met, report status naturally:
"I can see you've completed {previous-phase} ({key-detail}).
Ready to {current-phase-action}?"

Then proceed with the skill's main workflow.
</conversational-status>

<missing-prerequisites>
When prerequisites are missing:
"To use {this-skill}, you first need {missing-document}.
Run the {prerequisite-skill} skill to create it.

Current workflow status:
{brief-status-summary}"
</missing-prerequisites>
</phase>
</prerequisite-check-template>

<skill-specific-prerequisites>
| Skill | Required Documents | Prerequisite Skill |
|-------|-------------------|-------------------|
| tech-stack-advisor | PROJECT-MODE.md, brief-*.md | project-brief-writer |
| deployment-advisor | tech-stack-decision.md | tech-stack-advisor |
| project-spinup | deployment-strategy.md | deployment-advisor |
</skill-specific-prerequisites>

</reusable-templates>

---

<guardrails>

<must-do>
- Always check .docs/ subdirectory for handoff documents
- Detect project name from current directory
- Provide clear, actionable next step recommendation
- Show which documents were found
- Display workflow mode (LEARNING/QUICK) when detected
- Use consistent status indicators throughout
- Include tip about revision process
</must-do>

<must-not-do>
- Assume documents exist without checking
- Skip displaying the next step recommendation
- Show technical errors to user (handle gracefully)
- Recommend skipping phases in LEARNING mode
- Display raw file paths (use document names)
</must-not-do>

</guardrails>

---

<integration-notes>

<workflow-position>
Standalone utility skill - not part of linear chain.
Can be invoked at any point during workflow.
Other skills reference reusable-templates for their Step 0.
</workflow-position>

<reference-file>
HANDOFF-DOCUMENTATION.md in skill folder provides:
- Complete handoff system documentation
- Revision guidance for each phase
- Troubleshooting for common issues
</reference-file>

</integration-notes>
