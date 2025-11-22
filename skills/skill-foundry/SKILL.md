---
name: skill-foundry
description: "Generate new Claude Code skills with YAML frontmatter and XML-structured content. This skill should be used when users want to create a new skill that extends Claude's capabilities with specialized knowledge, workflows, or tool integrations. Produces both SKILL.md and README.md files."
---

# skill-foundry

<purpose>
Transform skill ideas into properly-formatted SKILL.md files (YAML frontmatter + XML-structured content) with minimal README documentation. Acts as a BUILDER with CONSULTANT touch—asks clarifying questions, then generates files.
</purpose>

<output>
- SKILL.md: YAML frontmatter (name, description, allowed-tools) + XML-structured instructions
- README.md: Minimal human documentation (overview + version history)
- Files saved to /Volumes/dev/skill-foundry/work/{skill-name}/
</output>

---

<workflow>

<phase id="0" name="gather-requirements">
<action>Collect essential information about the skill to be created.</action>

<required-information>
- Skill name (kebab-case)
- Purpose: What does the skill do? (1-2 sentences)
- Trigger: When should this skill be invoked?
- Output: What does it produce?
- Archetype: CONSULTANT (advisory) or BUILDER (generative) or hybrid
</required-information>

<optional-information>
- Related skills or workflow position
- Known constraints or guardrails
- Example interactions
- Domain-specific terminology
</optional-information>

<prompt-to-user>
What skill would you like to create?

Tell me about:
1. **Name**: What should it be called? (kebab-case, e.g., `api-documenter`)
2. **Purpose**: What does it do?
3. **Trigger**: When would you invoke it?
4. **Output**: What does it produce?

Feel free to share rough ideas—I'll ask clarifying questions.
</prompt-to-user>
</phase>

<phase id="1" name="design-structure">
<action>Determine the skill's phase structure and identify domain-specific XML tags.</action>

<design-process>
1. Break the skill's workflow into logical phases (typically 3-7)
2. Identify what each phase needs to accomplish
3. Determine domain-specific XML tags that would add semantic clarity
4. Note any templates, examples, or detection rules needed
</design-process>

<core-xml-tags>
Required in every skill:
- purpose: What the skill does (1-2 sentences)
- output: What it produces
- workflow: Container for all phases
- phase: Individual execution phases with id/name attributes
- action: What to do in each phase
- guardrails: Must-do and must-not-do constraints
</core-xml-tags>

<optional-xml-tags>
Use as needed—create semantic tags that fit the skill's domain:
- role: CONSULTANT vs BUILDER distinction
- prerequisites: What must exist before invoking
- required-information / optional-information: Input gathering
- template: Output format templates
- examples: Interaction examples
- detection-rules / detection-indicators: Patterns to identify
- response-if-detected: How to respond to patterns
- prompt-to-user: Specific prompts to present
- confirmation: Success messages
- workflow-status: Position in larger workflow
</optional-xml-tags>

<present-to-user>
Present the proposed phase structure:
- Phase names and purposes
- Key XML tags to be used
- Any templates or examples planned

Ask: "Does this structure capture your vision? Any phases to add or remove?"
</present-to-user>
</phase>

<phase id="2" name="draft-skill-md">
<action>Generate the XML-structured SKILL.md file.</action>

<structure-template>
---
name: {skill-name}
description: "{Description for when Claude should use this skill. Use third person. Wrap in quotes.}"
allowed-tools:                    # Optional - omit if skill needs all tools
  - Read
  - Write
  - {other tools as needed}
---

# {skill-name}

<purpose>
{What the skill does in 1-2 sentences.}
</purpose>

<output>
{What it produces.}
</output>

---

<workflow>

<phase id="0" name="{phase-name}">
<action>{What to do in this phase.}</action>

{Phase-specific content using appropriate XML tags...}
</phase>

{Additional phases...}

</workflow>

---

<guardrails>

<must-do>
- {Essential behavior 1}
- {Essential behavior 2}
</must-do>

<must-not-do>
- {Prohibited behavior 1}
- {Prohibited behavior 2}
</must-not-do>

