---
name: project-spinup
description: Generate complete project foundations with personalized claude.md, Docker setup, and project structure. Supports Learning Mode (guided setup) or Quick Start Mode (full scaffolding). Use after tech-stack-advisor and deployment-advisor to initialize new projects with John's workflow, infrastructure, and best practices.
---

# Project Spinup Skill

## Purpose

This skill generates a complete, ready-to-code project foundation tailored to John's development workflow, infrastructure, and the tech stack chosen via prerequisite meta-skills. It creates a comprehensive claude.md file that serves as the project's AI assistant context, along with Docker configuration, directory structure, and either guided learning prompts or full scaffolding.

## Prerequisites

Before invoking this skill, ensure:
1. **tech-stack-advisor** skill has been run (tech stack decided)
2. **deployment-advisor** skill has been run (deployment plan decided)
3. Project requirements/PRD documented or clearly described

## Inputs Required

When user invokes this skill, gather the following information:

### From User or Prior Skills
1. **project_name** (string) - Project name in kebab-case (e.g., "recipe-manager")
2. **project_description** (string) - Brief description of what the project does
3. **tech_stack** (object) - Output from tech-stack-advisor including:
   - Frontend framework/library
   - Backend framework/language
   - Database system
   - Authentication approach
   - Key libraries/tools
4. **hosting_plan** (object) - Output from deployment-advisor including:
   - Hosting provider and type (VPS, shared, PaaS)
   - Deployment workflow
   - Environment separation needs
5. **complexity_level** (enum: simple/standard/complex) - Default: standard
6. **learning_mode** (boolean) - Default: true
   - true = Learning Mode (foundation + guided prompts)
   - false = Quick Start Mode (full scaffolding immediately)
7. **special_features** (array) - Optional features like:
   - Authentication/authorization
   - Real-time updates
   - File uploads
   - AI/ML integration
   - Payment processing
   - etc.

## John's Personal Context (Hardcoded Constants)

Use these values consistently across all generated files:

### Developer Profile
- **Name**: John
- **Experience Level**: Hobbyist developer, learner, beginner-to-intermediate
- **Learning Goal**: Deep understanding of full-stack development, solid techniques, professional practices
- **Reliance on Claude Code**: Primary development partner, used extensively for coding, debugging, architecture decisions

### Development Environment
- **Computers**: MacBook Pro and Mac Mini (equal time on both)
- **Sync Methods**:
  1. iCloud Documents folder (general files)
  2. Portable SSDs (development projects)
  3. GitHub repositories (code projects)
- **Primary IDE**: VS Code with relevant extensions
- **Git Workflow**: main + dev branches, conventional commits
- **Package Manager**: Best for project (npm, pnpm, yarn, Composer, pip, etc.)
- **Linting/Formatting**: Best for project (ESLint, Prettier, Biome, etc.)
- **Directory Structure**: src/, docs/, tests/

### Infrastructure & Hosting
- **Self-Hosted Infrastructure**: Available on Hostinger VPS(s)
  - Supabase (PostgreSQL, Auth, Storage, Realtime, API)
  - n8n (workflow automation)
  - Ollama (local LLM inference)
  - Redis (caching)
  - Nginx (reverse proxy)
  - PostgreSQL (general use)
  - All in Docker containers
- **Simple Database Projects**: PocketBase when appropriate
- **File Storage**: Backblaze B2 (unless local VPS storage makes more sense)
- **DNS Provider**: Cloudflare (for all domain names)
- **VPS Access**: SSH as user "john" (non-root)
- **Deployment Hosts**: Hostinger VPS or shared hosting

### Common Development Tasks
- Feature implementation
- Debugging and troubleshooting
- Refactoring
- Testing (unit, integration, E2E)
- Deployment setup
- Performance optimization
- Security implementation

### Standards & Conventions
- **Security**: Typical standards, OWASP awareness
- **Testing**: Framework-appropriate (Jest, Vitest, Playwright, pytest, PHPUnit)
- **Code Style**: Framework/language best practices
- **Commit Messages**: Conventional commits format

## Workflow

When this skill is invoked:

### Step 1: Gather Information
If any required inputs are missing, ask the user to provide them. Confirm:
- Project name and description
- Tech stack details (should come from tech-stack-advisor)
- Hosting plan (should come from deployment-advisor)
- Complexity level
- Learning mode preference
- Special features needed

