---
name: deployment-advisor
description: Recommend hosting strategy based on chosen tech stack and project needs. Provides deployment workflow, cost breakdown, and scaling path. Considers Hostinger infrastructure, budget, and complexity tolerance. Use after tech-stack-advisor, before project-spinup skill.
allowed-tools: [Read, Grep, Glob, WebSearch, Write]
---

# Deployment Advisor Meta-Skill

## Purpose

This meta-skill recommends hosting and deployment strategies tailored to your chosen tech stack, project requirements, and infrastructure constraints. It provides concrete deployment workflows, cost estimates, and scaling paths to help you make informed hosting decisions.

---

## Advisory Mode (Important)

**This is a CONSULTANT role, not a BUILDER role.** This skill provides hosting recommendations and strategies only. It will not:
- ❌ Configure servers or infrastructure
- ❌ Create deployment scripts or automation
- ❌ Set up CI/CD pipelines
- ❌ Begin actual deployment

**When deployment assistance is requested:** I can write reference documentation (deployment runbooks, configuration examples, troubleshooting guides) only when explicitly requested for learning purposes.

**Next step after recommendations:** Use the [project-spinup](../project-spinup/SKILL.md) skill to scaffold the actual project foundation with deployment integration.

---

## When to Use

**Invoke this skill**:
- ✅ After tech-stack-advisor has recommended a tech stack
- ✅ Before invoking project-spinup skill
- ✅ When evaluating hosting options for existing projects
- ✅ When project outgrows current hosting and needs scaling
- ✅ When comparing self-hosted vs managed services

**Don't invoke this skill**:
- ❌ Before deciding on tech stack (tech stack drives hosting choice)
- ❌ For quick local-only prototypes (no deployment planned)
- ❌ When hosting is mandated by client/organization

---

## Checkpoint System

This skill includes checkpoints to validate your deployment understanding before proceeding. The strictness depends on your **PROJECT-MODE.md** setting:

### LEARNING Mode

You're exploring deployment options and trade-offs. Checkpoints are detailed:

**After recommendations are presented, you'll answer 3 focused comprehension questions:**
- Question 1: Why does the primary recommendation fit this project's core deployment needs?
- Question 2: What is the single most important trade-off if you chose Alternative 1 instead?
- Question 3: What is the biggest maintenance responsibility or operational challenge this deployment introduces?

**Rules for LEARNING mode:**
- ✅ Short but complete answers acceptable (not essays)
- ✅ Question-by-question SKIP allowed with acknowledgment ("I understand but want to skip this one")
- ❌ NO global bypass - can't skip all questions at once
- 📝 Educational feedback provided on answers
- 🔄 Mode change required if you want to loosen strictness

### BALANCED Mode

You want learning with flexibility. Checkpoints are moderate:

**Simple self-assessment checklist:**
- [ ] I understand why this hosting approach fits my tech stack
- [ ] I understand the deployment workflow
- [ ] I've considered the cost and maintenance trade-offs
- [ ] I'm ready to initialize my project

Simply confirm to proceed. Review options available if needed.

### DELIVERY Mode

You need to move fast. Checkpoints are minimal:

**Quick acknowledgment:**
"Ready to proceed? [Yes/No]"

That's it. Move forward efficiently.

---

## Prerequisites

You should have:
1. **Tech stack decided** (from tech-stack-advisor or manual decision)
2. **Project requirements** clear (traffic expectations, uptime needs, etc.)
3. **Budget constraints** understood (monthly hosting budget)

## Workflow

### Step 0: Check for Existing PROJECT-MODE.md

Before proceeding, I'll read PROJECT-MODE.md (if it exists) to determine:
- Your declared mode (LEARNING/DELIVERY/BALANCED)
- Checkpoint strictness level
- Workflow context from brief and tech stack decisions

This file is created by project-brief-writer skill and updated by tech-stack-advisor.

---

### Step 1: Gather Information

Ask the user for:

#### Required Information
1. **Tech Stack Details** (from tech-stack-advisor output):
   - Frontend framework
   - Backend framework/language
   - Database system
   - Special requirements (WebSockets, background jobs, etc.)

2. **Expected Traffic**:
   - Personal use only (you and maybe friends)
   - Small public project (<100 daily users)
   - Growing project (100-1000 daily users)
   - Medium traffic (1000-10000 daily users)
   - High traffic (>10000 daily users)

3. **Uptime Requirements**:
   - Casual (downtime acceptable for learning projects)
   - Standard (reasonable uptime, some downtime OK)
   - Professional (high uptime expected, monitoring needed)
   - Critical (24/7 uptime, redundancy required)

4. **Budget Constraints**:
   - What's your monthly hosting budget?
   - Are you willing to pay for managed services?
   - Prefer predictable costs or pay-as-you-go?

5. **Deployment Frequency**:
   - Rarely (once deployed, rarely update)
   - Occasional (weekly/monthly updates)
   - Frequent (daily/multiple times per day)
   - Continuous (automated CI/CD)

#### Optional But Helpful
6. **Technical Comfort Level**:
   - Prefer managed/easy solutions
   - Comfortable with some terminal/SSH work
   - Want to learn server management
   - Experienced with DevOps

7. **Geographic Considerations**:
   - Where are your users located?
   - Does latency matter?
   - Need CDN for static assets?

8. **Compliance/Privacy**:
   - Data sovereignty requirements?
   - GDPR/HIPAA/other compliance?
   - Want full control over data?

### Step 2: Consider John's Context

**Always factor in**:

#### Available Infrastructure
- **Hostinger VPS8**: Self-hosted infrastructure (8 cores, 32GB RAM, 400GB storage)
  - Supabase (self-hosted full stack: PostgreSQL, Auth, Storage, Realtime, REST API)
  - PocketBase (single-binary backend with SQLite)
  - n8n (workflow automation)
  - Ollama (local LLM inference)
  - Wiki.js (documentation platform)
  - Caddy (reverse proxy, automatic HTTPS)
  - Docker/Docker Compose based setup
  - SSH access as user "john"
- **DNS**: Cloudflare for all domains
- **File Storage**: Backblaze B2 account available
- **Development Workflow**: Docker, git, main+dev branches

#### Preferences
- Prefers self-hosting when practical (learning + cost control)
- Hostinger as primary provider
- Budget-conscious but willing to pay for right solution
- Values learning over convenience

#### Skills
- Comfortable with SSH and terminal basics
- Learning Docker, Nginx, server management
- Can follow documentation and troubleshoot
- Has Claude Code for complex configuration

### Step 3: Analyze & Recommend

Generate a comprehensive recommendation including:

1. **Primary Hosting Recommendation**: Best-fit hosting approach
2. **Deployment Workflow**: Step-by-step deployment process
3. **Cost Breakdown**: Setup + monthly costs
4. **Scaling Path**: What to do when project grows
5. **Alternative Options**: Other viable approaches
6. **Monitoring & Maintenance**: What to track and how often
7. **Backup Strategy**: How to backup and restore
8. **Next Steps**: How to proceed (usually: invoke project-spinup)

### Step 4: Create Handoff Document

**CRITICAL**: After analyzing the hosting requirements and formulating recommendations, you MUST create a `deployment-strategy.md` file in the current working directory.

This document serves as:
- **Handoff artifact** for the next skill in the workflow (project-spinup)
- **Session bridge** allowing the deployment strategy to be loaded in a fresh session
- **Deployment record** documenting the complete hosting plan for future reference

**File location**: `./deployment-strategy.md` (same directory as PROJECT-MODE.md and tech-stack-decision.md)

**When to create**: After collaborative refinement and user convergence on the recommendation. This occurs AFTER presenting recommendations in console and discussing any questions or alternatives with the user.

Use the Write tool to create this file with the complete analysis.

---

### Step 5: Output Format

Use this structured format for both the console output AND the deployment-strategy.md file:

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🎯 PRIMARY HOSTING RECOMMENDATION: {Hosting Approach}
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

{2-3 sentence summary}

WHY THIS FITS YOUR PROJECT:
• {Reason 1 - tech stack compatibility}
• {Reason 2 - traffic/performance match}
• {Reason 3 - budget alignment}
• {Reason 4 - deployment ease}

WHY THIS FITS YOUR INFRASTRUCTURE:
• {How it uses existing Hostinger VPS}
• {Integration with current setup}
• {Learning opportunity}