</guardrails>
</structure-template>

<writing-principles>
- YAML frontmatter is REQUIRED: name and description fields, optional allowed-tools
- Use imperative/infinitive form (verb-first instructions)
- Action-oriented content, not explanations
- Templates inline within phases
- No markdown headers for structure—use XML tags (except # title after frontmatter)
- Minimal prose, maximum clarity
- Wrap description in quotes to handle special characters
</writing-principles>

<quality-checks>
Before presenting draft, verify:
1. YAML frontmatter has name and description (quoted)
2. Purpose is clear and concise
3. Each phase has an action tag
4. Guardrails cover essential constraints
5. Templates are embedded where needed
6. XML tags are semantic to the domain
</quality-checks>
</phase>

<phase id="3" name="draft-readme">
<action>Generate minimal README.md with overview and version history.</action>

<readme-template>
# {skill-name} Skill

## Overview

{2-3 sentence description of what the skill does and when to use it.}

**Use when:** {Trigger condition}

**Output:** {What it produces}

---

## How It Works

When invoked, this skill will:

1. {Phase 0 summary}
2. {Phase 1 summary}
3. {Additional phases...}

---

## Version History

### v1.0 ({date})
**Initial Release**

- {Key feature 1}
- {Key feature 2}
- {Key feature 3}

---

**Version:** 1.0
**Last Updated:** {date}
</readme-template>
</phase>

<phase id="4" name="review-and-refine">
<action>Present both drafts for user review and incorporate feedback.</action>

<presentation>
Present SKILL.md and README.md drafts clearly separated.

Ask: "Review these drafts. What would you like to adjust?"
</presentation>

<refinement-scope>
- Phase structure changes
- XML tag adjustments
- Content additions or removals
- Guardrail modifications
- Template refinements
</refinement-scope>

<iteration>
Incorporate feedback and re-present until user approves.
</iteration>
</phase>

<phase id="5" name="save-to-foundry">
<action>Save approved files to the work directory.</action>

<save-location>
/Volumes/dev/skill-foundry/work/{skill-name}/
├── SKILL.md
└── README.md
</save-location>

<confirmation>
Saved {skill-name} to /Volumes/dev/skill-foundry/work/{skill-name}/

**Next steps:**
1. Test the skill by invoking it
2. Refine based on results
3. When ready, manually deploy to ~/.claude/skills/
</confirmation>
</phase>

</workflow>

---

<guardrails>

<must-do>
- Ask clarifying questions before generating
- Generate both SKILL.md and README.md
- Start SKILL.md with YAML frontmatter (--- delimited) containing name and description
- Use XML tags for structure, not markdown headers (after the title)
- Include guardrails section in every skill
- Present drafts for review before saving
- Use imperative/infinitive writing style
- Create semantic XML tags that fit the skill's domain
- Wrap description value in quotes in YAML frontmatter
</must-do>

<must-not-do>
- Enforce rigid XML tag vocabulary beyond core tags
- Skip to generation without understanding requirements
- Create verbose/explanatory content in SKILL.md (save for README)
- Mix human documentation into SKILL.md
- Use second-person ("you should") in skill instructions
- Add markdown headers (##, ###) for structural organization
- Use old <metadata> XML format instead of YAML frontmatter
- Omit YAML frontmatter—it is required for Claude Code to discover skills
</must-not-do>

</guardrails>

---

<reference-patterns>

<example-skill name="project-brief-writer">
Demonstrates:
- Clean phase progression with clear actions
- Embedded templates within phases
- Detection rules with indicators and examples
- User prompts as structured content
- Quality checks before output generation
</example-skill>

<xml-tag-flexibility>
The XML tag vocabulary is intentionally flexible. Create tags that make semantic sense for the skill's domain:

- A code-reviewer skill might use: <review-criteria>, <severity-levels>, <code-smells>
- A meeting-summarizer might use: <extraction-rules>, <summary-format>, <action-items>
- An API-documenter might use: <endpoint-template>, <parameter-rules>, <example-requests>

The goal is semantic clarity, not conformity.
</xml-tag-flexibility>

</reference-patterns>