### Step 2: Generate Core Files

**ALWAYS CREATE** (regardless of learning_mode):

1. **claude.md** - Comprehensive project context (see template below)
2. **docker-compose.yml** - Local development environment
3. **Directory structure** - src/, tests/, docs/
4. **.gitignore** - Tech-stack-appropriate
5. **README.md** - Setup and development instructions
6. **.env.example** - Required environment variables
7. **Git initialization guidance** - Commands for user to run

### Step 3: Learning Mode vs Quick Start Mode

#### If learning_mode = true (LEARNING MODE):
Generate foundation only, plus detailed "Next Steps" section in claude.md with:
- Step-by-step prompts to give Claude Code
- Explanation of what each step creates
- Learning objectives for each step
- Verification checkpoints
- Estimated time for each step

User will then incrementally ask Claude Code to build out the project.

#### If learning_mode = false (QUICK START MODE):
Generate complete scaffolding including:
- All tech-stack-specific configuration files
- Complete source file structure
- Starter/example code with comments
- Sample tests
- All middleware/utilities
- Initial git commit prepared

### Step 4: Provide Next Steps
Tell the user:
- What was created
- How to start working (if Learning Mode, copy first prompt)
- How to run the development environment
- Where to find documentation

## claude.md Template

Generate a comprehensive claude.md file using this structure. Adapt each section based on the tech stack and project specifics:

```markdown
# {PROJECT_NAME}

> {PROJECT_DESCRIPTION}

**Status**: 🚧 Active Development | **Developer**: John | **Philosophy**: Learning-Focused, Best Practices

---

## Developer Profile

**Name**: John

**Experience Level**: Hobbyist developer with learning goals focused on:
- Deep understanding of full-stack architecture
- Solid development techniques and professional practices
- Mastering the {PRIMARY_LANGUAGE/FRAMEWORK} ecosystem
- Building production-ready applications

**Development Approach**:
- Heavy reliance on Claude Code for implementation, debugging, and architectural decisions
- Iterative, deliberative approach over one-shot solutions
- Emphasis on understanding WHY, not just WHAT
- Conventional commits, main+dev git workflow
- Testing from the start

**Common Tasks for Claude Code**:
- Feature implementation following best practices
- Debugging and troubleshooting
- Code refactoring and optimization
- Writing tests (unit, integration, E2E)
- Security implementation
- Performance optimization
- Deployment configuration

---

## Project Overview

### What This Project Does
{Expanded project description with key features}

### Tech Stack

**Frontend**: {Frontend framework/library and version}
**Backend**: {Backend framework/language and version}
**Database**: {Database system and version}
**Authentication**: {Auth approach - Supabase, JWT, sessions, etc.}
**Styling**: {CSS approach - Tailwind, CSS Modules, styled-components, etc.}
**Testing**: {Testing frameworks}
**Deployment**: {Hosting plan from deployment-advisor}

### Architecture Decisions
{Brief explanation of why this tech stack was chosen, linking to tech-stack-advisor output if available}

---

## Development Environment

### Computers & Sync
- **Primary Machines**: MacBook Pro and Mac Mini (equal usage)
- **Code Sync**: GitHub repository + portable SSD
- **Documents**: iCloud Documents folder (non-code files)
- **IDE**: VS Code with {relevant extensions for this stack}

### Local Development Setup

#### Prerequisites
- Docker Desktop installed and running
- {Language/runtime installed - Node.js, PHP, Python, etc.}
- {Package manager - npm, Composer, pip, etc.}
- Git configured with conventional commits

#### First-Time Setup

{If LEARNING MODE, include detailed next steps here - see Learning Mode Template}

{If QUICK START MODE, include standard setup commands}

```bash
# Clone repository
git clone {repo_url}
cd {project_name}

# Copy environment variables
cp .env.example .env.local
# Edit .env.local with your values

# Install dependencies
{package manager install command}

# Start Docker services
docker compose up -d

# Run database migrations (if applicable)
{migration command}

# Start development server
{dev server command}

