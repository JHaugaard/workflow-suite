---
name: deployment-advisor
description: Recommend hosting strategy based on chosen tech stack and project needs. Provides deployment workflow, cost breakdown, and scaling path. Considers Hostinger infrastructure, budget, and complexity tolerance. Use after tech-stack-advisor, before project-spinup skill.
allowed-tools: [Read, Grep, Glob, WebSearch, Write]
---

# Hosting Advisor Meta-Skill

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

**After recommendations are presented, you'll answer 5 comprehension questions:**
- Question 1: Explain why the primary recommendation fits your tech stack
- Question 2: What's a key difference between self-hosted and PaaS?
- Question 3: When would you choose the Alternative hosting instead?
- Question 4: What are the main maintenance responsibilities?
- Question 5: How does this deployment strategy align with your project's scale?

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
- **Hostinger VPS(s)**: Already has self-hosted infrastructure
  - Supabase, PostgreSQL, n8n, Ollama, Redis, Nginx
  - Docker-based setup
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

### Step 4: Output Format

Use this structured format:

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
• Managed PaaS (Vercel/Heroku): ${X}/month (convenience premium)
• Full cloud (AWS/GCP): ${Y}/month (more features, complexity)
• Shared hosting: ${Z}/month (cheaper but limited)

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

3. Document hosting decision:
   → Save this recommendation for reference
   → Include in project README
   → Add to infrastructure documentation

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

**Choose Existing Hostinger VPS When**:
- Tech stack runs well in Docker
- Traffic is low-to-moderate
- Want to learn server management
- Budget-conscious
- Can leverage existing infrastructure

Example: Next.js + Supabase, PHP + MySQL, FastAPI + PostgreSQL

**Choose Hostinger Shared Hosting When**:
- Simple PHP application
- Very low traffic
- Minimal maintenance desired
- No Docker/container needs
- Lowest cost priority

Example: Simple PHP blog, WordPress site

**Choose PaaS (Vercel, Netlify, etc.) When**:
- Want zero-config deployment
- Willing to pay convenience premium
- Need global CDN out-of-box
- Serverless functions sufficient
- Development speed is priority

Example: Next.js on Vercel, static sites on Netlify

**Choose Managed Database When**:
- Database is mission-critical
- Want automated backups/scaling
- Outgrew VPS database capacity
- Can afford $15-50/month
- Don't want to manage database

Example: Supabase Cloud, PlanetScale, Neon

**Choose Cloud Provider (AWS, GCP, Azure) When**:
- Need specific cloud services
- Scaling beyond VPS capabilities
- Enterprise-level reliability required
- Budget for complexity and cost
- Team has cloud expertise

Example: High-traffic production apps, complex architectures

## Hosting Options Reference

### Self-Hosted on Hostinger VPS

**Best For**: Most full-stack applications, learning projects, budget-conscious
**Tech Stacks**: Next.js, PHP, Python, Node.js, basically anything with Docker
**Cost**: $40-60/month VPS (John already has this)
**Pros**:
- Full control over environment
- Use existing infrastructure (Supabase, PostgreSQL, etc.)
- Predictable costs
- Learning opportunity
- Docker-based for consistency

**Cons**:
- More maintenance responsibility
- Need to manage security updates
- Single point of failure (can add redundancy)
- Need to configure SSL, domains, etc.

**When to Recommend**:
- John already has VPS with capacity
- Tech stack runs well in Docker
- Traffic <10k daily users
- Learning is a goal
- Budget priority

### Hostinger Shared Hosting

**Best For**: Simple PHP applications, very low traffic
**Tech Stacks**: PHP + MySQL only (no Node.js, Python, Docker)
**Cost**: $2-10/month
**Pros**:
- Very cheap
- Easy to deploy (FTP, cPanel)
- Minimal maintenance
- Good for PHP/WordPress

**Cons**:
- Limited to PHP + MySQL
- Shared resources (performance varies)
- No Docker or custom services
- Limited control

**When to Recommend**:
- Simple PHP-only application
- Personal project, very low traffic
- Absolute lowest cost needed
- No backend complexity

### Vercel (Next.js Focus)

**Best For**: Next.js applications with serverless backend
**Tech Stacks**: Next.js, React, static sites
**Cost**: $0-20/month (generous free tier)
**Pros**:
- Zero-config deployment (git push to deploy)
- Global CDN included
- Serverless functions
- Preview deployments
- Excellent DX

**Cons**:
- Vendor lock-in (some Next.js features)
- Costs scale with traffic
- Limited to JavaScript/TypeScript
- No persistent servers (must use serverless)

**When to Recommend**:
- Using Next.js (best-in-class support)
- Want fastest deployment
- Serverless architecture acceptable
- Willing to pay for convenience
- Budget allows $0-50/month

### Netlify (Static Sites + Functions)