HOSTING DETAILS:
─────────────────────────────────────────────────────────
Provider:        {Hostinger VPS / Shared / Other}
Server Type:     {VPS / Shared / PaaS / Serverless}
Container:       {Docker / Native / Platform-managed}
Database:        {Self-hosted PostgreSQL / MySQL / Managed}
File Storage:    {Local VPS / Backblaze B2 / Provider storage}
CDN:             {Cloudflare / Provider CDN / None}
SSL:             {Let's Encrypt / Cloudflare / Provider}
Domain DNS:      {Cloudflare nameservers}
─────────────────────────────────────────────────────────

DEPLOYMENT WORKFLOW:
─────────────────────────────────────────────────────────

Initial Setup (One-time):
1. {Step 1}
2. {Step 2}
3. {Step 3}
...

Regular Deployment (For updates):
1. {Step 1}
2. {Step 2}
3. {Step 3}
...

Rollback Procedure (If deployment fails):
1. {Step 1}
2. {Step 2}
...

─────────────────────────────────────────────────────────

DEPLOYMENT SPEED: 🟢 Fast / 🟡 Moderate / 🔴 Slow
Initial deploy: ~{X minutes/hours}
Update deploy: ~{X minutes}

MAINTENANCE BURDEN: 🟢 Low / 🟡 Medium / 🔴 High
Weekly time: ~{X minutes/hours}

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
💰 COST BREAKDOWN
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

SETUP COSTS (One-time):
• Domain: ${X}/year (~${Y}/month) [if new domain needed]
• SSL Certificate: $0 (Let's Encrypt)
• Initial config: $0 (DIY with Claude Code)
TOTAL SETUP: ~${X}/month amortized

MONTHLY ONGOING:
• Hosting: ${X}/month
  └─ {Details - e.g., "Existing Hostinger VPS (already paid)"}
• Database: ${Y}/month
  └─ {Details - e.g., "Self-hosted on VPS (included)"}
• File Storage: ${Z}/month
  └─ {Details - e.g., "Backblaze B2, ~$0.005/GB"}
• CDN/Bandwidth: ${A}/month
  └─ {Details}
• Monitoring: ${B}/month
  └─ {Details - e.g., "Free tier (UptimeRobot)"}
• Backup Storage: ${C}/month
  └─ {Details}

TOTAL MONTHLY: ${Total}/month

COST SCALING (As traffic grows):
• Current: ${X}/month
• 10x traffic: ${Y}/month (what changes)
• 100x traffic: ${Z}/month (migration likely needed)

COMPARE TO ALTERNATIVES:
• Cloudflare Pages: $0/month (static/frontend only)
• Fly.io: ${X}/month (global distribution, managed)
• VPS: ${Y}/month (full control, existing infrastructure)
• Shared hosting: ${Z}/month (cheapest, PHP only)

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
📈 SCALING PATH
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

CURRENT STATE: {Current recommendation}
└─ Good for: {Traffic level}
└─ Limitations: {What will bottleneck first}

PHASE 1 OPTIMIZATION: {When to do this}
├─ Add: {What to add - caching, CDN, etc.}
├─ Optimize: {What to optimize - queries, assets, etc.}
├─ Cost: +${X}/month
└─ Handles: {New traffic level}

PHASE 2 SCALING: {When current setup maxes out}
├─ Migration: {What to migrate - e.g., database to managed service}
├─ New setup: {Description}
├─ Cost: ${X}/month (new total)
└─ Handles: {Even higher traffic level}

PHASE 3 ADVANCED: {For serious scale}
├─ Architecture: {What changes - load balancers, microservices, etc.}
├─ Providers: {Likely need cloud providers at this point}
├─ Cost: ${X}/month
└─ Handles: {Enterprise-level traffic}

WHEN TO SCALE:
• Phase 1: {Specific metric - e.g., "CPU >70% consistently"}
• Phase 2: {Specific metric - e.g., "Database queries >1000/sec"}
• Phase 3: {Specific metric - e.g., "Downtime impacts revenue"}

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🔄 ALTERNATIVE HOSTING OPTIONS
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

ALTERNATIVE 1: {Hosting approach}

PROS:
+ {Advantage 1}
+ {Advantage 2}
+ {Advantage 3}

CONS:
- {Disadvantage 1}
- {Disadvantage 2}
- {Disadvantage 3}

COST: ${X}/month
BEST FOR: {Specific scenario}
WHEN TO CHOOSE: {Conditions}

─────────────────────────────────────────────────────────

ALTERNATIVE 2: {Hosting approach}

[Similar structure]

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
📊 MONITORING & MAINTENANCE
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

MONITORING SETUP:

Uptime Monitoring:
• Tool: {UptimeRobot / Pingdom / Custom}
• Cost: ${X}/month (free tier available)
• Setup: {Brief description}

Performance Monitoring:
• Tool: {New Relic / Datadog / Custom scripts}
• Cost: ${X}/month
• Metrics: {What to track - response time, error rate, etc.}

Resource Monitoring:
• Tool: {htop / docker stats / Cloud provider dashboard}
• Cost: $0 (built-in)
• Check frequency: {How often}

MAINTENANCE SCHEDULE:

Daily (5 minutes):
□ Check uptime status (automated alerts)
□ Review error logs if any alerts

Weekly (15-30 minutes):
□ Review resource usage (CPU, RAM, disk)
□ Check backup logs
□ Review access logs for anomalies
□ Update monitoring if needed

Monthly (1-2 hours):
□ Apply security updates (OS, packages)
□ Review and clean old logs/backups
□ Review costs and optimize
□ Test backup restore procedure
□ Update documentation if changes made

Quarterly (2-4 hours):
□ Review architecture for optimization opportunities
□ Evaluate scaling needs
□ Test disaster recovery procedures
□ Review and update runbooks

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
💾 BACKUP STRATEGY
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

BACKUP COMPONENTS:

Database Backups:
• Frequency: {Daily / Multiple times per day}
• Method: {pg_dump / MySQL dump / Provider snapshots}
• Storage: {Backblaze B2 / Local + offsite / Provider}
• Retention: {X days daily, Y weeks weekly, Z months monthly}
• Automation: {cron job / scheduled script}

File Storage Backups:
• Frequency: {Daily / Weekly}
• Method: {rsync / rclone / Provider snapshots}
• Storage: {Backblaze B2 / Multiple locations}
• Retention: {X days}

Configuration Backups:
• Frequency: {On change}
• Method: {Git repository}
• Storage: {GitHub private repo}
• What: {Nginx configs, env files (sanitized), docker-compose, etc.}

BACKUP VERIFICATION:
• Test restores: {Monthly / Quarterly}
• Document restore procedure: {Location of docs}
• Recovery time objective (RTO): {X hours}
• Recovery point objective (RPO): {X hours data loss acceptable}

DISASTER RECOVERY PROCEDURE:

If VPS completely fails:
1. {Step 1 - provision new VPS}
2. {Step 2 - restore configurations}
3. {Step 3 - restore database}
4. {Step 4 - restore files}
5. {Step 5 - update DNS if needed}
6. {Step 6 - verify everything works}

Estimated recovery time: {X hours}

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🔒 SECURITY CONSIDERATIONS
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

IMPLEMENTED SECURITY:
✅ {Security measure 1 - e.g., "SSH key-only access"}
✅ {Security measure 2 - e.g., "Firewall (UFW) configured"}
✅ {Security measure 3 - e.g., "SSL/TLS via Let's Encrypt"}
✅ {Security measure 4 - e.g., "Fail2ban for brute force protection"}
✅ {Security measure 5 - e.g., "Regular security updates"}

RECOMMENDED ADDITIONS:
□ {Additional security measure if needed}
□ {Another measure}

SECURITY MAINTENANCE:
• Review access logs: {Frequency}
• Update passwords/keys: {Frequency}
• Security audit: {Frequency}
• Penetration testing: {If applicable}

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🎯 RECOMMENDED NEXT STEPS
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

📄 HANDOFF DOCUMENT CREATED: deployment-strategy.md
This file contains the complete hosting plan and can be referenced in new sessions.

1. Review this hosting recommendation
   - Questions about deployment workflow? Ask me
   - Unsure about costs? I can break down further
   - Want to explore alternatives? Tell me which one

2. If you agree with PRIMARY recommendation:
   → Proceed to project initialization
   Say: "Use project-spinup skill with:
         - Tech stack: {from tech-stack-advisor}
         - Hosting: {this recommendation}
         - Learning mode: {true/false}"
   Note: project-spinup will read deployment-strategy.md automatically

3. Document hosting decision:
   → Recommendation already saved to deployment-strategy.md
   → Will be integrated into project README by project-spinup
   → Keep for infrastructure documentation

4. Set up monitoring before deploying:
   → UptimeRobot or similar
   → Backup automation
   → Resource monitoring

5. Plan first deployment:
   → Review deployment workflow above
   → Prepare credentials/access
   → Schedule deployment window
   → Have rollback plan ready

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

## Hosting Decision Framework

Use this framework when analyzing options:

### Scoring Criteria (Rate each 1-5)

**Tech Stack Compatibility**:
- Can hosting handle the chosen tech stack?
- Are all required services available?
- Any limitations or workarounds needed?

**Performance Requirements**:
- Can it handle expected traffic?
- Acceptable latency for users?
- Scalability when needed?

**Cost Efficiency**:
- Fits budget constraints?
- Predictable vs variable costs?
- Cost-effective as project grows?

**Deployment Ease**:
- How complex is deployment?
- Can John manage it (with Claude Code help)?
- Automated or manual process?

**Maintenance Burden**:
- How much weekly maintenance?
- Monitoring complexity?
- Update frequency needed?

**Learning Value**:
- Teaches useful skills?
- Applicable to future projects?
- Industry-relevant experience?

### Recommendation Logic

**Choose Localhost When**:
- Personal utility app for your own use only
- Low-usage applications that don't need global access
- Learning and experimentation
- Testing before public deployment
- No sharing or distribution requirements
- Project is genuinely personal-scale

Example: Local development tools, learning projects, prototypes, personal productivity apps

**Choose Hostinger Shared Hosting When**:
- Simple PHP-based application
- Traditional LAMP stack preferred
- Minimal maintenance desired
- No Docker/container needs
- Want to use existing shared hosting account ($0 marginal cost)
- cPanel management interface preferred

Example: Simple PHP blog, WordPress site, basic company website, PHP CRUD apps

**Choose Cloudflare Pages When**:
- Frontend-only application
- Static or JAMstack architecture
- Want zero cost
- Need global CDN/performance
- No persistent backend state needed
- Want instant deployment

Example: React SPA, portfolio site, documentation, landing pages

**Choose Fly.io When**:
- Full-stack app with database
- Need global distribution
- Want managed infrastructure
- Comfortable with Docker
- Can budget $20-50/month
- Don't want to manage servers
- Need auto-scaling

Example: Multi-region apps, global APIs, production apps needing reliability

**Choose VPS with Docker When**:
- Tech stack runs well in Docker
- Traffic is low-to-moderate (<10k daily users)
- Want to learn DevOps/server management
- Budget-conscious (marginal cost $0)
- Can leverage existing infrastructure
- Need maximum control
- Want to self-host tools (Supabase, n8n, Ollama)
- Willing to manage infrastructure

Example: Next.js + self-hosted Supabase, FastAPI + PostgreSQL, any containerized app

## Hosting Options Reference

This skill evaluates **five deployment options** relevant to small personal projects:

1. **Localhost** - Local development, utility apps
2. **Hostinger Shared Hosting** - Traditional web hosting
3. **Cloudflare Pages** - Edge-deployed static/JAMstack apps
4. **Fly.io** - Containerized global deployment with databases
5. **VPS with Docker Containers** (Hostinger) - Maximum control, self-managed

**Key Principle**: Code is portable. Deployment target is a separate decision. Build once, deploy multiple ways.

---

### 1. Localhost

**What it is**: Running application on personal computer, not publicly accessible.

**Best For**:
- Learning and experimentation
- Simple utility apps for personal use only
- Testing before committing to public deployment
- Apps with no distributed/global requirements

**Cost**: $0

**Tech Stacks**: Any (full freedom)

**Pros**:
- No infrastructure to manage
- Immediate feedback loop
- Full control over environment
- Perfect for breaking things and learning
- Can run any database locally

**Cons**:
- Can't access from outside your network
- Not suitable for production or shared use
- Dependent on your computer being on
- No global distribution
- No uptime guarantees

**Databases & Storage**:
- Run any database locally (PostgreSQL, MongoDB, SQLite)
- File system for storage
- No backup/redundancy concerns

**When to Recommend**:
- Personal utility app for your own use only
- Low-usage applications (single user or small group)
- Learning and experimentation phase
- Testing before public deployment
- No sharing or global distribution requirements
- Project is genuinely personal-scale

**Valid as Production**: Yes, for truly personal-use applications. Not every app needs public deployment.

**Migration Path**: Code transfers directly to any other option (Pages, Fly.io, VPS) when ready

---

### 2. Hostinger Shared Hosting

**What it is**: Traditional web hosting with cPanel interface, PHP/MySQL stack, FTP/SFTP file management.

**Best For**:
- Simple PHP-based websites
- WordPress sites
- Static websites
- Projects with minimal backend requirements
- Traditional LAMP stack applications

**Cost**: $0 marginal cost if account already exists, otherwise $3-5/month

**Tech Stacks**: PHP + MySQL only (no Node.js, Python, Docker)

**Pros**:
- $0 marginal cost if shared hosting account already exists
- Managed infrastructure (Hostinger handles servers)
- Click-based management via cPanel
- Easy to deploy (FTP, cPanel, Git integration)
- Pre-installed PHP, MySQL
- Minimal maintenance required
- Familiar interface (cPanel)
- Predictable performance for simple sites

**Cons**:
- Limited to PHP + MySQL
- No Docker support
- No custom runtime languages
- Shared resources (performance varies under heavy load)
- Limited control over server configuration
- Single region only
- Manual deployment workflow (though Git supported)

**Databases & Storage**:
- MySQL included
- Limited storage (depends on plan)
- File system via FTP
- Limited to MySQL (can't run PostgreSQL, MongoDB, etc.)

**Self-Hosted Infrastructure Compatibility**:
- ❌ Can't run Docker containers
- ❌ Can't self-host Supabase, n8n, Ollama
- ❌ Can't run custom services
- ✅ Can call external APIs (self-hosted VPS services via HTTP)

**When to Recommend**:
- Simple PHP-only application
- WordPress blogs or PHP CMS sites
- Basic company websites
- Static content sites with some PHP
- Already have shared hosting account ($0 marginal cost)
- Minimal maintenance tolerance
- Traditional web development approach preferred

---

### 3. Cloudflare Pages

**What it is**: Serverless platform for static sites and JAMstack applications deployed to Cloudflare's global edge network.

**Best For**:
- Static websites
- Single-page applications (React, Vue, Svelte)
- JAMstack applications
- Frontend-only projects
- Sites with simple serverless backend needs

**Cost**: $0 (genuinely free)
- Unlimited bandwidth
- Unlimited requests
- Unlimited builds
- No hidden costs

**Tech Stacks**: Any static generator, React, Vue, Next.js (static), Svelte, Astro

**Pros**:
- Zero-config deployment (git push to deploy)
- Zero-configuration for most frameworks
- Automatic framework detection
- Git-connected auto-deployment
- Automatic HTTPS
- Global CDN via Cloudflare edge network (blazing fast)
- Instant preview URLs for PR branches
- Build environment included
- Very generous free tier
- Integrated with Cloudflare DNS/CDN (John already uses!)

**Cons**:
- Static/frontend only (no persistent backend state)
- Build output must be static files
- Not suitable for persistent databases
- Limited to 100MB project size
- Pages Functions have 10MB code limit
- Learning curve for Pages Functions
- Not for traditional backends
- Limited execution time
- Cold start: ~250ms for first request
- Not ideal for real-time applications

**Databases & Storage**:
- Not suitable for persistent databases (stateless only)
- Can call external databases/APIs
- R2 object storage available ($0.015/GB)
- Can integrate with external database services

**Self-Hosted Infrastructure Compatibility**:
- ✅ Can call self-hosted APIs (Supabase, n8n via HTTP)
- ✅ Can fetch from self-hosted services
- ✅ Pages Functions can make HTTP calls to VPS
- ⚠️ Not ideal for heavy integration (latency across calls)

**Vendor Lock-In**: LOW
- Code is vanilla HTML/CSS/JavaScript
- Pages Functions are simplified (can port to other platforms)
- No proprietary APIs required
- Can mirror same project on other platforms
- Exit cost: Minimal (copy files)

**When to Recommend**:
- Frontend-only app (static/JAMstack)
- Need global performance
- Static site with edge logic
- Budget constraints (great free tier)
- Want zero cost
- Prioritize low vendor lock-in
- Already using Cloudflare DNS

**Decision Checklist**:
- Is your app primarily frontend? ✅
- Does it need databases? ❌ (use external service instead)
- Is it static or JAMstack? ✅
- Do you want zero cost? ✅
- Do you prioritize low vendor lock-in? ✅
- Do you need global distribution? ✅

---

### 4. Fly.io

**What it is**: Platform for deploying containerized applications globally across 35 regions with managed PostgreSQL databases.

**Best For**:
- Full-stack applications with backends
- Apps requiring real databases
- Global distribution with database replication
- Learning containerized deployment
- Applications that don't fit serverless constraints
- Backends paired with Cloudflare Pages frontend

**Cost**: $20-50/month for multiple small projects
- Shared CPU machine: $2.40/month
- PostgreSQL database: Free-$3/month (small)
- Volume storage: $0.15/GB/month
- Data egress: $0.02-0.12/GB
- Example 5-project setup: ~$30-40/month total

**Tech Stacks**: Any (Docker-based) - Node.js, Python, Go, Ruby, PHP, etc.

**Pros**:
- Docker-based (use existing container knowledge)
- 35 global regions (automatic regional distribution)
- Auto-scaling based on traffic
- Managed PostgreSQL with backups
- CLI-first deployment (`fly deploy`)
- Private networking between apps
- Free WireGuard VPN included
- Auto-generated Dockerfiles for popular frameworks
- Support many languages/frameworks
- Easy deployment

**Cons**:
- Requires Docker knowledge (container concepts)
- Pay-per-use pricing (less predictable)
- Data egress charges apply
- CLI-first workflow (not dashboard-heavy)
- Requires credit card on file
- Not ideal for extremely heavy compute (use VPS for that)

**Databases & Storage**:
- ✅ Full PostgreSQL support (connection pooling works)
- ✅ Volumes for persistent storage ($0.15/GB/month)
- ✅ Can integrate Redis/Upstash
- ✅ Managed backups included

**Self-Hosted Infrastructure Compatibility**:
- ✅ Can call self-hosted Supabase API
- ✅ Can trigger n8n workflows via HTTP
- ✅ Can call Ollama API on VPS for AI compute
- ✅ Can use self-hosted Redis
- Recommended pattern: Fly for frontend/APIs, VPS for heavy compute

**Vendor Lock-In**: LOW
- Standard Docker containers (portable anywhere)
- PostgreSQL is industry-standard
- Can run same Docker image locally or on other platforms
- Can migrate to VPS, Kubernetes, or other Docker hosts
- Exit cost: Minimal (containers work everywhere)

**When to Recommend**:
- Full-stack app needing database
- Backend-heavy applications
- Want PaaS but value portability
- Need global distribution
- Comfortable with Docker
- Need database included
- Want connection pooling to work
- Budget $20-50/month acceptable

**Decision Checklist**:
- Does your app need a database? ✅
- Is it backend-heavy? ✅
- Do you need global distribution? ✅
- Are you comfortable with Docker? ✅
- Do you prioritize low vendor lock-in? ✅
- Do you want connection pooling to work? ✅

**Learning Value**: Bridges VPS knowledge with global infrastructure (Fly Machines are containers, just like your Docker approach).

---

### 5. VPS with Docker Containers (Hostinger)

**What it is**: Virtual Private Server running Docker containers for complete control over infrastructure, databases, and deployment.

**Best For**:
- Full-stack applications with custom requirements
- Self-hosted infrastructure (Supabase, n8n, Ollama)
- Learning DevOps and infrastructure management
- Applications not fitting serverless constraints
- Maximum control and flexibility
- Long-term cost optimization at scale

**Cost**: $40-60/month VPS (John already has this - marginal cost $0)
- Hostinger VPS: $40-60/month
- Fixed monthly cost (predictable)
- Can run unlimited services on one VPS
- Additional costs: Domain, storage, bandwidth as needed

**Tech Stacks**: Any (Next.js, PHP, Python, Node.js, basically anything with Docker)

**Pros**:
- Full control over environment
- Use existing infrastructure (Supabase, PostgreSQL, etc.)
- Predictable costs
- Learning opportunity (maximum)
- Docker-based for consistency
- Full Linux environment (complete control)
- Docker/Docker Compose for containerization
- SSH key-based authentication
- Custom firewall rules
- Run any database
- Connection pooling works natively
- Can self-host Supabase, n8n, Ollama, Redis
- Unlimited customization
- Always-on compute

**Cons**:
- More maintenance responsibility
- Need to manage security updates
- Single point of failure (can add redundancy)
- Need to configure SSL, domains, etc.
- Requires DevOps knowledge for setup/maintenance
- No automatic scaling
- Responsible for security updates and patching
- More points of failure than managed platforms
- Deployment slower (build → push → SSH → pull → restart)
- Single geographic location (latency for distant users)
- Manual backups/disaster recovery (can automate)
- Learning curve is steep (worth it long-term)

**Databases & Storage**:
- ✅ Run any database (PostgreSQL, MySQL, MongoDB, etc.)
- ✅ Connection pooling works natively
- ✅ Full storage control
- ✅ Can self-host Supabase, n8n, Ollama
- ✅ Can run Redis, caching layers
- ✅ Unlimited customization

**Self-Hosted Infrastructure Compatibility**:
- ✅ Perfect for running Supabase Docker containers
- ✅ Perfect for running n8n
- ✅ Perfect for running Ollama with GPU access
- ✅ Perfect for running Redis
- ✅ Can run multiple isolated services simultaneously
- ✅ Full networking control

**Vendor Lock-In**: NONE
- Standard Docker containers (portable everywhere)
- Standard Linux commands
- Can migrate to any cloud provider or self-host
- Can clone/backup everything
- Exit cost: Zero (move containers and data anywhere)

**When to Recommend**:
- John already has VPS with capacity
- Tech stack runs well in Docker
- Traffic <10k daily users
- Learning is a goal (maximum learning value)
- Budget priority (marginal cost $0)
- Need maximum control
- Can manage own infrastructure
- Need to self-host tools
- Prioritize portability
- Want to avoid vendor lock-in
- Learning DevOps

**Decision Checklist**:
- Do you need maximum control? ✅
- Can you manage your own infrastructure? ✅
- Do you need to self-host tools? ✅
- Do you prioritize portability? ✅
- Do you want to avoid vendor lock-in? ✅
- Are you learning DevOps? ✅
- Do you have time for infrastructure management? ⚠️

**Learning Value**: Best for understanding full-stack development, containerization, DevOps workflows, and infrastructure management

## Common Deployment Patterns

These patterns demonstrate how to combine the 5 deployment options with different tech stacks:

### Pattern 1: Static/JAMstack Site (Frontend Only)

**Deployment Option**: Cloudflare Pages
**Tech Stack**: React, Vue, Svelte, Astro, Next.js (static)
**Database**: External API or none
**File Storage**: R2 or external service
**Deployment**: Git push (auto-deploy)
**Cost**: $0/month
**Best For**: Frontend-only apps, portfolios, documentation sites, landing pages

### Pattern 2: Full-Stack with Database (Self-Hosted)

**Deployment Option**: VPS with Docker Containers
**Tech Stack**: Next.js, FastAPI, PHP, Node.js (any)
**Database**: Self-hosted PostgreSQL/MySQL on same VPS
**File Storage**: Local VPS or Backblaze B2
**Deployment**: Git push → SSH → docker compose up
**Cost**: $0/month marginal (uses existing VPS)
**Best For**: Learning DevOps, full control, leveraging existing infrastructure

### Pattern 3: Full-Stack with Global Distribution

**Deployment Option**: Fly.io
**Tech Stack**: Next.js, FastAPI, Node.js, Go (any)
**Database**: Fly.io managed PostgreSQL
**File Storage**: Fly.io volumes or Backblaze B2
**Deployment**: `fly deploy` (CLI)
**Cost**: $20-50/month
**Best For**: Global performance, auto-scaling, managed infrastructure

### Pattern 4: Hybrid (Frontend + Self-Hosted Backend)

**Frontend**: Cloudflare Pages (global edge)
**Backend API**: VPS with Docker (self-hosted)
**Database**: Self-hosted PostgreSQL on VPS
**File Storage**: Backblaze B2
**Deployment**: Pages auto-deploy + VPS manual
**Cost**: $0/month marginal (uses existing VPS)
**Best For**: Best of both worlds - fast frontend, controlled backend

### Pattern 5: Simple PHP/WordPress Site

**Deployment Option**: Hostinger Shared Hosting
**Tech Stack**: PHP + MySQL
**Database**: MySQL on shared host
**File Storage**: Local storage
**Deployment**: FTP or Git
**Cost**: $3-5/month
**Best For**: Simple PHP apps, WordPress, lowest cost, minimal traffic

### Pattern 6: Local Development/Personal Utility

**Deployment Option**: Localhost
**Tech Stack**: Any
**Database**: Any (local PostgreSQL, SQLite, etc.)
**File Storage**: Local filesystem
**Deployment**: N/A (local only)
**Cost**: $0
**Best For**: Learning, experimentation, personal utilities, testing

## Teaching Moments

Use recommendations as opportunities to teach:

### Explain Deployment Trade-Offs
"Cloudflare Pages gives you zero-cost global deployment but only for static/frontend apps. Fly.io adds backend capabilities with auto-scaling for $20-50/month. VPS gives you full control for the cost you're already paying, but you manage everything..."

### Compare Managed vs Self-Hosted
"Fly.io managed PostgreSQL ($3/month) handles backups, scaling, and maintenance for you. Self-hosted on VPS ($0 marginal cost) requires you to manage it, but you learn DevOps skills and leverage infrastructure you already have..."

### Connect to Project Lifecycle
"Start with Cloudflare Pages for frontend-only apps (free). When you need a database, add VPS backend or use Fly.io. When you hit 10k+ users globally, consider Fly.io's multi-region deployment. VPS works great until you need global scale..."

### Reference Real-World Patterns
"Many developers start with static sites on Pages, add backends with Fly.io or VPS, then scale strategically. For learning projects, VPS teaches you the fundamentals. For production at scale, managed platforms handle complexity..."

### Highlight Learning Paths
"Localhost → Cloudflare Pages teaches frontend. Pages → VPS teaches full-stack + DevOps. VPS → Fly.io teaches managed infrastructure. Each step builds on previous knowledge..."

---

## Self-Hosted Infrastructure Context

**Key principle:** Evaluate all deployment options with honest trade-off analysis of available self-hosted infrastructure.

**Available Assets:**
- **Hostinger VPS8:** $40-60/month (8 cores, 32GB RAM, 400GB storage)
- **Docker/Docker Compose:** Containerized deployments
- **Supabase:** Self-hosted full stack (PostgreSQL, Auth, Storage, Realtime, REST API)
- **PocketBase:** Lightweight backend (SQLite, Auth)
- **PostgreSQL:** Available through Supabase or standalone
- **n8n:** Workflow automation
- **Ollama:** Local LLM inference
- **Caddy:** Reverse proxy with automatic HTTPS
- **Wiki.js:** Documentation and knowledge base platform
- **Cloudflare:** DNS and CDN

**Framework:**
1. **Discover:** What's running on existing VPS?
2. **Evaluate:** All hosting options (self-hosted + managed alternatives)
3. **Analyze:** Trade-offs with infrastructure context (marginal cost $0 if capacity exists)
4. **Recommend:** Honestly what's best for THIS project
5. **Explain:** How self-hosted infrastructure fits
6. **Empower:** User decides with full picture

**Examples:**
- Database: PostgreSQL self-hosted if already running and project fits → recommend it (genuine best fit, $0 marginal cost)
- Scaling: Self-hosted until traffic requires managed database → recommend migration point honestly
- Backup: Self-hosted backup tools vs managed options → present trade-offs transparently

**What I WON'T do:**
- ❌ Force self-hosting just because infrastructure exists
- ❌ Hide superior managed alternatives
- ❌ Recommend infrastructure that doesn't fit the project
- ❌ Ignore learning opportunities in either direction

---

## Workflow State Visibility

When you invoke this skill, you'll see your progress in the Skills workflow:

```
📍 Skills Phase 2 of 3: Deployment Strategy

Status:
  ✅ Phase 0: Project Brief (completed by project-brief-writer)
  ✅ Phase 1: Tech Stack (completed by tech-stack-advisor)
  🔵 Phase 2: Deployment Strategy (you are here)
  ⏳ Phase 3: Project Foundation (project-spinup)
```

This helps you understand your progress and what comes next.

---

## Version History

### v1.4 (2025-01-17)
**v1.0-ready UX refinements based on Gemini Pro 2.5 review**

Key changes:
- **Reduced LEARNING mode checkpoint questions from 5 to 3** (lines 53-56)
  - Question 1: Why does primary fit this project's core deployment needs?
  - Question 2: What is the single most important trade-off vs Alternative 1?
  - Question 3: What is the biggest maintenance responsibility or operational challenge?
  - 40% reduction in checkpoint burden while preserving pedagogical value
- Improves user experience without compromising learning outcomes

**Status:** v1.0-ready

### v1.3 (2025-11-17)
**Infrastructure Alignment & Workflow Refinement**

Key changes:
- Updated infrastructure list: removed Redis, added VPS8 specs, Caddy, PocketBase, Supabase details
- Revised localhost positioning: valid production option for personal-use/low-usage apps
- Revised shared hosting positioning: $0 marginal cost emphasis, removed negative framing
- Removed "Red Flags & Warnings" section (incompatible with collaborative refinement workflow)
- Fixed handoff document timing: created AFTER collaborative refinement, not before
- Aligned with homelab-setup-v2.md infrastructure stack

### v1.2 (2025-11-12)
**Deployment Options Alignment**

Corrected deployment options to align with canonical 5 options:
- Removed non-standard options (Vercel, Netlify, Railway, Render, DigitalOcean, Supabase Cloud)
- Aligned with deployment-recap.md: Localhost, Shared Hosting, Cloudflare Pages, Fly.io, VPS
- Updated all deployment patterns to reference only the 5 canonical options
- Updated recommendation logic and decision framework
- Updated teaching moments and examples
- Corrected "Hosting Advisor" heading to "Deployment Advisor"

### v1.1 (2025-11-11)
**Skills Workflow Refinement - Phase 4**

Key enhancements:
- Added allowed-tools guardrails for advisory-only mode
- Implemented 3-level checkpoint system (LEARNING/BALANCED/DELIVERY)
- Integrated PROJECT-MODE.md awareness
- Added self-hosted infrastructure evaluation framework
- Added workflow state visibility showing Skills Phase 2 of 3
- Enhanced infrastructure context integration

### v1.0 (2025-11-04)
**Initial Release**

Core functionality:
- Hosting recommendation framework
- Multiple deployment patterns
- Self-hosted VPS focus

---

## Further Reading

### Background Documentation
- **deployment-recap.md** - Five deployment options framework and decision matrix
- **INFRASTRUCTURE_REPO_README.md** - Self-hosted infrastructure details and setup
- **lovable-vs-claude-code.md** - Strategic decision-making and Phase 0 meta-skills philosophy
- **after-action-report.md** - Learning vs delivery trade-offs and workflow design

### Related Skills
- **project-brief-writer** - Creates PROJECT-MODE.md that determines checkpoint strictness (Phase 0)
- **tech-stack-advisor** - Provides tech stack decisions that inform hosting options (prerequisite)
- **project-spinup** - Scaffolds project with deployment configuration (next step)

### Workflow Integration
This is Phase 2 of 3 in the Skills workflow. Reads PROJECT-MODE.md to determine checkpoint level and provides hosting recommendations based on chosen tech stack. Evaluates self-hosted infrastructure alongside managed alternatives.

---

## Notes for Claude

When this skill is invoked:

1. **Gather tech stack details** - Can't recommend hosting without knowing what needs to run
2. **Consider John's VPS** - He already has infrastructure, leverage it when appropriate
3. **Budget-conscious** - John prefers cost-effective solutions, self-hosting when practical
4. **Learning-first** - Education may trump "easiest" option, explain learning opportunities
5. **Provide concrete steps** - Not just "deploy to VPS" but actual commands/workflow
6. **Cost transparency** - Show setup + monthly costs, compare alternatives
7. **Scaling guidance** - How to grow when project needs it
8. **Security matters** - Always include backup, monitoring, security considerations
9. **CREATE HANDOFF DOCUMENT** - Always write deployment-strategy.md with complete analysis

The goal is to provide John with a clear, actionable hosting plan that fits his tech stack, budget, infrastructure, and learning goals.

**CRITICAL WORKFLOW REQUIREMENT**: After formulating your recommendations, you MUST use the Write tool to create `deployment-strategy.md` in the current working directory. This file is essential for the project-spinup skill to function properly in a new session.

## Example Invocations

### Example 1: Static site deployment
```
User: "Use deployment-advisor skill for my React portfolio site"

→ Skill analyzes static site requirements, recommends Cloudflare Pages
(free, global CDN, instant deployment), provides git-based deployment workflow,
explains costs ($0), shows how to add Pages Functions if needed later.
```

### Example 2: Full-stack with database
```
User: "I'm building a Next.js + Supabase app. Should I self-host or use managed services?"

→ Skill recommends VPS with Docker (has Supabase already, marginal cost $0),
provides deployment workflow, but also presents Fly.io alternative if global
distribution becomes important ($20-50/mo, auto-scaling, managed).
```

### Example 3: Comparing deployment options
```
User: "For my FastAPI + PostgreSQL app, compare VPS vs Fly.io deployment"

→ Skill compares:
- VPS: Full control, $0/month marginal, self-hosted PostgreSQL, single region, requires DevOps knowledge
- Fly.io: Managed PostgreSQL, $20-50/month, 35 regions, auto-scaling, less maintenance
- Recommends based on priorities (learning + budget → VPS, global scale → Fly.io)
```

### Example 4: Simple PHP site
```
User: "Should I use shared hosting or VPS for a simple WordPress blog?"

→ Skill recommends Hostinger Shared Hosting ($3-5/mo, easiest for WordPress,
cPanel management), explains VPS would be overkill for this use case, shows
migration path if needs grow beyond shared hosting capabilities.
```

---

**Skill Version**: 1.2
**Created**: 2025-11-04
**Updated**: 2025-11-12
**For**: John's hosting decisions
**Use After**: tech-stack-advisor skill
**Use Before**: project-spinup skill