# Open browser to http://localhost:{port}
```

---

## Infrastructure & Hosting

### Self-Hosted Infrastructure (Available)

John maintains a self-hosted infrastructure suite on Hostinger VPS(s):

- **Supabase** (self-hosted): PostgreSQL, Auth, Storage, Realtime, API
- **n8n**: Workflow automation
- **Ollama**: Local LLM inference for AI features
- **Redis**: Caching and sessions
- **Nginx**: Reverse proxy and SSL termination
- **PostgreSQL**: General database use

{If project uses any of these, provide connection details/references}

### Project-Specific Hosting

**Deployment Target**: {From deployment-advisor - e.g., "Hostinger VPS via Docker"}
**DNS**: Cloudflare (nameservers for all domains)
**File Storage**: {Backblaze B2 or local VPS storage}
**SSL**: Let's Encrypt via Certbot

### Access & Credentials

**VPS Access**:
```bash
ssh john@{vps_ip_or_domain}
```

**Environment Variables**: See .env.example for required values

---

## Development Workflow

### Git Branching Strategy

- **main**: Production-ready code
- **dev**: Active development branch
- **feature/***: Feature branches (branch from dev, merge back to dev)
- **fix/***: Bug fix branches

### Commit Convention

Follow conventional commits format:

```
feat: add user authentication with Supabase
fix: resolve database connection timeout
docs: update README with deployment steps
test: add unit tests for API endpoints
refactor: improve query performance
chore: update dependencies
```

### Development Cycle

1. **Create feature branch**: `git checkout -b feature/feature-name`
2. **Make small, focused changes**: Commit frequently
3. **Write tests**: Test as you build
4. **Manual testing**: Verify in browser/environment
5. **Commit**: Use conventional format
6. **Push**: `git push origin feature/feature-name`
7. **When complete**: Merge to dev, test, then merge to main

### Testing Strategy

**Unit Tests**: {Testing framework and command}
**Integration Tests**: {If applicable}
**E2E Tests**: {If applicable}

Run tests:
```bash
{test command}
```

---

## Code Conventions

### File Organization

```
{project_name}/
├── src/                  # Source code
│   ├── {structure based on tech stack}
├── tests/                # All tests
│   ├── unit/
│   ├── integration/
│   └── e2e/
├── docs/                 # Documentation
├── docker-compose.yml    # Local dev environment
├── .env.example          # Environment template
├── .gitignore
├── README.md
└── claude.md            # This file
```

### Naming Conventions
{Tech-stack-specific naming conventions}

### Code Style
{Linting and formatting setup - ESLint, Prettier, Biome, etc.}

---

## Common Commands

### Development
```bash
# Start development environment
{dev start command}

# Stop development environment
{dev stop command}

# View logs
{logs command}

# Run tests
{test command}

# Lint code
{lint command}

# Format code
{format command}
```

### Database
{If applicable - migration commands, seed commands, etc.}

### Docker
```bash
# Start all services
docker compose up -d

# Stop all services
docker compose down

# View logs
docker compose logs -f {service_name}

# Rebuild after changes
docker compose up --build -d

# Reset everything
docker compose down -v && docker compose up -d
```

---

## Project-Specific Notes

### Authentication
{Details about auth implementation for this project}

### Database Schema
{Link to schema documentation or describe key models/tables}

### API Endpoints
{If backend API, document key endpoints or link to API docs}

### Environment Variables
{Explain each required env var and where to get values}

---

## Deployment

### Deployment Workflow
{From deployment-advisor - describe deployment process}

### Deployment Checklist
- [ ] All tests passing
- [ ] Environment variables configured on server
- [ ] Database migrations run
- [ ] SSL certificate configured
- [ ] DNS pointing to correct server
- [ ] Monitoring/logging set up
- [ ] Backups configured

### Rollback Procedure
{How to rollback if deployment fails}

---

## Resources & References

### Project Documentation
- README.md - Setup and basic info
- docs/ - Detailed documentation
- {Tech-stack-specific docs}

### External Resources
- {Framework documentation}
- {Library documentation}
- John's Infrastructure Repo: {Link if applicable}

### Learning Resources
{Relevant tutorials, courses, or documentation for this tech stack}

---

## Troubleshooting

### Common Issues

**Docker containers won't start**:
```bash
docker compose down -v
docker compose up -d
```

**Port already in use**:
```bash
# Find process using port
lsof -i :{port}
# Kill process
kill -9 {pid}
```

{Add tech-stack-specific troubleshooting}

---

## Next Steps

{If LEARNING MODE: Include detailed guided steps - see template below}
{If QUICK START MODE: Include immediate next actions}

---

**Last Updated**: {Current date}
**Claude Code Version**: {If relevant}
**Project Status**: Active Development
```

