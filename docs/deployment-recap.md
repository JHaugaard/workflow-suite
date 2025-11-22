# Deployment Options Recap

**Purpose**: Context document for Claude Code Skills and Agents development. Synthesizes deployment considerations for small, personal, non-commercial learning projects. Establishes the range of viable deployment options and decision criteria.

**Conversation Context**: Initial exploration of Cloudflare resources (Workers, Pages), comparison with existing VPS approach, evaluation of alternative platforms (Vercel, Netlify, Fly.io), and synthesis into actionable deployment framework.

---

## Overview: Five Deployment Options

This document evaluates five deployment approaches relevant to small personal projects:

1. **Localhost** - Local development, utility apps
2. **Hostinger Shared Hosting** - Traditional web hosting
3. **Cloudflare Pages** - Edge-deployed static/JAMstack apps
4. **Fly.io** - Containerized global deployment with databases
5. **VPS with Docker Containers** (Hostinger) - Maximum control, self-managed

**Key Principle**: Code is portable. Deployment target is a separate decision. Build once, deploy multiple ways.

---

## Deployment Options Matrix

### 1. LOCALHOST

**What it is**: Running application on personal computer, not publicly accessible.

**Best for**:
- Learning and experimentation
- Simple utility apps for personal use only
- Testing before committing to public deployment
- Apps with no distributed/global requirements

**Cost**: $0

**Key Characteristics**:
- No infrastructure to manage
- Immediate feedback loop
- Can't access from outside your network (unless explicitly configured)
- Full control over environment
- Perfect for breaking things and learning

**Databases & Storage**:
- Run any database locally (PostgreSQL, MongoDB, SQLite)
- File system for storage
- No backup/redundancy concerns

**GitHub Integration**: Not applicable (local only)

**Vendor Lock-In**: None (you own everything)

**Constraints**:
- Not suitable for production or shared use
- Dependent on your computer being on
- No global distribution
- No uptime guarantees

**Migration Path**: Code transfers directly to any other option (Pages, Fly.io, VPS)

---

### 2. HOSTINGER SHARED HOSTING

**What it is**: Traditional web hosting with cPanel interface, PHP/MySQL stack, FTP/SFTP file management.

**Best for**:
- Simple PHP-based websites
- WordPress sites
- Static websites
- Projects with minimal backend requirements
- Low-cost baseline option

**Cost**: $3-5/month

**Key Characteristics**:
- Managed infrastructure (Hostinger handles servers)
- Click-based management via cPanel
- File upload via FTP/SFTP
- Pre-installed PHP, MySQL
- Limited customization
- Single geographic location