**Best For**: Static sites, JAMstack apps
**Tech Stacks**: Any static generator, React, Vue, serverless functions
**Cost**: $0-20/month (generous free tier)
**Pros**:
- Great for static sites
- Global CDN
- Serverless functions
- Forms and identity built-in
- Easy deployment

**Cons**:
- Primarily for static/JAMstack
- Function execution limits on free tier
- Not great for traditional backends
- Costs increase with traffic

**When to Recommend**:
- Static or mostly-static site
- JAMstack architecture
- Serverless functions sufficient
- Want easy deployment

### Railway / Render (PaaS Options)

**Best For**: Full-stack apps with databases, any language
**Tech Stacks**: Node.js, Python, Go, Ruby, etc. + PostgreSQL/MySQL
**Cost**: $5-50/month (usage-based)
**Pros**:
- Support many languages/frameworks
- Managed databases included
- Easy deployment (git push)
- Docker support
- Reasonable pricing

**Cons**:
- Less mature than Heroku/Vercel
- Pricing can be unpredictable
- Smaller community
- May have growing pains

**When to Recommend**:
- Want PaaS but not Heroku prices
- Need database included
- Tech stack needs beyond Vercel/Netlify
- Comfortable with newer platforms
- Budget $20-100/month

### DigitalOcean App Platform

**Best For**: Full-stack apps, similar to Railway/Render
**Tech Stacks**: Node.js, Python, PHP, Go, Ruby, etc.
**Cost**: $5-50/month
**Pros**:
- DigitalOcean reliability
- Managed databases available
- Simpler than raw VPS
- Static sites free tier

**Cons**:
- More expensive than raw DO Droplet
- Less flexible than VPS
- Pricing per service

**When to Recommend**:
- Want managed platform
- Like DigitalOcean ecosystem
- Need database + app hosting
- $25-100/month budget

### Cloudflare Pages + Workers

**Best For**: Static sites, edge computing, global performance
**Tech Stacks**: Static sites, serverless edge functions
**Cost**: $0-20/month (generous free tier)
**Pros**:
- Global edge network (blazing fast)
- Very generous free tier
- Workers for server logic
- Integrated with Cloudflare DNS/CDN

**Cons**:
- Learning curve for Workers
- Not for traditional backends
- Limited execution time
- Worker storage is KV (not SQL)

**When to Recommend**:
- Need global performance
- Static site with edge logic
- Budget constraints (great free tier)
- Already using Cloudflare DNS (John is!)

### Supabase Cloud (Managed Backend)

**Best For**: Apps using Supabase, don't want to self-host
**Tech Stacks**: Any frontend + Supabase backend
**Cost**: $0-25/month (free tier, then $25/month Pro)
**Pros**:
- Managed Supabase (no self-hosting hassle)
- Database, auth, storage, realtime included
- Generous free tier
- Automatic backups and scaling

**Cons**:
- John already self-hosts Supabase (paying for redundant)
- Free tier limitations (500MB database, 1GB storage)
- $25/month jumps quickly to $100+ with growth
- Some vendor lock-in

**When to Recommend**:
- Don't want to self-host database
- Need auth + database + storage quickly
- Project may need managed services
- Can afford $25/month+

## Common Deployment Patterns

### Pattern 1: Next.js + Supabase (Self-Hosted)

**Hosting**: Hostinger VPS (Docker)
**Database**: Self-hosted Supabase on same VPS (John has this)
**File Storage**: Supabase Storage or Backblaze B2
**Deployment**: Git push → SSH to VPS → docker compose up
**Cost**: $0/month (uses existing VPS)
**Best For**: Learning, full control, leveraging existing infrastructure

### Pattern 2: Next.js + Supabase (Hybrid)

**Hosting**: Vercel (frontend)
**Database**: Self-hosted Supabase on Hostinger VPS
**File Storage**: Backblaze B2
**Deployment**: Git push to Vercel (auto-deploy)
**Cost**: $0-20/month (Vercel free tier + existing VPS)
**Best For**: Easy frontend deployment, keep backend control

### Pattern 3: Next.js + Supabase (Fully Managed)

**Hosting**: Vercel (frontend)
**Database**: Supabase Cloud (managed)
**File Storage**: Supabase Storage
**Deployment**: Git push (auto-deploy)
**Cost**: $0-45/month ($0-20 Vercel + $0-25 Supabase)
**Best For**: Fastest setup, minimal maintenance, willing to pay

### Pattern 4: PHP + MySQL (Shared Hosting)

**Hosting**: Hostinger Shared
**Database**: MySQL on same host
**File Storage**: Local storage
**Deployment**: FTP or Git
**Cost**: $2-10/month
**Best For**: Simple PHP apps, lowest cost, minimal traffic

### Pattern 5: PHP + MySQL (VPS)

