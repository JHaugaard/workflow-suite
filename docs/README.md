# Workflow Suite

A comprehensive suite of Claude Code skills and coordination tools for structured project development, from initial concept through deployment-ready foundation.

## Purpose

This repository contains:
- **Workflow Skills**: A coordinated 4-skill workflow for project development
- **Coordinator Agent**: Tools for orchestrating the workflow with checkpoints
- **Documentation**: Design notes and workflow philosophy

## Repository Status

**Active Development** - Skills are deployed to production (`~/.claude/skills`), while this repository serves as the development workspace and documentation hub.

**GitHub Repository**: [JHaugaard/workflow-suite](https://github.com/JHaugaard/workflow-suite)

---

## The Workflow Skills Suite

### Core Skills (Phase 0-3)

A coordinated sequence of skills that guide project development from idea to foundation:

#### 1. project-brief-writer (Phase 0)
- **Purpose**: Transform rough ideas into problem-focused project briefs
- **Creates**: PROJECT-MODE.md, brief.md
- **Status**: Deployed to `~/.claude/skills`
- **Location**: [skills/project-brief-writer/](skills/project-brief-writer/)

#### 2. tech-stack-advisor (Phase 1)
- **Purpose**: Recommend technology stack with trade-off analysis
- **Creates**: tech-stack-decision.md
- **Status**: Deployed to `~/.claude/skills`
- **Location**: [skills/tech-stack-advisor/](skills/tech-stack-advisor/)

#### 3. deployment-advisor (Phase 2)
- **Purpose**: Recommend hosting strategy and deployment workflow
- **Creates**: deployment-strategy.md
- **Status**: Deployed to `~/.claude/skills`
- **Location**: [skills/deployment-advisor/](skills/deployment-advisor/)

#### 4. project-spinup (Phase 3)
- **Purpose**: Generate project foundation with Docker, structure, and learning roadmap
- **Creates**: Complete project scaffold with claude.md
- **Status**: Deployed to `~/.claude/skills`
- **Location**: [skills/project-spinup/](skills/project-spinup/)

### Future Skills (In Development)

#### 5. workflow-status
- **Purpose**: Track and visualize workflow progress across phases
- **Status**: In development
- **Location**: [skills/workflow-status/](skills/workflow-status/)

#### 6. ci-cd-implement (Planned)
- **Purpose**: Set up continuous integration and deployment pipelines
- **Status**: Planned

---

## The Workflow Coordinator Agent

A Claude Code agent that orchestrates the workflow skills suite with proper sequencing, checkpoint validation, and progress tracking.

**Documentation**:
- [agent/workflow-agent-notes.md](agent/workflow-agent-notes.md) - Complete agent design guide
- [agent/agent-ideas.md](agent/agent-ideas.md) - Initial concepts and brainstorming
- [agent/moonshot.md](agent/moonshot.md) - Historical: Original learning-focused agent concept

**Status**: Design phase - implementation pending

---

## Workflow Philosophy

### The Skills Workflow Pattern

```
Project Idea
    ↓
[Phase 0] project-brief-writer
    → Creates: PROJECT-MODE.md, brief.md
    ↓
[Phase 1] tech-stack-advisor
    → Creates: tech-stack-decision.md
    ↓
[Phase 2] deployment-advisor
    → Creates: deployment-strategy.md
    ↓
[Phase 3] project-spinup
    → Creates: Complete project foundation
    ↓
Ready for Development
```

### Key Principles

1. **Sequential Phases**: Each skill builds on artifacts from previous phases
2. **Handoff Documents**: Skills create documents that feed into next skill
3. **Project Modes**: LEARNING/BALANCED/DELIVERY mode set in Phase 0 guides all subsequent phases
4. **Checkpoint Validation**: Understanding validated before progressing
5. **Self-Contained Skills**: Each skill is comprehensive and executable independently

### Integration with Coordinator Agent

The workflow coordinator agent (in development) will:
- Orchestrate skill invocation in proper sequence
- Enforce checkpoint validation between phases
- Maintain workflow state across sessions
- Provide progress visibility
- Prevent phase skipping without validation

---

## Directory Structure

```
workflow-suite/
├── README.md                      # This file
├── HANDOFF-DOCUMENTATION.md       # Session continuity system
├── .gitignore
├── .claude/
│
├── skills/                        # The workflow skills suite
│   ├── project-brief-writer/
│   │   └── SKILL.md              # Phase 0: Project brief creation
│   ├── tech-stack-advisor/
│   │   └── SKILL.md              # Phase 1: Tech stack selection
│   ├── deployment-advisor/
│   │   └── SKILL.md              # Phase 2: Deployment planning
│   ├── project-spinup/
│   │   └── SKILL.md              # Phase 3: Foundation generation
│   └── workflow-status/
│       └── (in development)       # Progress tracking
│
├── agent/                         # Workflow coordinator agent
│   ├── workflow-agent-notes.md   # Main agent design document
│   ├── agent-ideas.md            # Brainstorming and concepts
│   └── moonshot.md               # Historical: Original concept
│
├── docs/                          # Workflow documentation
│   └── (documentation files)
│
└── for-removal/                   # Files segregated for potential removal
    ├── background-docs/          # General context (not workflow-specific)
    ├── claude-code-agent-skills.md
    ├── gemini-review.md
    ├── homelab-setup-v2.md
    └── repo-update.md
```

---

## Usage

### Using the Workflow Skills

The skills are deployed to `~/.claude/skills` and can be invoked in Claude Code:

```
# Start a new project workflow
Skill: project-brief-writer

# After brief is complete, choose tech stack
Skill: tech-stack-advisor

# After tech decision, plan deployment
Skill: deployment-advisor

# Finally, create project foundation
Skill: project-spinup
```

Each skill reads handoff documents from previous phases and creates artifacts for the next phase.

### Building the Coordinator Agent

See [agent/workflow-agent-notes.md](agent/workflow-agent-notes.md) for complete instructions on building the workflow coordinator agent.

---

## Two Repository System

### This Repository (workflow-suite)
- **Purpose**: Development workspace for workflow skills and coordinator agent
- **Location**: `/Volumes/dev/develop/workflow-suite`
- **GitHub**: [JHaugaard/workflow-suite](https://github.com/JHaugaard/workflow-suite)
- **Updates**: Design, documentation, new skill development

### Production Repository (claude-skills)
- **Purpose**: Deployed, production-ready skills
- **Location**: `~/.claude/skills` (both machines)
- **GitHub**: [JHaugaard/claude-skills](https://github.com/JHaugaard/claude-skills)
- **Updates**: Skill maintenance and refinements

**Workflow**: Skills developed here → Deployed to `~/.claude/skills` → Maintained in production

---

## Development Workflow

### Working on Skills

```bash
# All skill development happens here
cd /Volumes/dev/develop/workflow-suite/skills/[skill-name]

# Edit SKILL.md
# Test with Claude Code

# When ready for deployment
cp -R [skill-name] ~/.claude/skills/

# Maintain deployed version
cd ~/.claude/skills
git add [skill-name]/
git commit -m "Update [skill-name]: description"
git push origin main
```

### Building the Coordinator Agent

```bash
# Agent development happens here
cd /Volumes/dev/develop/workflow-suite/agent/

# Follow workflow-agent-notes.md guide
# Create agent file in ~/.claude/commands/

# Test agent in Claude Code
/workflow-coordinator
```

---

## Key Files

### HANDOFF-DOCUMENTATION.md
System for maintaining session continuity when working with Claude Code across multiple sessions. Documents decisions, context, and next steps.

### Skills Documentation
Each skill directory contains:
- **SKILL.md**: Complete skill definition (400-800 lines)
- Self-contained with purpose, workflows, examples, and version history

### Agent Documentation
- **workflow-agent-notes.md**: Complete guide for building coordinator agent
- **agent-ideas.md**: Brainstorming and design concepts
- **moonshot.md**: Historical document from initial agent concept

---

## Version History

**v2.0** (November 18, 2025)
- Reorganized as workflow-suite (formerly skill-builder)
- Focused exclusively on workflow skills and coordinator agent
- Created new directory structure (skills/, agent/, docs/)
- Updated documentation for workflow-centric purpose

**v1.0** (November 2025)
- Initial skill-builder workspace setup
- Developed 4 core workflow skills
- Established two-repository pattern
- Created deployment workflow

---

## Related Resources

### Claude Code Documentation
- [Official Claude Code Docs](https://docs.claude.com/en/docs/claude-code)
- Skills system and agent patterns

### Infrastructure Context
- **Hosting**: Hostinger VPS (Docker-based)
- **Self-hosted**: Supabase, PocketBase, n8n, Ollama, Wiki.js, Caddy
- **DNS**: Cloudflare
- **File Storage**: Backblaze B2
- **Development**: Heavy reliance on Claude Code

---

## Notes

This repository represents a focused workflow system for structured project development. The 4-skill workflow suite guides from initial concept through deployment-ready foundation, while the coordinator agent (in development) will orchestrate the journey with proper sequencing and validation.

The workflow pattern has emerged through practical use and iteration, balancing comprehensive guidance with execution efficiency. Each skill is self-contained and thoroughly documented, making the system maintainable and extensible.

**Philosophy**: Structured learning and project development through coordinated skills, with flexibility for different project modes (LEARNING/BALANCED/DELIVERY).