---

## Learning Mode Template (Add to claude.md "Next Steps" section)

When learning_mode = true, append this to the claude.md:

```markdown
## Next Steps (Learning Mode)

You now have the project foundation. Complete the setup by asking Claude Code to help you build out the structure step-by-step. This approach helps you understand each layer of the application.

### 📚 Learning Philosophy

Each step below is designed to teach you about a specific part of the stack. Take your time, review what gets created, and ask Claude Code questions about why things are structured the way they are.

Estimated total time: {X hours based on complexity}

---

### Step 1: Initialize {Framework} Structure
⏱️ ~{X} minutes | 🎯 Learning: Project structure and configuration

**What you'll learn**: How {framework} projects are organized, what each config file does, and why this structure supports development and deployment.

**Say to Claude Code**:
```
Set up the {Framework} structure with {specifics} as specified in claude.md. Please explain the purpose of each major file and directory as you create them.
```

**What will be created**:
{List specific files/directories}

**Verification**:
```bash
{Command to verify setup}
```

**Before moving to Step 2**: Review the generated files, read the comments, and ensure you understand the project structure.

---

### Step 2: Configure Database Connection
⏱️ ~{X} minutes | 🎯 Learning: Database configuration and connection patterns

**What you'll learn**: How to securely connect to databases, manage connection pools, and structure database access code.

**Say to Claude Code**:
```
Set up the database client configuration for {database} with environment variables as specified in claude.md. Include examples of connection patterns I should follow.
```

**What will be created**:
{List specific files}

**Verification**:
```bash
{Command to test database connection}
```

---

### Step 3: Implement Authentication Scaffolding
⏱️ ~{X} minutes | 🎯 Learning: Authentication patterns, session management, security

**What you'll learn**: How authentication works in {tech stack}, session/token management, and security best practices.

**Say to Claude Code**:
```
Implement authentication using {auth approach} as specified in claude.md. Include registration, login, logout, and protected route examples. Please explain the security considerations.
```

**What will be created**:
{List specific files}

**Verification**:
{How to test auth is working}

---

### Step 4: Create Example CRUD Feature
⏱️ ~{X} minutes | 🎯 Learning: Full-stack data flow, API design, form handling

**What you'll learn**: How data flows from UI → API → Database and back, proper error handling, and validation patterns.

**Say to Claude Code**:
```
Create a simple CRUD feature for {example entity} that demonstrates best practices for this stack. Include frontend form, API endpoints, database operations, and basic tests.
```

**What will be created**:
{List specific files}

**Verification**:
{How to test CRUD operations}

---

### Step 5: Add Testing Suite
⏱️ ~{X} minutes | 🎯 Learning: Testing strategies, test-driven development

**What you'll learn**: How to write effective tests, test structure, and testing best practices for this stack.

**Say to Claude Code**:
```
Set up the testing framework and write example tests for the CRUD feature we just created. Include unit tests, integration tests, and explain when to use each type.
```

**What will be created**:
{List test files}

**Verification**:
```bash
{Command to run tests}
```

---

### Step 6: Configure Docker Development Environment
⏱️ ~{X} minutes | 🎯 Learning: Docker containerization, local development workflows

**What you'll learn**: How Docker containers work, docker-compose orchestration, and why this improves development consistency.

**Say to Claude Code**:
```
Review and enhance the docker-compose.yml to ensure all services are properly configured. Explain how to use Docker for local development and troubleshooting tips.
```

**Verification**:
```bash
docker compose up -d
docker compose ps
```

---

### Step 7: Document and Prepare for First Feature
⏱️ ~{X} minutes | 🎯 Learning: Documentation practices, development workflow

**What you'll learn**: How to document your code, maintain good development habits, and prepare for feature development.

**Say to Claude Code**:
```
Help me document the project structure, update the README with everything we've built, and create a template for building new features. Explain the development workflow I should follow going forward.
```

---

### 🎓 You're Ready to Build!

After completing these steps, you'll have:
- ✅ Complete {framework} project structure
- ✅ Database connected and configured
- ✅ Authentication working
- ✅ Example CRUD feature as a template
- ✅ Testing framework ready
- ✅ Docker environment running
- ✅ Clear development workflow

**Next**: Start building your actual features! Use the CRUD example as a template and ask Claude Code for help implementing each new capability.

**Remember**: Continue using conventional commits, write tests as you go, and commit working increments frequently.
```

