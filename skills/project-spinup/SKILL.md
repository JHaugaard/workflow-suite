---
name: project-spinup
description: "Generate complete project foundations with personalized claude.md, Docker setup, and project structure. Supports Guided Setup or Quick Start."
---

# project-spinup

<purpose>
Generate a complete, ready-to-code project foundation tailored to development workflow, infrastructure, and chosen tech stack. Creates comprehensive claude.md for AI assistant context, Docker configuration, directory structure, and either guided learning prompts or full scaffolding.
</purpose>

<output>
Complete project foundation including:
- claude.md (comprehensive project context)
- docker-compose.yml (local development)
- Directory structure (src/, tests/, docs/)
- .env.example, .gitignore, README.md
- Guided setup prompts OR full scaffolding
</output>

---

<prerequisites>
Before invoking, ensure:
1. .docs/PROJECT-MODE.md exists (from project-brief-writer)
2. .docs/brief-*.md exists (from project-brief-writer)
3. .docs/tech-stack-decision.md exists (from tech-stack-advisor)
4. .docs/deployment-strategy.md exists (from deployment-advisor)

Note: Phase 0 will check for these documents and report status conversationally.
</prerequisites>

---

<workflow>

<phase id="0" name="check-prerequisites">
<action>Verify required handoff documents exist and report workflow context conversationally.</action>

<required-documents>
- .docs/PROJECT-MODE.md (workflow mode declaration)
- .docs/brief-*.md (project brief)
- .docs/tech-stack-decision.md (technology stack selection)
- .docs/deployment-strategy.md (deployment strategy)
</required-documents>

<check-process>
1. Scan .docs/ for required documents
2. If missing, inform user which prerequisite skill to run first
3. If present, summarize current workflow state conversationally
4. Proceed with skill workflow
</check-process>

<conversational-status>
When prerequisites are met, report status naturally:

"I can see you've completed all prerequisite phases. You're in {MODE} mode, with {tech-stack} selected for deployment on {hosting-approach}.

Ready to generate your project foundation?"

Then proceed with the skill's main workflow.
</conversational-status>

<missing-prerequisites>
When prerequisites are missing:

"To use project-spinup, you first need to complete the deployment strategy phase.

Run the **deployment-advisor** skill to create:
- .docs/deployment-strategy.md

**Tip:** You can invoke the **workflow-status** skill anytime to see your current progress."
</missing-prerequisites>

<documents-to-read>
After prerequisites confirmed, read:
1. .docs/PROJECT-MODE.md - Workflow commitment (LEARNING/BALANCED/DELIVERY)
2. .docs/tech-stack-decision.md - Complete tech stack with rationale
3. .docs/deployment-strategy.md - Complete deployment strategy
</documents-to-read>

<spinup-approach-prompt>
I see you're in {MODE} mode for this project workflow.

For the project scaffolding, would you prefer:

1. **Guided Setup** - I'll create the foundation, then provide step-by-step prompts
   to build out the structure incrementally. You'll learn how each layer works.
   (Recommended if you're new to {tech stack})

2. **Quick Start** - I'll generate the complete project scaffolding immediately
   with all boilerplate code and configuration.
   (Recommended if you're familiar with {tech stack})

{MODE-informed suggestion}

Which approach would you like?
</spinup-approach-prompt>

<mode-vs-spinup-distinction>
- PROJECT-MODE (LEARNING/BALANCED/DELIVERY): Governs advisory skills - exploration, discussion, checkpoints. Strategic learning.
- spinup_approach (Guided/Quick Start): Governs scaffolding style - step-by-step or all-at-once. Tactical implementation.

Key insight: You can be in LEARNING mode but use Quick Start if familiar with the stack. Or DELIVERY mode but use Guided Setup for a new framework.
</mode-vs-spinup-distinction>
</phase>

<phase id="1" name="gather-information">
<action>Confirm any missing inputs.</action>

<inputs-required>
1. project_name (kebab-case)
2. project_description
3. tech_stack (from tech-stack-decision.md)
4. hosting_plan (from deployment-strategy.md)
5. complexity_level (simple/standard/complex)
6. spinup_approach (guided/quick-start)
7. special_features (optional)
</inputs-required>
</phase>

<phase id="2" name="generate-core-files">
<action>Create foundation files regardless of spinup approach.</action>

<always-create>
1. claude.md - Comprehensive project context
2. docker-compose.yml - Local development environment
3. Directory structure - src/, tests/, docs/
4. .gitignore - Tech-stack-appropriate
5. README.md - Setup and development instructions
6. .env.example - Required environment variables
7. Git initialization guidance
</always-create>
</phase>

<phase id="3" name="apply-spinup-approach">

<guided-setup>
Generate foundation only, plus detailed "Next Steps" section in claude.md with:
- Step-by-step prompts to give Claude Code
- Explanation of what each step creates
- Learning objectives for each step
- Verification checkpoints
- Estimated time for each step

User incrementally asks Claude Code to build out the project.
</guided-setup>

<quick-start>
Generate complete scaffolding including:
- All tech-stack-specific configuration files
- Complete source file structure
- Starter/example code with comments
- Sample tests
- All middleware/utilities
- Initial git commit prepared
</quick-start>

</phase>

<phase id="4" name="provide-next-steps">
<action>Summarize what was created and provide clear next steps.</action>

<summary-includes>
- What was created
- How to start working
- How to run development environment
- Where to find documentation
- Workflow completion status (all 3 phases complete)
- Git initialization commands
</summary-includes>
</phase>

</workflow>

---

<user-context>

<developer-profile>
- Name: John
- Experience: Hobbyist developer, beginner-to-intermediate
- Learning Goal: Deep understanding of full-stack, professional practices
- Reliance: Heavy use of Claude Code for implementation
</developer-profile>

<development-environment>
- Computers: MacBook Pro and Mac Mini
- Sync: iCloud, portable SSDs, GitHub
- IDE: VS Code with relevant extensions
- Git: main + dev branches, conventional commits
- Package Manager: Best for project
- Linting: Best for project
- Directory: src/, docs/, tests/
</development-environment>

<infrastructure>
- Hostinger VPS8: 8 cores, 32GB RAM, 400GB storage
  - Supabase (self-hosted), PocketBase, n8n, Ollama, Wiki.js, Caddy
  - All in Docker containers
- File Storage: Backblaze B2
- DNS: Cloudflare
- VPS Access: SSH as user "john"
- Deployment Options: Localhost, Shared Hosting, Cloudflare Pages, Fly.io, VPS Docker
</infrastructure>

<common-tasks>
- Feature implementation
- Debugging and troubleshooting
- Refactoring
- Testing (unit, integration, E2E)
- Deployment setup
- Performance optimization
- Security implementation
</common-tasks>

<standards>
- Security: OWASP awareness
- Testing: Framework-appropriate
- Code Style: Framework/language best practices
- Commits: Conventional commits format
</standards>

</user-context>

---

<claude-md-template>

<structure>
# {PROJECT_NAME}

> {PROJECT_DESCRIPTION}

**Status**: 🚧 Active Development | **Developer**: John | **Philosophy**: Learning-Focused, Best Practices

---

## Developer Profile
{Experience level, learning goals, development approach, common tasks for Claude Code}

---

## Project Overview
{What project does, tech stack breakdown, architecture decisions}

---

## Development Environment
{Computers/sync, local setup, prerequisites, first-time setup commands}

---

## Infrastructure & Hosting
{Self-hosted infrastructure available, project-specific hosting, access/credentials}

---

## Development Workflow
{Git branching strategy, commit convention, development cycle, testing strategy}

---

## Code Conventions
{File organization, naming conventions, code style/linting}

---

## Common Commands
{Development, database, Docker commands}

---

## Project-Specific Notes
{Authentication, database schema, API endpoints, environment variables}

---

## Deployment
{Deployment workflow, checklist, rollback procedure}

---

## Resources & References
{Project docs, external resources, learning resources}

---

## Troubleshooting
{Common issues and solutions}

---

## Next Steps
{If Guided Setup: detailed step-by-step prompts}
{If Quick Start: immediate next actions}
</structure>

</claude-md-template>

---

<guided-setup-template>

<next-steps-section>
## Next Steps (Guided Setup)

You now have the project foundation. Complete the setup by asking Claude Code to build out the structure step-by-step.

### 📚 Learning Philosophy
Each step teaches about a specific part of the stack. Take your time, review what gets created, ask questions about why things are structured this way.

Estimated total time: {X hours}

---

### Step 1: Initialize {Framework} Structure
⏱️ ~{X} minutes | 🎯 Learning: Project structure and configuration

**What you'll learn**: How {framework} projects are organized, what each config file does.

**Say to Claude Code**:
```
Set up the {Framework} structure with {specifics} as specified in claude.md. Please explain the purpose of each major file and directory.
```

**What will be created**: {list}

**Verification**: {command}

---

### Step 2: Configure Database Connection
⏱️ ~{X} minutes | 🎯 Learning: Database configuration and connection patterns

**Say to Claude Code**:
```
Set up the database client configuration for {database} with environment variables as specified in claude.md.
```

---

### Step 3: Implement Authentication Scaffolding
⏱️ ~{X} minutes | 🎯 Learning: Authentication patterns, security

**Say to Claude Code**:
```
Implement authentication using {auth approach} as specified in claude.md. Include registration, login, logout, and protected route examples.
```

---

### Step 4: Create Example CRUD Feature
⏱️ ~{X} minutes | 🎯 Learning: Full-stack data flow, API design

**Say to Claude Code**:
```
Create a simple CRUD feature for {example entity} that demonstrates best practices. Include frontend form, API endpoints, database operations, and basic tests.
```

---

### Step 5: Add Testing Suite
⏱️ ~{X} minutes | 🎯 Learning: Testing strategies

**Say to Claude Code**:
```
Set up the testing framework and write example tests for the CRUD feature. Include unit and integration tests.
```

---

### Step 6: Configure Docker Development Environment
⏱️ ~{X} minutes | 🎯 Learning: Docker containerization

**Say to Claude Code**:
```
Review and enhance the docker-compose.yml. Explain how to use Docker for local development.
```

---

### Step 7: Document and Prepare for First Feature
⏱️ ~{X} minutes | 🎯 Learning: Documentation practices

**Say to Claude Code**:
```
Help me document the project structure, update the README, and create a template for building new features.
```

---

### 🎓 You're Ready to Build!

After completing these steps, you'll have:
- ✅ Complete {framework} project structure
- ✅ Database connected and configured
- ✅ Authentication working
- ✅ Example CRUD feature as template
- ✅ Testing framework ready
- ✅ Docker environment running
- ✅ Clear development workflow
</next-steps-section>

</guided-setup-template>

---

<tech-stack-templates>

<nextjs-supabase>
Default Configuration:
- Next.js 15 (App Router)
- TypeScript (strict mode)
- Tailwind CSS v4
- Supabase client (auth, database, storage)
- shadcn/ui components (optional)
- React Query / TanStack Query
- Zod for validation
- Jest or Vitest for testing

Files to generate (Quick Start):
- package.json, tsconfig.json, next.config.js
- tailwind.config.ts, postcss.config.js
- app/layout.tsx, app/page.tsx, app/api/
- lib/supabase/client.ts, lib/supabase/server.ts
- components/, middleware.ts
- .eslintrc.json, .prettierrc
- jest.config.js or vitest.config.ts
- tests/, docker-compose.yml
</nextjs-supabase>

<php-mysql>
Default Configuration:
- PHP 8.2+
- MySQL 8.0+
- Composer for dependencies
- Simple MVC structure
- PDO for database access
- PHPUnit for testing

Files to generate (Quick Start):
- composer.json, index.php
- src/Controllers/, src/Models/, src/Views/
- src/Router.php, src/Database.php
- config/database.php, config/config.php
- public/index.php, public/css/, public/js/
- tests/, docker-compose.yml (PHP, MySQL, phpMyAdmin)
</php-mysql>

<fastapi-postgresql>
Default Configuration:
- FastAPI
- PostgreSQL 15
- SQLAlchemy ORM
- Alembic for migrations
- Pydantic for validation
- pytest for testing
- uvicorn for ASGI server

Files to generate (Quick Start):
- requirements.txt, pyproject.toml, main.py
- app/__init__.py, app/models/, app/routers/
- app/schemas/, app/database.py, app/config.py
- tests/, alembic/
- docker-compose.yml
</fastapi-postgresql>

</tech-stack-templates>

---

<execution-summary-template>

<template>
✅ Project foundation created successfully!

📁 Project: {project_name}
📦 Tech Stack: {primary technologies}
🎓 Spinup Approach: {Guided Setup or Quick Start}

---

**Workflow Status: ✅ ALL SKILLS PHASES COMPLETE**
- ✅ Phase 0: project-brief-writer (Brief and PROJECT-MODE.md)
- ✅ Phase 1: tech-stack-advisor (Technology decisions)
- ✅ Phase 2: deployment-advisor (Deployment strategy)
- ✅ Phase 3: project-spinup (Project foundation) ← Just completed

🎉 **Skills workflow complete! Ready to begin development.**

---

Generated:
✅ claude.md - Comprehensive project context
✅ docker-compose.yml - Local development environment
✅ Directory structure (src/, tests/, docs/)
✅ .env.example, .gitignore, README.md
{If Quick Start: ✅ Complete project scaffolding with starter code}

Next Steps:

{If Guided Setup:}
1. Open claude.md and read "Next Steps (Guided Setup)"
2. Copy the first prompt and paste it to Claude Code
3. Follow the guided steps incrementally
4. Ask questions as you go

{If Quick Start:}
1. Review generated code structure
2. Copy .env.example to .env.local and configure
3. Run: docker compose up -d
4. Run: {dev server command}
5. Open http://localhost:{port}
6. Start building features!

Initialize git:
```
cd {project_name}
git init
git checkout -b main
git add .
git commit -m "chore: initial project setup via project-spinup skill"
git checkout -b dev
```

Happy coding! 🚀
</template>

</execution-summary-template>

---

<guardrails>

<must-do>
- Load ALL handoff documents first
- Use handoff documents as primary source
- Ask about spinup approach with MODE-informed suggestion
- Adapt templates to tech stack
- Be comprehensive in claude.md
- Include user's context (workflow, infrastructure, learning goals)
- Respect user choice (Guided/Quick Start equally valid)
- Include testing setup and examples
- Always include docker-compose.yml
- Security-conscious (.env for secrets, .gitignore)
- Show workflow completion status
</must-do>

<must-not-do>
- Skip reading handoff documents
- Use wrong templates for tech stack
- Assume spinup approach without asking
- Generate incomplete foundation
</must-not-do>

</guardrails>

---

<workflow-status>
📍 Skills Phase 3 of 3: Project Foundation

Status:
  ✅ Phase 0: Project Brief (completed by project-brief-writer)
  ✅ Phase 1: Tech Stack (completed by tech-stack-advisor)
  ✅ Phase 2: Deployment Strategy (completed by deployment-advisor)
  🔵 Phase 3: Project Spinup (you are here)

After completion: Skills workflow complete, ready for development
</workflow-status>

---

<integration-notes>

<workflow-position>
Phase 3 of 3 in the Skills workflow chain (final phase).
Requires: deployment-advisor output (.docs/deployment-strategy.md)
Produces: Complete project foundation ready for development
</workflow-position>

<status-utility>
Users can invoke the **workflow-status** skill at any time to:
- See current workflow progress
- Check which phases are complete
- Get guidance on next steps
- Review all handoff documents

Mention this option when users seem uncertain about their progress.
</status-utility>

</integration-notes>