**Databases & Storage**:
- MySQL included
- Limited storage (depends on plan)
- File system via FTP
- Limited to MySQL (can't run PostgreSQL, MongoDB, etc.)

**GitHub Integration**: Manual (no native CI/CD)
- Option: Deploy via FTP after local push
- No automatic deployments
- Manual version control

**Vendor Lock-In**: Low-to-Medium
- Code is standard PHP/HTML (portable)
- MySQL is standard (can migrate)
- Hosting-specific configurations exist
- Exit cost: Manual

**Self-Hosted Infrastructure Compatibility**:
- ❌ Can't run Docker containers
- ❌ Can't self-host Supabase, n8n, Ollama
- ❌ Can't run custom services
- Limited to what cPanel provides

**Constraints**:
- No Docker support
- No custom runtime languages easily
- Limited to PHP/MySQL stack
- Single region only
- No auto-scaling
- Manual deployment only
- Limited for development learning (too abstracted)

**Use Cases**:
- WordPress blogs
- Simple company websites
- Static content sites
- Educational starting point (see why better options exist)

---

### 3. CLOUDFLARE PAGES

**What it is**: Serverless platform for static sites and JAMstack applications deployed to Cloudflare's global edge network.

**Best for**:
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

**Key Characteristics**:
- Zero-configuration for most frameworks
- Automatic framework detection (Next.js, Astro, etc.)
- Git-connected auto-deployment
- Automatic HTTPS
- Global CDN via Cloudflare network
- Instant preview URLs for PR branches
- Build environment included

**Databases & Storage**:
- Not suitable for persistent databases (stateless only)
- Can call external databases/APIs
- R2 object storage available ($0.015/GB)
- Can integrate with external database services

**GitHub Integration**: Excellent (Native)
- Connect repo via OAuth
- Auto-deploy on push
- Preview URLs for PRs
- Build status in GitHub
- Zero configuration required

**Vendor Lock-In**: LOW
- Code is vanilla HTML/CSS/JavaScript
- Pages Functions are simplified (can port to other platforms)
- No proprietary APIs required
- Can mirror same project on Netlify/Vercel
- Exit cost: Minimal (copy files)

**Self-Hosted Infrastructure Compatibility**:
- ✅ Can call self-hosted APIs (Supabase, n8n via HTTP)
- ✅ Can fetch from self-hosted services
- ✅ Pages Functions can make HTTP calls to VPS
- ⚠️ Not ideal for heavy integration (latency across calls)

**Constraints**:
- Static/frontend only (no persistent backend state)
- Build output must be static files
- Not suitable for databases
- Limited to 100MB project size
- Pages Functions have 10MB code limit
- Cold start: ~250ms for first request
- Not ideal for real-time applications

**Decision Checklist**:
- Is your app primarily frontend? ✅
- Does it need databases? ❌ (use external service instead)
- Is it static or JAMstack? ✅
- Do you want zero cost? ✅
- Do you prioritize low vendor lock-in? ✅
- Do you need global distribution? ✅

---

### 4. FLY.IO

**What it is**: Platform for deploying containerized applications globally across 35 regions with managed PostgreSQL databases.

**Best for**:
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

**Key Characteristics**:
- Docker-based (use your existing container knowledge)
- 35 global regions (automatic regional distribution)
- Auto-scaling based on traffic
- Managed PostgreSQL with backups
- CLI-first deployment (`fly deploy`)
- Private networking between apps
- Free WireGuard VPN included
- Auto-generated Dockerfiles for popular frameworks

**Databases & Storage**:
- ✅ Full PostgreSQL support (connection pooling works)
- ✅ Volumes for persistent storage ($0.15/GB/month)
- ✅ Can integrate Redis/Upstash
- ✅ Managed backups included
- No KV store (use Upstash Redis instead)

**GitHub Integration**: Native (2024 feature)
- Auto-deploy on push
- Alternative: GitHub Actions with `flyctl`
- Slightly more config than Pages but still straightforward

**Vendor Lock-In**: LOW
- Standard Docker containers (portable anywhere)
- PostgreSQL is industry-standard
- Can run same Docker image locally or on other platforms
- Can migrate to VPS, Kubernetes, or other Docker hosts
- Exit cost: Minimal (containers work everywhere)

**Self-Hosted Infrastructure Compatibility**:
- ✅ Can call self-hosted Supabase API
- ✅ Can trigger n8n workflows via HTTP
- ✅ Can call Ollama API on VPS for AI compute
- ✅ Can use self-hosted Redis
- Recommended pattern: Fly for frontend/APIs, VPS for heavy compute

**Constraints**:
- Requires understanding Docker concepts
- CLI-first workflow (not dashboard-heavy)
- Pay-per-use pricing (less predictable than fixed VPS)
- Data egress charges apply
- Requires credit card on file

**Decision Checklist**:
- Does your app need a database? ✅
- Is it backend-heavy? ✅
- Do you need global distribution? ✅
- Are you comfortable with Docker? ✅
- Do you prioritize low vendor lock-in? ✅
- Do you want connection pooling to work? ✅

**Learning Value**: Bridges VPS knowledge with global infrastructure (Fly Machines are containers, just like your Docker approach).

---

### 5. VPS WITH DOCKER CONTAINERS (Hostinger)

**What it is**: Virtual Private Server running Docker containers for complete control over infrastructure, databases, and deployment.

**Best for**:
- Full-stack applications with custom requirements
- Self-hosted infrastructure (Supabase, n8n, Ollama)
- Learning DevOps and infrastructure management
- Applications not fitting serverless constraints
- Maximum control and flexibility
- Long-term cost optimization at scale

**Cost**: $3-5/month (VPS base cost only)
- Hostinger VPS: $3-5/month
- Additional costs: Domain, storage, bandwidth as needed
- Fixed monthly cost (predictable)
- Can run unlimited services on one VPS

**Key Characteristics**:
- Full Linux environment (complete control)
- Docker/Docker Compose for containerization
- Manual deployment via GitHub Actions + SSH
- SSH key-based authentication
- Custom firewall rules
- Single or multiple regions (your choice)
- No automatic scaling (manual resource allocation)
- Always-on compute (continuous cost)

**Databases & Storage**:
- ✅ Run any database (PostgreSQL, MySQL, MongoDB, etc.)
- ✅ Connection pooling works natively
- ✅ Full storage control
- ✅ Can self-host Supabase, n8n, Ollama
- ✅ Can run Redis, caching layers
- ✅ Unlimited customization

**GitHub Integration**: Manual (via GitHub Actions)
- Requires GitHub Actions workflow configuration (.yml)
- SSH keys, Docker registry auth, secrets management
- More manual than Pages/Fly.io
- Full control but higher complexity

**Vendor Lock-In**: NONE
- Standard Docker containers (portable everywhere)
- Standard Linux commands
- Can migrate to any cloud provider or self-host
- Can clone/backup everything
- Exit cost: Zero (move containers and data anywhere)

**Self-Hosted Infrastructure Compatibility**:
- ✅ Perfect for running Supabase Docker containers
- ✅ Perfect for running n8n
- ✅ Perfect for running Ollama with GPU access
- ✅ Perfect for running Redis
- ✅ Can run multiple isolated services simultaneously
- ✅ Full networking control

**Constraints**:
- Requires DevOps knowledge for setup/maintenance
- No automatic scaling
- Responsible for security updates and patching
- More points of failure than managed platforms
- Deployment slower (build → push → SSH → pull → restart)
- Single geographic location (latency for distant users)
- Manual backups/disaster recovery (can automate)
- Learning curve is steep (worth it long-term)

**Decision Checklist**:
- Do you need maximum control? ✅
- Can you manage your own infrastructure? ✅
- Do you need to self-host tools? ✅
- Do you prioritize portability? ✅
- Do you want to avoid vendor lock-in? ✅
- Are you learning DevOps? ✅
- Do you have time for infrastructure management? ⚠️

**Learning Value**: Best for understanding full-stack development, containerization, DevOps workflows, and infrastructure management.

---

## Comparative Matrix

| Factor | Localhost | Shared Hosting | Pages | Fly.io | VPS Docker |
|--------|-----------|---|---|---|---|
| **Cost** | $0 | $3-5/mo | $0 | $20-50/mo | $3-5/mo |
| **Databases** | Yes (local) | MySQL only | External only | ✅ Full PG | ✅ Any |
| **Global Distribution** | ❌ | ❌ | ✅ Automatic | ✅ 35 regions | ❌ Single |
| **GitHub CI/CD** | N/A | Manual | ✅ Native | ✅ Native | Manual (Actions) |
| **Vendor Lock-In** | None | Low | Low | Low | None |
| **Learning DevOps** | ❌ | ❌ | ⚠️ Limited | ✅ Some | ✅ Maximum |
| **Self-Hosted Tools** | ✅ Local | ❌ | ⚠️ API calls | ✅ Clean APIs | ✅ Native |
| **Scaling** | ❌ | ❌ | ✅ Auto | ✅ Auto | Manual |
| **Setup Time** | Minutes | Minutes | Minutes | 15-30 min | 1-2 hours |
| **Ongoing Mgmt** | None | Minimal | Minimal | Minimal | Moderate |

---

## Decision Framework for Claude Code

### Primary Decision Factors

**1. Database Requirement**:
- No database → Localhost, Pages, or Shared Hosting
- Simple database → Fly.io or VPS
- Complex queries → VPS
- Self-hosting Supabase → VPS only

**2. Scope of Application**:
- Frontend only → Pages
- Full-stack, simple → Fly.io
- Full-stack, complex → VPS
- Personal utility → Localhost

**3. Global Distribution Requirement**:
- Not needed → Localhost, Shared Hosting, VPS
- Nice to have → Pages, Fly.io
- Critical → Pages (static) or Fly.io (dynamic)

**4. Infrastructure Learning Goals**:
- Learning web development basics → Localhost, Pages
- Learning DevOps/containers → Fly.io, VPS
- Learning everything → VPS (comprehensive)
- Learning with managed services → Fly.io

**5. Cost Sensitivity**:
- Absolute minimum → Pages ($0)
- Low budget → Shared Hosting ($3-5/mo) or Pages
- Moderate → Fly.io ($20-50/mo)
- Fixed cost acceptable → VPS ($3-5/mo)

**6. Vendor Lock-In Tolerance**:
- Want to stay portable → Pages, Fly.io, VPS
- Okay with platform-specific → All options viable
- Deeply portable → VPS, Fly.io

### Recommended Deployment Decision Tree

```
START: Project Brief available?

→ Is app frontend-only (static/JAMstack)?
   YES → CLOUDFLARE PAGES
        (Cost: $0, simplest, global, low lock-in)
   NO  → Continue

→ Does app need persistent database?
   NO  → CLOUDFLARE PAGES or LOCALHOST
   YES → Continue

→ Do you need global distribution?
   YES → FLY.IO
        (Cost: $20-50/mo, managed, scalable, Docker)
   NO  → Continue

→ Do you want to self-host infrastructure?
   (Supabase, n8n, Ollama, Redis, etc.)
   YES → VPS WITH DOCKER
        (Cost: $3-5/mo, maximum control, learning value)
   NO  → FLY.IO
        (Cost: $20-50/mo, simpler than VPS)

→ Is this a personal utility app (local-only)?
   YES → LOCALHOST
   NO  → See above paths
```

### Constraints & Limitations Checklist

**For Claude Code Decision Making**:

When recommending a deployment option, flag these constraints:

**Localhost**:
- [ ] App runs only on your computer
- [ ] Not accessible to others
- [ ] Useful for learning/testing before production
- [ ] Can be final choice for personal utility apps

**Shared Hosting**:
- [ ] Limited to PHP/MySQL stack
- [ ] No Docker/custom containers
- [ ] Manual deployment only
- [ ] Single geographic location
- [ ] Not suitable for modern app development learning
- [ ] Good only for simple PHP/static sites

**Cloudflare Pages**:
- [ ] Frontend/static only (no persistent backend)
- [ ] Can call external databases via APIs
- [ ] Not suitable for real-time applications
- [ ] Project size limits (100MB output)
- [ ] Build output must be static files
- [ ] Best paired with external API services

**Fly.io**:
- [ ] Requires Docker knowledge (container concepts)
- [ ] Pay-per-use pricing (less predictable)
- [ ] Data egress charges apply
- [ ] Not ideal for extremely heavy compute (use VPS for that)
- [ ] Single database per app by default
- [ ] 6 TCP connections per request limit

**VPS with Docker**:
- [ ] Requires DevOps knowledge for setup/maintenance
- [ ] No automatic scaling
- [ ] Single geographic location (latency for distant users)
- [ ] Manual security updates required
- [ ] Steeper learning curve
- [ ] More infrastructure complexity
- [ ] Better long-term for comprehensive learning

---

## Self-Hosted Infrastructure Compatibility Matrix

For projects integrating self-hosted services (Supabase, n8n, Ollama, Redis):

| Service | Localhost | Shared | Pages | Fly.io | VPS |
|---------|-----------|--------|-------|--------|-----|
| **Supabase** | ✅ Native | ❌ | ⚠️ API calls | ✅ API calls | ✅ Native |
| **n8n** | ✅ Native | ❌ | ⚠️ Webhooks | ✅ Webhooks | ✅ Native |
| **Ollama** | ✅ Local GPU | ❌ | ❌ | ✅ Remote API | ✅ Native |
| **Redis** | ✅ Native | ❌ | ❌ | ⚠️ Upstash | ✅ Native |
| **Custom Services** | ✅ Any | ❌ | ⚠️ API only | ⚠️ API only | ✅ Any |

**Key Insight**: VPS allows running all self-hosted services natively. Fly.io can integrate via HTTP APIs. Pages can only integrate via HTTP APIs.

---

## Recommended Learning Progression

For someone building multiple small projects:

**Phase 1: Frontend Learning**
- Deploy to: Localhost → Cloudflare Pages
- Cost: $0
- Focus: HTML/CSS/JavaScript without infrastructure friction

**Phase 2: Backend Introduction**
- Deploy to: Fly.io with PostgreSQL
- Cost: ~$20-50/month for multiple projects
- Focus: Full-stack development, databases, APIs
- Learning: Container basics, managed infrastructure

**Phase 3: Infrastructure Control**
- Deploy to: VPS with Docker
- Cost: $3-5/month
- Focus: DevOps, containerization, self-hosted services
- Learning: Full infrastructure management

**Phase 4: Hybrid Approach**
- Use multiple options strategically:
  - Frontend on Cloudflare Pages or Fly.io
  - Heavy compute on VPS
  - APIs distributed as needed

---

## Key Principles for Claude Code

1. **Code Portability**: Application code is separate from deployment. Build once, consider deployment options as distinct decision.

2. **Start Simple**: Localhost → Pages → Fly.io/VPS. Don't over-engineer early projects.

3. **Lock-In Awareness**: Pages, Fly.io, and VPS all maintain reasonable exit costs. Shared Hosting less portable (avoid for learning).

4. **Self-Hosted Integration**: VPS is ideal for learning infrastructure. Fly.io bridges to global scale. Pages for frontend-only.

5. **Cost-Benefit**:
   - Pages: Free but limited to frontend
   - Fly.io: $20-50/mo but includes global + databases
   - VPS: $3-5/mo but requires more management

6. **Learning Goals Drive Decision**: Prioritize learning value if cost is not constraining.

---

## Questions Deployment Skill Should Ask Project

When evaluating a project brief, the Deployment Skill should determine:

1. **Backend Requirement**: Does app need persistent state/databases?
2. **Global Need**: Does app require global/multi-region distribution?
3. **Infrastructure**: Does project self-host tools (Supabase, n8n, Ollama)?
4. **Learning Goal**: What infrastructure concepts should developer learn?
5. **Scale Timeline**: Will project scale beyond current small-project scope?
6. **Cost Budget**: Is cost a constraining factor, or learning value primary?
7. **Time to Deploy**: Does project prioritize speed-to-deployment or control?

Answers to these questions map cleanly to deployment recommendations.

---

## Summary

**Five viable deployment options for small, personal, learning projects:**

- **Localhost**: Development, local utilities, testing
- **Shared Hosting**: Traditional hosting (avoid for modern learning)
- **Cloudflare Pages**: Frontend/JAMstack, zero cost, global, low lock-in
- **Fly.io**: Full-stack, global, databases, moderate cost, low lock-in
- **VPS with Docker**: Maximum control, self-hosted infrastructure, learning value

**Recommended default path**: Localhost → Pages → Fly.io/VPS depending on project requirements and learning goals.

**Key insight for Skills/Agents**: Deployment decision is driven by project characteristics (backend need, scale, infrastructure) and learning goals (DevOps, infrastructure, simplicity). No single "best" option; each serves specific use cases.