---

## Quick Start Mode Template

When learning_mode = false, the skill should generate ALL of the following immediately (adapted for the specific tech stack):

### Files to Generate (Tech-Stack-Specific)

#### Next.js + Supabase Example:
- package.json (with all dependencies)
- tsconfig.json (strict mode)
- next.config.js
- tailwind.config.ts
- postcss.config.js
- app/layout.tsx (with providers)
- app/page.tsx
- app/api/ (example API routes)
- lib/supabase/client.ts
- lib/supabase/server.ts
- lib/supabase/middleware.ts
- components/ (example UI components)
- components/ui/ (shadcn/ui components if applicable)
- middleware.ts (auth middleware)
- .eslintrc.json
- .prettierrc
- jest.config.js (or vitest.config.ts)
- tests/ (example test files)
- docker-compose.yml
- .env.example
- .gitignore
- README.md

#### PHP + MySQL Example:
- composer.json
- index.php (entry point)
- src/ (MVC structure)
  - Controllers/
  - Models/
  - Views/
  - Router.php
  - Database.php
- config/
  - database.php
  - config.php
- public/
  - index.php
  - css/
  - js/
- tests/
- docker-compose.yml (PHP, MySQL, phpMyAdmin)
- .env.example
- .gitignore
- README.md

#### Laravel Example:
Reference existing Laravel setup script or use `laravel new {project_name}`

#### FastAPI + PostgreSQL Example:
- requirements.txt
- pyproject.toml
- main.py
- app/
  - __init__.py
  - models/
  - routers/
  - schemas/
  - database.py
  - config.py
- tests/
- alembic/ (migrations)
- docker-compose.yml
- .env.example
- .gitignore
- README.md

---

## Tech Stack Templates

### Next.js + Supabase

**Default Configuration**:
- Next.js 15 (App Router)
- TypeScript (strict mode)
- Tailwind CSS v4
- Supabase client (auth, database, storage)
- shadcn/ui components (optional)
- React Query / TanStack Query
- Zod for validation
- Jest or Vitest for testing

**docker-compose.yml**:
```yaml
version: '3.8'

services:
  app:
    build:
      context: .
      dockerfile: Dockerfile.dev
    ports:
      - "3000:3000"
    volumes:
      - .:/app
      - /app/node_modules
    environment:
      - NODE_ENV=development
      - NEXT_PUBLIC_SUPABASE_URL=${NEXT_PUBLIC_SUPABASE_URL}
      - NEXT_PUBLIC_SUPABASE_ANON_KEY=${NEXT_PUBLIC_SUPABASE_ANON_KEY}
    command: npm run dev
```

### PHP + MySQL

**Default Configuration**:
- PHP 8.2+
- MySQL 8.0+
- Composer for dependencies
- Simple MVC structure or framework
- PDO for database access
- PHPUnit for testing

**docker-compose.yml**:
```yaml
version: '3.8'

services:
  php:
    image: php:8.2-apache
    ports:
      - "8000:80"
    volumes:
      - .:/var/www/html
    depends_on:
      - mysql

  mysql:
    image: mysql:8.0
    environment:
      MYSQL_ROOT_PASSWORD: ${DB_ROOT_PASSWORD}
      MYSQL_DATABASE: ${DB_DATABASE}
      MYSQL_USER: ${DB_USER}
      MYSQL_PASSWORD: ${DB_PASSWORD}
    ports:
      - "3306:3306"
    volumes:
      - mysql_data:/var/lib/mysql

  phpmyadmin:
    image: phpmyadmin/phpmyadmin
    ports:
      - "8080:80"
    environment:
      PMA_HOST: mysql
      PMA_USER: ${DB_USER}
      PMA_PASSWORD: ${DB_PASSWORD}
    depends_on:
      - mysql

volumes:
  mysql_data:
```

### FastAPI + PostgreSQL

**Default Configuration**:
- FastAPI
- PostgreSQL 15
- SQLAlchemy ORM
- Alembic for migrations
- Pydantic for validation
- pytest for testing
- uvicorn for ASGI server