**Hosting**: Hostinger VPS (Docker or native)
**Database**: MySQL on same VPS
**File Storage**: Local or Backblaze B2
**Deployment**: Git push → SSH → deploy script
**Cost**: $0/month (uses existing VPS)
**Best For**: Learning PHP, full control, Docker practice

### Pattern 6: FastAPI + PostgreSQL (VPS)

**Hosting**: Hostinger VPS (Docker)
**Database**: PostgreSQL on same VPS (John has this)
**File Storage**: Backblaze B2
**Deployment**: Git push → SSH → docker compose up
**Cost**: $0/month (uses existing VPS)
**Best For**: API-first, Python projects, data-heavy apps

### Pattern 7: Static Site + Serverless Functions

**Hosting**: Cloudflare Pages or Netlify
**Backend**: Serverless functions (Cloudflare Workers / Netlify Functions)
**Database**: Supabase or PlanetScale (serverless-friendly)
**File Storage**: Backblaze B2 or provider storage
**Deployment**: Git push (auto-deploy)
**Cost**: $0-25/month
**Best For**: JAMstack, API calls, simple backend logic

## Red Flags & Warnings

### When to Push Back

**User wants expensive managed services for learning project**:
```
CONCERN: Paying $100/month for AWS when VPS would work
INSTEAD: Recommend self-hosted on existing VPS
REASON: Learning goal + budget consciousness + has infrastructure
```

**User wants self-hosted for production SaaS with uptime needs**:
```
CONCERN: Single VPS for business-critical app
INSTEAD: Recommend managed database + redundancy at minimum
REASON: Downtime = lost revenue, needs reliability
```

**User wants complex Kubernetes setup for simple CRUD app**:
```
CONCERN: Massive overkill for project scale
INSTEAD: Recommend simple Docker Compose on VPS
REASON: Maintenance burden, learning curve not justified
```

**User wants shared hosting for Next.js app**:
```
CONCERN: Shared hosting doesn't support Node.js
INSTEAD: Recommend VPS or Vercel
REASON: Tech stack incompatibility
```

## Teaching Moments

Use recommendations as opportunities to teach:

### Explain Deployment Trade-Offs
"PaaS like Vercel gives you push-to-deploy convenience, but you pay a premium and give up some control. VPS gives you full control and better pricing, but you manage everything..."

### Compare Managed vs Self-Hosted
"Managed database ($25/month) handles backups, scaling, and maintenance for you. Self-hosted ($0 on your VPS) requires you to manage it, but you learn those skills and save money..."

### Connect to Project Lifecycle
"Start on VPS for learning and low traffic. When you hit 10k users and performance matters, migrate database to managed service. At 100k users, consider multi-region cloud setup..."

### Reference Real-World Patterns
"Most startups begin on VPS or PaaS, scale with managed databases, then migrate to cloud providers when reaching scale. You're following that natural progression..."

---

## Self-Hosted Infrastructure Context

**Key principle:** Evaluate all deployment options with honest trade-off analysis of available self-hosted infrastructure.

**Available Assets:**
- **Hostinger VPS:** $40-60/month (likely already running services)
- **Docker:** Containerized deployments
- **PostgreSQL:** Self-hosted database option
- **n8n:** Workflow automation
- **Ollama:** Local LLM inference
- **Redis:** Caching and queues
- **Nginx:** Reverse proxy
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

The goal is to provide John with a clear, actionable hosting plan that fits his tech stack, budget, infrastructure, and learning goals.

## Example Invocations

### Example 1: From tech-stack-advisor output
```
User: "Use deployment-advisor skill for the Next.js + Supabase stack we just decided on"

→ Skill analyzes Next.js + Supabase requirements, recommends self-hosted on
existing VPS since John has Supabase already, provides Docker deployment
workflow, explains costs ($0 uses existing), shows scaling path.
```

### Example 2: Exploring alternatives
```
User: "I'm building a FastAPI project with PostgreSQL. Should I self-host or
use a managed service? Traffic will be moderate (500-1000 daily users)."

→ Skill recommends self-hosted on VPS (has PostgreSQL, moderate traffic fits),
provides deployment workflow, but also explains when managed database makes
sense (if traffic 10x, if mission-critical, if want auto-scaling).
```

### Example 3: Comparing options
```
User: "For my Next.js app, compare hosting on Vercel vs my VPS"

→ Skill compares:
- Vercel: Easy deployment, $0-20/month, limited backend, fast global CDN
- VPS: Full control, $0/month (existing), Docker workflow, more maintenance
- Recommends based on priorities (learning → VPS, speed → Vercel)
```

---

**Skill Version**: 1.0
**Created**: 2025-11-04
**For**: John's hosting decisions
**Use After**: tech-stack-advisor skill
**Use Before**: project-spinup skill