**docker-compose.yml**:
```yaml
version: '3.8'

services:
  api:
    build:
      context: .
      dockerfile: Dockerfile.dev
    ports:
      - "8000:8000"
    volumes:
      - .:/app
    environment:
      - DATABASE_URL=postgresql://${DB_USER}:${DB_PASSWORD}@db:5432/${DB_NAME}
    depends_on:
      - db
    command: uvicorn main:app --host 0.0.0.0 --port 8000 --reload

  db:
    image: postgres:15
    environment:
      POSTGRES_USER: ${DB_USER}
      POSTGRES_PASSWORD: ${DB_PASSWORD}
      POSTGRES_DB: ${DB_NAME}
    ports:
      - "5432:5432"
    volumes:
      - postgres_data:/var/lib/postgresql/data

volumes:
  postgres_data:
```

---

## .gitignore Template

Generate tech-stack-appropriate .gitignore. Common patterns:

```
# Dependencies
node_modules/
vendor/
venv/
__pycache__/

# Environment
.env
.env.local
.env.*.local

# Build outputs
dist/
build/
.next/
out/

# IDE
.vscode/
.idea/
*.swp
*.swo

# OS
.DS_Store
Thumbs.db

# Logs
*.log
npm-debug.log*
yarn-debug.log*

# Testing
coverage/
.pytest_cache/

# Docker
docker-compose.override.yml

# Misc
.cache/
tmp/
```

---

## README.md Template

```markdown
# {PROJECT_NAME}

> {PROJECT_DESCRIPTION}

**Status**: 🚧 Active Development
**Tech Stack**: {Primary technologies}
**Developer**: John

---

## Quick Start

### Prerequisites
- Docker Desktop
- {Language/runtime}
- {Package manager}

### Setup

1. Clone the repository:
```bash
git clone {repo_url}
cd {project_name}
```

2. Copy environment variables:
```bash
cp .env.example .env.local
# Edit .env.local with your values
```

3. Install dependencies:
```bash
{install command}
```

4. Start development environment:
```bash
docker compose up -d
{dev server command}
```

5. Open http://localhost:{port}

---

## Development

See [claude.md](./claude.md) for comprehensive development documentation, including:
- Development workflow
- Git branching strategy
- Testing approach
- Deployment process
- Troubleshooting

---

## Tech Stack

- **Frontend**: {details}
- **Backend**: {details}
- **Database**: {details}
- **Hosting**: {details}

---

## License

{License info}
```

---

## Skill Execution Summary

After generating all files, provide this summary to the user:

```
✅ Project foundation created successfully!

📁 Project: {project_name}
📦 Tech Stack: {primary technologies}
🎓 Mode: {Learning Mode or Quick Start Mode}

Generated:
✅ claude.md - Comprehensive project context for Claude Code
✅ docker-compose.yml - Local development environment
✅ Directory structure (src/, tests/, docs/)
✅ .env.example - Environment variable template
✅ .gitignore - Tech-stack-appropriate
✅ README.md - Setup instructions
{If Quick Start: ✅ Complete project scaffolding with starter code}

Next Steps:

{If Learning Mode:}
1. Open claude.md and read the "Next Steps (Learning Mode)" section
2. Copy the first prompt and paste it to Claude Code
3. Follow the guided steps to build out your project incrementally
4. Ask questions as you go - learning is the goal!

{If Quick Start Mode:}
1. Review the generated code structure
2. Copy .env.example to .env.local and configure
3. Run: docker compose up -d
4. Run: {dev server start command}
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
```

---

## Notes for Claude

When this skill is invoked:

1. **Gather all required inputs** - Don't proceed until you have tech stack, hosting plan, and project details
2. **Adapt templates to tech stack** - Don't use Next.js templates for a PHP project
3. **Be comprehensive** - The claude.md file is the primary context for future Claude Code sessions
4. **Include John's context** - Personal workflow, infrastructure, learning goals
5. **Learning Mode is default** - Unless user explicitly requests Quick Start
6. **Explain trade-offs** - Help John understand why choices were made
7. **Test-friendly** - Include testing setup and examples
8. **Docker-first** - Always include docker-compose.yml for local dev
9. **Security-conscious** - .env for secrets, .gitignore to prevent leaks
10. **Documentation-rich** - Comments, README, claude.md all work together

The goal is to give John a solid foundation to start learning and building, with Claude Code as his primary development partner.
