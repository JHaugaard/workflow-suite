# Haugaard Homelab Infrastructure Setup Guide

**VPS:** VPS8 (Hostinger KVM 8)
**Specifications:** 8 cores, 32GB RAM, 400GB storage
**SSH Access:** `ssh vps8`
**Base Domain:** haugaard.dev
**Deployment Strategy:** Docker containers with Caddy reverse proxy
**Backup Strategy:** Backblaze B2 (S3-compatible)

**Last Updated:** November 16, 2025
**Version:** 2.0

**Changes from v1.0:**
- Switched to official Supabase full stack (production-tested configuration)
- Added UFW firewall prerequisite (critical security hardening)
- Removed Redis (YAGNI principle - can be added later if needed)
- Fixed all critical security and backup issues
- Production-ready configuration with industry-standard practices

---

## Table of Contents

1. [Introduction](#introduction)
2. [VPS8 Architecture Overview](#vps8-architecture-overview)
3. [Prerequisites](#prerequisites)
4. [Phase 1: Caddy (Reverse Proxy)](#phase-1-caddy-reverse-proxy)
5. [Phase 2: Supabase](#phase-2-supabase)
6. [Phase 3: n8n](#phase-3-n8n)
7. [Phase 4: Wiki.js](#phase-4-wikijs)
8. [Phase 5: Ollama](#phase-5-ollama)
9. [Phase 6: PocketBase](#phase-6-pocketbase)
10. [Monitoring & Health Checks](#monitoring--health-checks)
11. [Backup Strategy](#backup-strategy)
12. [Quick Reference](#quick-reference)

---

## Introduction

This guide provides a step-by-step roadmap for deploying the Haugaard Homelab infrastructure stack on VPS8. The deployment follows a deliberate sequence, with each service building upon the foundation established by previous components.

**Infrastructure Stack (in deployment order):**
1. **Caddy** - Reverse proxy with automatic HTTPS
2. **Supabase** - Complete backend platform (PostgreSQL, Auth, REST API, Realtime)
3. **n8n** - Workflow automation
4. **Wiki.js** - Documentation and knowledge management
5. **Ollama** - Local LLM inference
6. **PocketBase** - Lightweight backend alternative

**Design Principles:**
- All services run in Docker containers for portability and isolation
- Caddy handles all external traffic and SSL/TLS certificates
- Named Docker volumes for data persistence with B2 backup integration
- Localhost-only service exposure (only Caddy faces the internet)
- Hybrid environment variable management (master + service-specific)
- Local development points to VPS8 infrastructure (no local service duplication)
- Production-tested configurations (official sources where available)

---

## VPS8 Architecture Overview

### Network Architecture

```
Internet (HTTPS)
    ↓
Caddy Reverse Proxy :80, :443
    ├─→ supabase.haugaard.dev → Supabase Kong :8000
    ├─→ n8n.haugaard.dev → n8n :5678
    ├─→ ollama.haugaard.dev → Ollama :11434
    ├─→ wikijs.haugaard.dev → Wiki.js :3000
    └─→ pocketbase.haugaard.dev → PocketBase :8090

Docker Network: homelab-net
    ├─ caddy (public: 80, 443)
    ├─ supabase-kong (internal: 8000)
    ├─ supabase-db (internal: 5432)
    ├─ supabase-auth (internal: 9999)
    ├─ supabase-rest (internal: 3000)
    ├─ supabase-realtime (internal: 4000)
    ├─ supabase-storage (internal: 5000)
    ├─ supabase-meta (internal: 8080)
    ├─ wikijs (internal: 3000)
    ├─ wikijs-db (internal: 5433)
    ├─ n8n (internal: 5678)
    ├─ ollama (internal: 11434)
    └─ pocketbase (internal: 8090)

Data Persistence
    ├─ Named Docker volumes (primary storage)
    ├─ Automated backups to Backblaze B2
    └─ Restore capability from B2
```

### Port Assignments

| Service | Internal Port | Host Binding | Public Access |
|---------|---------------|--------------|---------------|
| Caddy | 80, 443 | `0.0.0.0:80`, `0.0.0.0:443` | ✅ Internet-facing |
| Supabase Kong | 8000 | `127.0.0.1:8000` | ❌ Via Caddy only |
| Supabase PostgreSQL | 5432 | `127.0.0.1:5432` | ❌ Internal access only |
| n8n | 5678 | `127.0.0.1:5678` | ❌ Via Caddy only |
| Ollama | 11434 | `127.0.0.1:11434` | ❌ Via Caddy only |
| Wiki.js | 3000 | `127.0.0.1:3000` | ❌ Via Caddy only |
| Wiki.js PostgreSQL | 5433 | `127.0.0.1:5433` | ❌ Internal access only |
| PocketBase | 8090 | `127.0.0.1:8090` | ❌ Via Caddy only |

### Directory Structure

```
/home/john/homelab/
├── .env.master              # Shared environment variables
├── caddy/
│   ├── Caddyfile
│   └── docker-compose.yml
├── supabase/
│   ├── docker-compose.yml
│   ├── .env
│   └── volumes/
├── n8n/
│   ├── docker-compose.yml
│   └── .env
├── wikijs/
│   ├── docker-compose.yml
│   └── .env
├── ollama/
│   ├── docker-compose.yml
│   └── .env
├── pocketbase/
│   ├── docker-compose.yml
│   └── .env
└── scripts/
    ├── backup-to-b2.sh
    └── health-check.sh
```

---

## Prerequisites

### 1. Verify SSH Access

```bash
# Test SSH connection
ssh vps8

# Should connect without password
# Expected: john@srv1086450
```

### 2. DNS Configuration

Configure A records in Cloudflare (or your DNS provider) pointing to VPS8 IP (72.60.27.146):

```
supabase.haugaard.dev   → A → 72.60.27.146
n8n.haugaard.dev        → A → 72.60.27.146
ollama.haugaard.dev     → A → 72.60.27.146
wikijs.haugaard.dev     → A → 72.60.27.146
pocketbase.haugaard.dev → A → 72.60.27.146
```

**IMPORTANT: Cloudflare Proxy Configuration**

For Caddy to acquire Let's Encrypt certificates, DNS records must be in **DNS-only mode** (gray cloud) during initial setup.

**Recommended Configuration:**
```
supabase.haugaard.dev   → A → 72.60.27.146   [Gray Cloud - DNS only]
n8n.haugaard.dev        → A → 72.60.27.146   [Gray Cloud - DNS only]
ollama.haugaard.dev     → A → 72.60.27.146   [Gray Cloud - DNS only]
wikijs.haugaard.dev     → A → 72.60.27.146   [Gray Cloud - DNS only]
pocketbase.haugaard.dev → A → 72.60.27.146   [Gray Cloud - DNS only]
```

**Why DNS-only?**
- Caddy uses HTTP-01 ACME challenge for Let's Encrypt
- Cloudflare proxy intercepts this challenge
- Certificates fail to issue

**Verify DNS propagation:**
```bash
# From your local machine
nslookup supabase.haugaard.dev
nslookup n8n.haugaard.dev
# Should all return: 72.60.27.146
```

### 3. Backblaze B2 Account Setup

**Create B2 Account:**
1. Visit [backblaze.com](https://backblaze.com)
2. Create account (free tier: 10GB storage)

**Create B2 Buckets:**
- `haugaard-homelab-backups` (for all database and volume backups)
- `haugaard-app-files` (for application file storage)

**Bucket Settings:**
- **Versioning**: Enable (allows point-in-time recovery)
- **Lifecycle Rules**: Archive files older than 90 days (cost optimization)
- **Privacy**: Private (only accessible with API key)

**Generate B2 Application Key:**
1. Go to B2 → App Keys
2. Click "Add Application Key"
3. Name: `haugaard-homelab-backups`
4. Bucket: Select `haugaard-homelab-backups`
5. Permissions: Read and Write
6. Copy **Key ID** and **Application Key** (shown only once)

**Store credentials securely** - you'll add these to `.env.master`

### 4. Create Base Directory Structure

```bash
# SSH to VPS8
ssh vps8

# Create homelab directory
mkdir -p /home/john/homelab/{caddy,supabase,n8n,wikijs,ollama,pocketbase,scripts}

# Create logs directory
mkdir -p /home/john/logs

# Verify structure
tree -L 1 /home/john/homelab
```

### 5. Install Docker (if not already installed)

```bash
# Check if Docker is installed
docker --version

# If not installed:
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh

# Add user to docker group
sudo usermod -aG docker john

# Log out and back in
exit
ssh vps8

# Verify Docker works without sudo
docker ps
```

### 6. Create Docker Network

```bash
# Create homelab network (all services will use this)
docker network create homelab-net

# Verify
docker network ls | grep homelab-net
```

### 7. Create Master Environment File

```bash
# Create master environment file
nano /home/john/homelab/.env.master
```

**Add the following:**
```env
# Domain Configuration
DOMAIN=haugaard.dev

# Backblaze B2 Configuration
B2_KEY_ID=your_b2_key_id_here
B2_APPLICATION_KEY=your_b2_application_key_here
B2_BACKUP_BUCKET=haugaard-homelab-backups
B2_REGION=us-west-004  # Or your B2 region

# Docker Network
HOMELAB_NETWORK=homelab-net

# Timezone
TZ=America/New_York  # Adjust to your timezone
```

**Secure the file:**
```bash
chmod 600 /home/john/homelab/.env.master
```

### 8. Configure Host Firewall (UFW)

**IMPORTANT:** Configure firewall BEFORE deploying any services.

**Install UFW (if not already installed):**
```bash
ssh vps8
sudo apt update
sudo apt install -y ufw
```

**Configure firewall rules:**
```bash
# Default policies
sudo ufw default deny incoming
sudo ufw default allow outgoing

# Allow SSH (CRITICAL: Do this first or you'll lock yourself out!)
sudo ufw allow 22/tcp

# Allow HTTP and HTTPS (for Caddy)
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp

# Enable firewall
sudo ufw enable

# Verify status
sudo ufw status verbose
```

**Expected output:**
```
Status: active
Logging: on (low)
Default: deny (incoming), allow (outgoing), disabled (routed)

To                         Action      From
--                         ------      ----
22/tcp                     ALLOW IN    Anywhere
80/tcp                     ALLOW IN    Anywhere
443/tcp                    ALLOW IN    Anywhere
```

**SECURITY NOTES:**
- Always configure SSH (port 22) before enabling UFW
- Docker bypasses UFW for container ports by default
- Since all services bind to 127.0.0.1, they won't be exposed even if UFW is bypassed
- Consider restricting SSH to your IP: `sudo ufw allow from YOUR_IP to any port 22`

**Optional: SSH rate limiting (prevent brute force):**
```bash
sudo ufw limit 22/tcp
```

### 9. Configure Global Docker Log Rotation

**IMPORTANT:** Prevent logs from filling disk over time.

**Create `/etc/docker/daemon.json`:**
```bash
sudo nano /etc/docker/daemon.json
```

**Add the following:**
```json
{
  "log-driver": "json-file",
  "log-opts": {
    "max-size": "10m",
    "max-file": "3"
  }
}
```

**Apply configuration:**
```bash
# Restart Docker daemon
sudo systemctl restart docker

# Verify configuration
docker info | grep -A 5 "Logging Driver"
```

**This ensures:**
- Each container limited to 30MB of logs (3 files × 10MB)
- Old logs automatically deleted
- No manual intervention required
- System-wide protection against disk fill

---

## Phase 1: Caddy (Reverse Proxy)

### Why Caddy First

Caddy is the foundation of the infrastructure - all other services will be accessed through it. It provides:
- Automatic HTTPS certificate management (Let's Encrypt)
- Reverse proxy routing based on domain names
- Security isolation (only Caddy faces the internet)

### Files to Create

**Directory structure:**
```
/home/john/homelab/caddy/
├── docker-compose.yml
└── Caddyfile
```

### Caddyfile Configuration

```bash
nano /home/john/homelab/caddy/Caddyfile
```

**Add the following:**
```caddyfile
# Global options
{
    email your-email@example.com
}

# Security headers snippet
(security) {
    header {
        Strict-Transport-Security "max-age=31536000; includeSubDomains; preload"
        X-Content-Type-Options "nosniff"
        X-Frame-Options "DENY"
        Referrer-Policy "strict-origin-when-cross-origin"
    }
}

# Supabase
supabase.haugaard.dev {
    import security
    reverse_proxy supabase-kong:8000
}

# n8n
n8n.haugaard.dev {
    import security
    reverse_proxy n8n:5678
}

# Ollama
ollama.haugaard.dev {
    import security
    reverse_proxy ollama:11434
}

# Wiki.js
wikijs.haugaard.dev {
    import security
    reverse_proxy wikijs:3000
}

# PocketBase
pocketbase.haugaard.dev {
    import security
    reverse_proxy pocketbase:8090
}
```

### Docker Compose Configuration

```bash
nano /home/john/homelab/caddy/docker-compose.yml
```

**Add the following:**
```yaml
version: '3.8'

services:
  caddy:
    image: caddy:2.7-alpine
    container_name: caddy
    restart: unless-stopped
    ports:
      - "80:80"      # HTTP
      - "443:443"    # HTTPS
    volumes:
      - ./Caddyfile:/etc/caddy/Caddyfile:ro
      - caddy_data:/data
      - caddy_config:/config
    networks:
      - homelab-net

volumes:
  caddy_data:
  caddy_config:

networks:
  homelab-net:
    external: true
```

### Claude Code Initial Prompt

```
I need to deploy Caddy reverse proxy to my VPS8 Homelab infrastructure.

Context:
- VPS8: 8 cores, 32GB RAM, 400GB storage
- SSH access: ssh vps8
- Base domain: haugaard.dev
- Subdomains configured in DNS: supabase, n8n, ollama, wikijs, pocketbase
- Docker network already created: homelab-net
- Directory: /home/john/homelab/caddy/

Requirements:
- Caddy 2.7 (Alpine image for minimal footprint)
- Automatic HTTPS certificate management
- Reverse proxy routing for 5 services
- Named Docker volumes for persistence
- Security headers (HSTS, X-Frame-Options, etc.)

Please help me:
1. Review the Caddyfile configuration at /home/john/homelab/caddy/Caddyfile
2. Review the docker-compose.yml at /home/john/homelab/caddy/docker-compose.yml
3. Start the Caddy container
4. Verify Caddy is running and accessible
5. Check certificate acquisition status
6. Test that Caddy responds on port 80 and 443

Reference files:
- @docs/homelab-setup-v2.md (this deployment guide)
```

### Manual Deployment Steps

```bash
# Navigate to Caddy directory
cd /home/john/homelab/caddy

# Start Caddy
docker compose up -d

# Verify container is running
docker ps | grep caddy

# Check logs (watch for certificate acquisition)
docker logs caddy -f
# Press Ctrl+C to exit logs

# Verify Caddy is listening
curl http://localhost
# Should return: "404 page not found" (expected - no backend services yet)
```

### Testing & Verification

```bash
# Test HTTP redirect to HTTPS
curl -I http://supabase.haugaard.dev
# Should return: 301 or 308 redirect to https://

# Test HTTPS (will fail until backend service is deployed, but cert should be acquired)
curl -I https://supabase.haugaard.dev
# May return: 502 Bad Gateway (expected - Supabase not deployed yet)
# Important: Should NOT return certificate errors

# Check Caddy configuration
docker exec caddy caddy validate --config /etc/caddy/Caddyfile

# View certificates
docker exec caddy ls -la /data/caddy/certificates/acme-v02.api.letsencrypt.org-directory/
```

### Troubleshooting

**Caddy won't start:**
```bash
# Check logs for errors
docker logs caddy

# Verify Caddyfile syntax
docker exec caddy caddy fmt /etc/caddy/Caddyfile

# Ensure ports 80 and 443 are not in use
sudo lsof -i :80
sudo lsof -i :443
```

**Certificate acquisition fails:**
```bash
# Ensure DNS is propagated
nslookup supabase.haugaard.dev

# Ensure ports 80 and 443 are accessible from internet
# Test from external machine:
curl http://72.60.27.146

# Verify UFW allows ports 80 and 443
sudo ufw status | grep -E "80|443"
```

---

## Phase 2: Supabase

### Why Supabase at This Stage

Supabase is your priority infrastructure component. Your existing apps require it for data persistence, authentication, and real-time features. Deploying it immediately after Caddy allows you to:
- Start testing your apps against production infrastructure
- Validate the reverse proxy routing
- Establish the database backup workflow to B2

### Architecture

Supabase is a complete backend platform with multiple services:
- **Kong** - API gateway (entry point)
- **PostgreSQL** - Database with pgvector extension
- **PostgREST** - Auto-generated REST API from database schema
- **GoTrue** - Authentication service
- **Realtime** - WebSocket server for real-time subscriptions
- **Storage** - File upload/download service
- **Meta** - Admin API for database management

**We'll deploy the official full stack** for production reliability and complete features.

### Files to Create

**Directory structure:**
```
/home/john/homelab/supabase/
├── docker-compose.yml
├── .env
└── volumes/  (created automatically by Docker)
```

### Environment Variables

```bash
nano /home/john/homelab/supabase/.env
```

**Generate required secrets:**
```bash
# JWT Secret (use this for all JWT keys below)
openssl rand -base64 32

# ANON and SERVICE_ROLE keys will be generated using Supabase CLI or online tool
# Visit: https://supabase.com/docs/guides/self-hosting/docker#securing-your-setup
```

**Add the following:**
```env
############
# Secrets
############
POSTGRES_PASSWORD=your-super-secure-postgres-password-here
JWT_SECRET=your-jwt-secret-from-openssl-above
ANON_KEY=your-generated-anon-key-here
SERVICE_ROLE_KEY=your-generated-service-role-key-here

############
# Database
############
POSTGRES_HOST=supabase-db
POSTGRES_DB=postgres
POSTGRES_PORT=5432

############
# API Proxy
############
KONG_HTTP_PORT=8000
KONG_HTTPS_PORT=8443

############
# API
############
PGRST_DB_SCHEMAS=public,storage,graphql_public

############
# Auth
############
SITE_URL=https://supabase.haugaard.dev
ADDITIONAL_REDIRECT_URLS=
JWT_EXPIRY=3600
DISABLE_SIGNUP=false
API_EXTERNAL_URL=https://supabase.haugaard.dev

############
# Email - Configure with your SMTP provider
############
SMTP_ADMIN_EMAIL=admin@haugaard.dev
SMTP_HOST=smtp.example.com
SMTP_PORT=587
SMTP_USER=your-smtp-user
SMTP_PASS=your-smtp-password
SMTP_SENDER_NAME=Haugaard Homelab

############
# Storage
############
STORAGE_BACKEND=file
FILE_SIZE_LIMIT=52428800

############
# Studio
############
STUDIO_PORT=3000

############
# Monitoring
############
LOGFLARE_API_KEY=your-logflare-key
LOGFLARE_URL=https://api.logflare.app
```

**Secure the file:**
```bash
chmod 600 /home/john/homelab/supabase/.env
```

### Docker Compose Configuration

**Download official Supabase docker-compose:**

```bash
cd /home/john/homelab/supabase

# Download official docker-compose.yml from Supabase
curl -o docker-compose.yml https://raw.githubusercontent.com/supabase/supabase/master/docker/docker-compose.yml

# Download example .env (for reference)
curl -o .env.example https://raw.githubusercontent.com/supabase/supabase/master/docker/.env.example
```

**Modify docker-compose.yml for homelab:**

```bash
nano docker-compose.yml
```

**Key modifications needed:**

1. **Change Kong port binding** (line ~60):
```yaml
# Change from:
# ports:
#   - ${KONG_HTTP_PORT}:8000

# To:
ports:
  - "127.0.0.1:8000:8000"
```

2. **Change PostgreSQL port binding** (line ~130):
```yaml
# Change from:
# ports:
#   - ${POSTGRES_PORT}:5432

# To:
ports:
  - "127.0.0.1:5432:5432"
```

3. **Add network to all services:**
```yaml
# Add to each service:
networks:
  - homelab-net

# Add at bottom of file:
networks:
  homelab-net:
    external: true
```

4. **Ensure all services use `restart: unless-stopped`**

### Claude Code Initial Prompt

```
I need to deploy Supabase (official full stack) to my VPS8 Homelab infrastructure.

Context:
- VPS8: 8 cores, 32GB RAM, 400GB storage
- SSH access: ssh vps8
- Caddy reverse proxy already running
- Domain: supabase.haugaard.dev (DNS configured)
- Docker network: homelab-net
- Directory: /home/john/homelab/supabase/

Requirements:
- Official Supabase full stack (production configuration)
- PostgreSQL with pgvector extension
- Kong API gateway, PostgREST, GoTrue, Realtime, Storage
- Database backups to Backblaze B2 (daily automated)
- All services bind to localhost (accessed via Caddy only)

Please help me:
1. Download official docker-compose.yml from Supabase repository
2. Modify port bindings for localhost-only access
3. Configure .env file with secure secrets
4. Add homelab-net network to all services
5. Start the Supabase stack
6. Verify all containers are running and healthy
7. Test API endpoint: https://supabase.haugaard.dev
8. Test REST API endpoint: https://supabase.haugaard.dev/rest/v1/
9. Set up B2 backup integration for PostgreSQL

Reference files:
- @docs/homelab-setup-v2.md (this deployment guide)
- Official docs: https://supabase.com/docs/guides/self-hosting/docker
```

### Manual Deployment Steps

```bash
# Navigate to Supabase directory
cd /home/john/homelab/supabase

# Start Supabase stack
docker compose up -d

# Verify containers are running
docker ps | grep supabase

# Check logs for each service
docker logs supabase-db
docker logs supabase-kong
docker logs supabase-auth
docker logs supabase-rest

# Wait for health checks to pass (may take 1-2 minutes)
docker compose ps
# All services should show "healthy" or "running" status
```

### Testing & Verification

```bash
# Test database connection (from VPS8)
docker exec -it supabase-db psql -U postgres -c "SELECT version();"

# Test pgvector extension
docker exec -it supabase-db psql -U postgres -c "CREATE EXTENSION IF NOT EXISTS vector;"
docker exec -it supabase-db psql -U postgres -c "SELECT * FROM pg_extension WHERE extname = 'vector';"

# Test API endpoint (from local machine)
curl https://supabase.haugaard.dev

# Test REST API
curl https://supabase.haugaard.dev/rest/v1/

# Test from local app (create test.js)
cat > test.js << 'EOF'
import { createClient } from '@supabase/supabase-js'

const supabase = createClient(
  'https://supabase.haugaard.dev',
  'your-anon-key-here'
)

// Test connection
const { data, error } = await supabase
  .from('test_table')
  .select('*')

console.log({ data, error })
EOF

node test.js
```

### B2 Backup Integration

**Install s3cmd on VPS8:**
```bash
ssh vps8

# Install s3cmd
sudo apt update
sudo apt install -y s3cmd

# Configure for Backblaze B2
s3cmd --configure

# When prompted:
# Access Key: [B2_KEY_ID from .env.master]
# Secret Key: [B2_APPLICATION_KEY from .env.master]
# S3 Endpoint: s3.us-west-004.backblazeb2.com
# DNS-style bucket: %(bucket)s.s3.us-west-004.backblazeb2.com
# Use HTTPS: Yes

# Test connection
s3cmd ls s3://haugaard-homelab-backups/
```

**Backup script will be created in Backup Strategy section below.**

### Create Dedicated Database User for n8n

**Important:** Don't use the postgres superuser for applications. Create dedicated users with limited privileges.

```bash
# Connect to PostgreSQL
docker exec -it supabase-db psql -U postgres

# Create dedicated user for n8n
CREATE USER n8n_user WITH PASSWORD 'strong-random-password-here';

# Create n8n database with proper owner
CREATE DATABASE n8n OWNER n8n_user;

# Grant only necessary privileges
GRANT ALL PRIVILEGES ON DATABASE n8n TO n8n_user;

# Prevent n8n from accessing other databases
REVOKE CONNECT ON DATABASE postgres FROM n8n_user;

# Exit psql
\q
```

**Save the n8n_user password** - you'll need it in Phase 3.

---

## Phase 3: n8n

### Why n8n at This Stage

With Caddy and Supabase operational, n8n can now:
- Monitor and automate backups for all infrastructure services
- Create workflows that interact with Supabase data
- Serve as the automation layer for the entire homelab

### Architecture

n8n will:
- Use Supabase's PostgreSQL for workflow persistence (dedicated database)
- Connect to other services via Docker network
- Be accessible via `n8n.haugaard.dev`

### Files to Create

**Directory structure:**
```
/home/john/homelab/n8n/
├── docker-compose.yml
└── .env
```

### Environment Variables

```bash
nano /home/john/homelab/n8n/.env
```

**Add the following:**
```env
# n8n Configuration
N8N_HOST=n8n.haugaard.dev
N8N_PORT=5678
N8N_PROTOCOL=https
WEBHOOK_URL=https://n8n.haugaard.dev/

# Encryption key (generate with: openssl rand -hex 32)
N8N_ENCRYPTION_KEY=your-encryption-key-here

# Database (use dedicated n8n_user created in Phase 2)
DB_TYPE=postgresdb
DB_POSTGRESDB_HOST=supabase-db
DB_POSTGRESDB_PORT=5432
DB_POSTGRESDB_DATABASE=n8n
DB_POSTGRESDB_USER=n8n_user
DB_POSTGRESDB_PASSWORD=strong-random-password-from-phase2

# Timezone
GENERIC_TIMEZONE=America/New_York
```

**Secure the file:**
```bash
chmod 600 /home/john/homelab/n8n/.env
```

### Docker Compose Configuration

```bash
nano /home/john/homelab/n8n/docker-compose.yml
```

**Add the following:**
```yaml
version: '3.8'

services:
  n8n:
    image: n8nio/n8n:1.19
    container_name: n8n
    restart: unless-stopped
    ports:
      - "127.0.0.1:5678:5678"
    env_file:
      - ../.env.master
      - .env
    volumes:
      - n8n_data:/home/node/.n8n
    networks:
      - homelab-net
    healthcheck:
      test: ["CMD-SHELL", "wget --no-verbose --tries=1 --spider http://localhost:5678/healthz || exit 1"]
      interval: 30s
      timeout: 10s
      retries: 3
      start_period: 60s

volumes:
  n8n_data:

networks:
  homelab-net:
    external: true
```

### Claude Code Initial Prompt

```
I need to deploy n8n workflow automation to my VPS8 Homelab infrastructure.

Context:
- VPS8: 8 cores, 32GB RAM, 400GB storage
- SSH access: ssh vps8
- Caddy reverse proxy already running
- Supabase/PostgreSQL already deployed
- Domain: n8n.haugaard.dev (DNS configured)
- Docker network: homelab-net
- Directory: /home/john/homelab/n8n/
- Dedicated n8n_user already created in PostgreSQL

Requirements:
- n8n version 1.19 (pinned)
- Use Supabase PostgreSQL with dedicated n8n_user (least privilege)
- Accessible via https://n8n.haugaard.dev
- Persistent workflow storage
- Healthcheck configured

Please help me:
1. Generate N8N_ENCRYPTION_KEY
2. Create the .env file with database connection using n8n_user
3. Create the docker-compose.yml
4. Start n8n service
5. Verify n8n is running and accessible
6. Test accessing https://n8n.haugaard.dev
7. Guide me through initial owner account setup

Reference files:
- @docs/homelab-setup-v2.md (this deployment guide)
```

### Manual Deployment Steps

```bash
# n8n database already created in Phase 2
# Navigate to n8n directory
cd /home/john/homelab/n8n

# Start n8n
docker compose up -d

# Verify container is running
docker ps | grep n8n

# Check logs
docker logs n8n -f
# Press Ctrl+C to exit
```

### Testing & Verification

```bash
# Test n8n API (from VPS8)
curl http://localhost:5678/healthz

# Test via Caddy (from local machine)
curl https://n8n.haugaard.dev

# Access n8n UI
# Open browser: https://n8n.haugaard.dev
# First visit: Create owner account

# Test database connection
# In n8n UI: Create a simple workflow that writes to PostgreSQL
# Credentials > Add > Postgres
# Host: supabase-db
# Database: postgres
# User: postgres
# Password: [your Supabase password]
```

### Example n8n Workflow: Daily Database Backup

Once n8n is running, create this workflow:

**Workflow: Automated Database Backup to B2**
1. **Schedule Trigger** - Daily at 2 AM
2. **Execute Command** - Run backup script via SSH
3. **PostgreSQL** - Log backup metadata to database
4. **HTTP Request** - (Optional) Send notification to Discord/Slack

---

## Phase 4: Wiki.js

### Why Wiki.js at This Stage

With core infrastructure (Caddy, Supabase, n8n) operational, Wiki.js provides:
- Documentation platform to record your homelab setup steps
- Knowledge base for troubleshooting and procedures
- Search functionality for your documentation

### Architecture

Wiki.js will:
- Use its own dedicated PostgreSQL container (isolated from Supabase)
- Be accessible via `wikijs.haugaard.dev`
- Store documentation in PostgreSQL with full-text search

### Files to Create

**Directory structure:**
```
/home/john/homelab/wikijs/
├── docker-compose.yml
└── .env
```

### Environment Variables

```bash
nano /home/john/homelab/wikijs/.env
```

**Add the following:**
```env
# Wiki.js Configuration
WIKIJS_PORT=3000

# Database Configuration (dedicated PostgreSQL)
DB_TYPE=postgres
DB_HOST=wikijs-db
DB_PORT=5432
DB_USER=wikijs
DB_PASS=your-secure-wikijs-password-here
DB_NAME=wikijs

# PostgreSQL Configuration
POSTGRES_USER=wikijs
POSTGRES_PASSWORD=your-secure-wikijs-password-here
POSTGRES_DB=wikijs
```

**Secure the file:**
```bash
chmod 600 /home/john/homelab/wikijs/.env
```

### Docker Compose Configuration

```bash
nano /home/john/homelab/wikijs/docker-compose.yml
```

**Add the following:**
```yaml
version: '3.8'

services:
  wikijs-db:
    image: postgres:15-alpine
    container_name: wikijs-db
    restart: unless-stopped
    ports:
      - "127.0.0.1:5433:5432"
    environment:
      POSTGRES_USER: ${POSTGRES_USER}
      POSTGRES_PASSWORD: ${POSTGRES_PASSWORD}
      POSTGRES_DB: ${POSTGRES_DB}
    volumes:
      - wikijs_db_data:/var/lib/postgresql/data
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U wikijs"]
      interval: 5s
      timeout: 5s
      retries: 5
    networks:
      - homelab-net

  wikijs:
    image: ghcr.io/requarks/wiki:2.5
    container_name: wikijs
    restart: unless-stopped
    ports:
      - "127.0.0.1:3000:3000"
    env_file:
      - ../.env.master
      - .env
    depends_on:
      - wikijs-db
    networks:
      - homelab-net
    healthcheck:
      test: ["CMD-SHELL", "wget --no-verbose --tries=1 --spider http://localhost:3000 || exit 1"]
      interval: 30s
      timeout: 10s
      retries: 3
      start_period: 90s

volumes:
  wikijs_db_data:

networks:
  homelab-net:
    external: true
```

### Claude Code Initial Prompt

```
I need to deploy Wiki.js documentation platform to my VPS8 Homelab infrastructure.

Context:
- VPS8: 8 cores, 32GB RAM, 400GB storage
- SSH access: ssh vps8
- Caddy reverse proxy already running
- Domain: wikijs.haugaard.dev (DNS configured)
- Docker network: homelab-net
- Directory: /home/john/homelab/wikijs/

Requirements:
- Wiki.js 2.5 (pinned version)
- Dedicated PostgreSQL 15 container (port 5433 to avoid conflict with Supabase)
- Accessible via https://wikijs.haugaard.dev
- Persistent storage for wiki content and database
- Healthchecks configured

Please help me:
1. Create the .env file with secure passwords
2. Create the docker-compose.yml with both Wiki.js and PostgreSQL
3. Start both services
4. Verify containers are running and healthy
5. Test accessing https://wikijs.haugaard.dev
6. Guide me through initial Wiki.js setup

Reference files:
- @docs/homelab-setup-v2.md (this deployment guide)
```

### Manual Deployment Steps

```bash
# Navigate to Wiki.js directory
cd /home/john/homelab/wikijs

# Start Wiki.js stack
docker compose up -d

# Verify containers are running
docker ps | grep wikijs

# Check logs
docker logs wikijs-db
docker logs wikijs

# Wait for initialization (check logs for "Server started successfully")
docker logs wikijs -f
```

### Testing & Verification

```bash
# Test database connection
docker exec -it wikijs-db psql -U wikijs -c "SELECT version();"

# Test Wiki.js (from VPS8)
curl http://localhost:3000

# Test via Caddy (from local machine)
curl https://wikijs.haugaard.dev

# Access Wiki.js UI
# Open browser: https://wikijs.haugaard.dev
# First visit: Complete setup wizard
#   - Administrator Email
#   - Password
#   - Site URL: https://wikijs.haugaard.dev
```

### Initial Wiki.js Setup

**After accessing `https://wikijs.haugaard.dev` for the first time:**

1. **Administrator Account**
   - Email: your-email@example.com
   - Password: Strong password
   - Confirm password

2. **Site Configuration**
   - Site Title: Haugaard Homelab Documentation
   - Site URL: https://wikijs.haugaard.dev

3. **Storage**
   - Keep default: PostgreSQL (already configured)

4. **First Page**
   - Create home page with your homelab overview

**Recommended first pages to create:**
- `/infrastructure/overview` - Homelab architecture diagram
- `/infrastructure/services` - Service inventory
- `/procedures/backup-restore` - Backup and restore procedures
- `/troubleshooting/common-issues` - Known issues and solutions

---

## Phase 5: Ollama

### Why Ollama at This Stage

With documentation (Wiki.js) in place, you can now deploy Ollama and document:
- Model management procedures
- API usage examples
- Performance benchmarks on VPS8

Ollama is resource-intensive, so deploying it later allows you to monitor resource usage after lighter services are stable.

### Architecture

Ollama will:
- Run LLM inference (CPU-based on VPS8)
- Be accessible via `ollama.haugaard.dev`
- Store models in persistent Docker volume

### Resource Considerations

**Performance Expectations on 8-core CPU (no GPU):**

| Model | RAM Usage | CPU Usage | Tokens/Second | Concurrency |
|-------|-----------|-----------|---------------|-------------|
| llama3.2:3b | ~2-4 GB | High (4-6 cores) | 10-25 tok/s | Single user OK |
| mistral:7b | ~5-10 GB | Very High (6-8 cores) | 3-10 tok/s | Limited |
| nomic-embed | ~500 MB | Low | Fast | Multiple OK |

**Important:** Ollama will saturate CPU during inference. This may impact other services temporarily.

### Files to Create

**Directory structure:**
```
/home/john/homelab/ollama/
├── docker-compose.yml
└── .env
```

### Environment Variables

```bash
nano /home/john/homelab/ollama/.env
```

**Add the following:**
```env
# Ollama Configuration
OLLAMA_HOST=0.0.0.0:11434
OLLAMA_ORIGINS=https://ollama.haugaard.dev

# Resource Management
OLLAMA_NUM_PARALLEL=1           # Limit concurrent requests
OLLAMA_MAX_LOADED_MODELS=1      # Keep only one model in memory
OLLAMA_KEEP_ALIVE=5m            # Unload model after 5min idle

# Model Storage
OLLAMA_MODELS=/root/.ollama/models
```

**Secure the file:**
```bash
chmod 600 /home/john/homelab/ollama/.env
```

### Docker Compose Configuration

```bash
nano /home/john/homelab/ollama/docker-compose.yml
```

**Add the following:**
```yaml
version: '3.8'

services:
  ollama:
    image: ollama/ollama:0.1
    container_name: ollama
    restart: unless-stopped
    ports:
      - "127.0.0.1:11434:11434"
    env_file:
      - ../.env.master
      - .env
    volumes:
      - ollama_models:/root/.ollama
    networks:
      - homelab-net
    deploy:
      resources:
        limits:
          cpus: '6'      # Leave 2 cores for other services
          memory: 16G    # Cap memory usage

volumes:
  ollama_models:

networks:
  homelab-net:
    external: true
```

### Claude Code Initial Prompt

```
I need to deploy Ollama LLM inference to my VPS8 Homelab infrastructure.

Context:
- VPS8: 8 cores, 32GB RAM, 400GB storage (no GPU, CPU inference only)
- SSH access: ssh vps8
- Caddy reverse proxy already running
- Domain: ollama.haugaard.dev (DNS configured)
- Docker network: homelab-net
- Directory: /home/john/homelab/ollama/

Requirements:
- Ollama version 0.1 (pinned)
- Accessible via https://ollama.haugaard.dev
- Persistent model storage
- CPU/memory limits to prevent starving other services
- Start with small models (7B parameters or less)

Please help me:
1. Create the .env file with resource management settings
2. Create the docker-compose.yml with CPU/memory limits
3. Start Ollama service
4. Verify Ollama is running and accessible
5. Pull a small test model (e.g., llama3.2:3b)
6. Test model inference
7. Test API endpoint: https://ollama.haugaard.dev/api/tags

Reference files:
- @docs/homelab-setup-v2.md (this deployment guide)
```

### Manual Deployment Steps

```bash
# Navigate to Ollama directory
cd /home/john/homelab/ollama

# Start Ollama
docker compose up -d

# Verify container is running
docker ps | grep ollama

# Check logs
docker logs ollama -f
```

### Testing & Verification

```bash
# Test Ollama API (from VPS8)
curl http://localhost:11434/api/tags

# Test via Caddy (from local machine)
curl https://ollama.haugaard.dev/api/tags

# Pull a small model
docker exec ollama ollama pull llama3.2:3b

# List installed models
docker exec ollama ollama list

# Test inference
docker exec ollama ollama run llama3.2:3b "Hello, what is 2+2?"

# Test API inference
curl https://ollama.haugaard.dev/api/generate -d '{
  "model": "llama3.2:3b",
  "prompt": "Why is the sky blue?",
  "stream": false
}'
```

### Model Management

**Recommended models for VPS8 (32GB RAM, CPU inference):**
- `llama3.2:3b` - Fast, good for general tasks (~2GB)
- `mistral:7b` - Better quality, slower (~4GB)
- `nomic-embed-text` - Embeddings for semantic search (~274MB)

**Pull models:**
```bash
docker exec ollama ollama pull llama3.2:3b
docker exec ollama ollama pull nomic-embed-text
```

**Remove models:**
```bash
docker exec ollama ollama rm llama3.2:3b
```

**Check model storage:**
```bash
docker exec ollama du -sh /root/.ollama/models
```

**Create model manifest for backup:**
```bash
docker exec ollama ollama list > /home/john/homelab/ollama/models-manifest.txt
```

---

## Phase 6: PocketBase

### Why PocketBase at This Stage

PocketBase is a lightweight, standalone backend that:
- Provides an alternative to Supabase for simpler projects
- Uses embedded SQLite (no separate database container needed)
- Is fast to deploy and test

### Architecture

PocketBase will:
- Run as a single container with embedded SQLite
- Be accessible via `pocketbase.haugaard.dev`
- Store data in persistent Docker volume

### Files to Create

**Directory structure:**
```
/home/john/homelab/pocketbase/
├── docker-compose.yml
└── .env
```

### Environment Variables

```bash
nano /home/john/homelab/pocketbase/.env
```

**Add the following:**
```env
# PocketBase Configuration
POCKETBASE_PORT=8090
```

**Secure the file:**
```bash
chmod 600 /home/john/homelab/pocketbase/.env
```

### Docker Compose Configuration

```bash
nano /home/john/homelab/pocketbase/docker-compose.yml
```

**Add the following:**
```yaml
version: '3.8'

services:
  pocketbase:
    image: ghcr.io/muchobien/pocketbase:0.20.0
    container_name: pocketbase
    restart: unless-stopped
    ports:
      - "127.0.0.1:8090:8090"
    env_file:
      - ../.env.master
      - .env
    volumes:
      - pocketbase_data:/pb/pb_data
    networks:
      - homelab-net
    healthcheck:
      test: ["CMD-SHELL", "wget --no-verbose --tries=1 --spider http://localhost:8090/api/health || exit 1"]
      interval: 30s
      timeout: 10s
      retries: 3
      start_period: 30s

volumes:
  pocketbase_data:

networks:
  homelab-net:
    external: true
```

### Claude Code Initial Prompt

```
I need to deploy PocketBase backend to my VPS8 Homelab infrastructure.

Context:
- VPS8: 8 cores, 32GB RAM, 400GB storage
- SSH access: ssh vps8
- Caddy reverse proxy already running
- Domain: pocketbase.haugaard.dev (DNS configured)
- Docker network: homelab-net
- Directory: /home/john/homelab/pocketbase/

Requirements:
- PocketBase version 0.20.0 (pinned)
- Embedded SQLite database
- Accessible via https://pocketbase.haugaard.dev
- Persistent data storage
- Healthcheck configured

Please help me:
1. Create the .env file
2. Create the docker-compose.yml
3. Start PocketBase service
4. Verify PocketBase is running and accessible
5. Test accessing https://pocketbase.haugaard.dev/_/
6. Guide me through initial admin account setup

Reference files:
- @docs/homelab-setup-v2.md (this deployment guide)
```

### Manual Deployment Steps

```bash
# Navigate to PocketBase directory
cd /home/john/homelab/pocketbase

# Start PocketBase
docker compose up -d

# Verify container is running
docker ps | grep pocketbase

# Check logs
docker logs pocketbase -f
```

### Testing & Verification

```bash
# Test PocketBase API (from VPS8)
curl http://localhost:8090/api/health

# Test via Caddy (from local machine)
curl https://pocketbase.haugaard.dev/api/health

# Access PocketBase Admin UI
# Open browser: https://pocketbase.haugaard.dev/_/
# First visit: Create admin account
#   - Email
#   - Password

# Test from local app
curl https://pocketbase.haugaard.dev/api/collections
```

### Initial PocketBase Setup

**After accessing `https://pocketbase.haugaard.dev/_/`:**

1. **Create Admin Account**
   - Email: your-email@example.com
   - Password: Strong password

2. **Create Test Collection**
   - Name: `posts`
   - Type: Base
   - Fields: `title` (text), `content` (text)

3. **Test API**
```bash
# Create a record
curl -X POST https://pocketbase.haugaard.dev/api/collections/posts/records \
  -H "Content-Type: application/json" \
  -d '{"title":"Test Post","content":"Hello from PocketBase"}'

# List records
curl https://pocketbase.haugaard.dev/api/collections/posts/records
```

---

## Monitoring & Health Checks

### Health Check Script

Create a simple script to check all services:

```bash
nano /home/john/homelab/scripts/health-check.sh
```

**Add the following:**
```bash
#!/bin/bash
set -euo pipefail

echo "========================================="
echo "Haugaard Homelab Health Check"
echo "========================================="
echo ""

# Check if services are running
services=("caddy" "supabase-db" "supabase-kong" "supabase-auth" "supabase-rest" "n8n" "wikijs" "wikijs-db" "ollama" "pocketbase")

echo "Container Status:"
for service in "${services[@]}"; do
  if docker ps --format '{{.Names}}' | grep -q "^${service}$"; then
    echo "  ✓ $service: Running"
  else
    echo "  ✗ $service: NOT RUNNING"
  fi
done

echo ""
echo "Service Endpoints:"

# Test Caddy
if curl -s -o /dev/null -w "%{http_code}" https://haugaard.dev 2>/dev/null | grep -q "200\|301\|302\|404"; then
  echo "  ✓ Caddy (https): OK"
else
  echo "  ✗ Caddy: FAILED"
fi

# Test Supabase
if curl -s -o /dev/null -w "%{http_code}" https://supabase.haugaard.dev 2>/dev/null | grep -q "200\|404"; then
  echo "  ✓ Supabase: OK"
else
  echo "  ✗ Supabase: FAILED"
fi

# Test Supabase REST API
if curl -s -o /dev/null -w "%{http_code}" https://supabase.haugaard.dev/rest/v1/ 2>/dev/null | grep -q "200\|401"; then
  echo "  ✓ Supabase REST API: OK"
else
  echo "  ✗ Supabase REST API: FAILED"
fi

# Test n8n
if curl -s -o /dev/null -w "%{http_code}" https://n8n.haugaard.dev 2>/dev/null | grep -q "200"; then
  echo "  ✓ n8n: OK"
else
  echo "  ✗ n8n: FAILED"
fi

# Test Wiki.js
if curl -s -o /dev/null -w "%{http_code}" https://wikijs.haugaard.dev 2>/dev/null | grep -q "200"; then
  echo "  ✓ Wiki.js: OK"
else
  echo "  ✗ Wiki.js: FAILED"
fi

# Test Ollama
if curl -s -o /dev/null -w "%{http_code}" https://ollama.haugaard.dev/api/tags 2>/dev/null | grep -q "200"; then
  echo "  ✓ Ollama: OK"
else
  echo "  ✗ Ollama: FAILED"
fi

# Test PocketBase
if curl -s -o /dev/null -w "%{http_code}" https://pocketbase.haugaard.dev/api/health 2>/dev/null | grep -q "200"; then
  echo "  ✓ PocketBase: OK"
else
  echo "  ✗ PocketBase: FAILED"
fi

echo ""
echo "Database Connectivity:"

# Test Supabase PostgreSQL
if docker exec supabase-db pg_isready -U postgres >/dev/null 2>&1; then
  echo "  ✓ Supabase PostgreSQL: Ready"
else
  echo "  ✗ Supabase PostgreSQL: FAILED"
fi

# Test Wiki.js PostgreSQL
if docker exec wikijs-db pg_isready -U wikijs >/dev/null 2>&1; then
  echo "  ✓ Wiki.js PostgreSQL: Ready"
else
  echo "  ✗ Wiki.js PostgreSQL: FAILED"
fi

echo ""
echo "Disk Usage:"
DISK_USAGE=$(df -h / | awk 'NR==2 {print $5}' | sed 's/%//')
if [ "$DISK_USAGE" -gt 90 ]; then
  echo "  ✗ Disk Usage: ${DISK_USAGE}% (CRITICAL)"
elif [ "$DISK_USAGE" -gt 80 ]; then
  echo "  ⚠ Disk Usage: ${DISK_USAGE}% (WARNING: High)"
else
  echo "  ✓ Disk Usage: ${DISK_USAGE}%"
fi

echo ""
echo "Resource Usage:"
docker stats --no-stream --format "  {{.Name}}: CPU={{.CPUPerc}} | Memory={{.MemUsage}}"

echo ""
echo "========================================="
```

**Make executable:**
```bash
chmod +x /home/john/homelab/scripts/health-check.sh
```

**Run health check:**
```bash
/home/john/homelab/scripts/health-check.sh
```

### Viewing Logs

**View logs for specific service:**
```bash
# Real-time logs
docker logs -f caddy
docker logs -f supabase-db
docker logs -f n8n

# Last 100 lines
docker logs --tail 100 wikijs

# Logs since specific time
docker logs --since 1h ollama
```

**View all container logs:**
```bash
cd /home/john/homelab/[service]
docker compose logs -f
```

### Resource Monitoring

**Check resource usage:**
```bash
# Real-time monitoring
docker stats

# One-time snapshot
docker stats --no-stream

# Specific containers
docker stats caddy supabase-db n8n
```

**Check disk usage:**
```bash
# Docker disk usage
docker system df

# Volume sizes
docker volume ls -q | xargs docker volume inspect --format '{{ .Name }}: {{ .Mountpoint }}' | while read line; do
  name=$(echo $line | cut -d: -f1)
  path=$(echo $line | cut -d: -f2-)
  size=$(sudo du -sh $path 2>/dev/null | cut -f1)
  echo "$name: $size"
done
```

---

## Backup Strategy

### Automated Backups to Backblaze B2

All persistent data is backed up daily to B2.

### Master Backup Script

Create a comprehensive backup script that backs up all services:

```bash
nano /home/john/homelab/scripts/backup-all-to-b2.sh
```

**Add the following:**
```bash
#!/bin/bash
set -euo pipefail

# Load environment variables
set -a
source /home/john/homelab/.env.master
set +a

# Backup directory
BACKUP_DIR="/tmp/homelab_backups"
mkdir -p "$BACKUP_DIR"

TIMESTAMP=$(date +%Y%m%d_%H%M%S)

echo "========================================="
echo "Haugaard Homelab Backup to B2"
echo "Started: $(date)"
echo "========================================="

# Verify required variables are set
if [ -z "${B2_BACKUP_BUCKET:-}" ]; then
    echo "ERROR: B2_BACKUP_BUCKET not set" >&2
    exit 1
fi

# Backup all Supabase PostgreSQL databases (includes postgres + n8n)
echo "Backing up all Supabase databases..."
SUPABASE_PASSWORD=$(grep POSTGRES_PASSWORD /home/john/homelab/supabase/.env | cut -d'=' -f2)
PGPASSWORD="$SUPABASE_PASSWORD" docker exec supabase-db \
  pg_dumpall -U postgres | gzip > "$BACKUP_DIR/supabase_all_${TIMESTAMP}.sql.gz"

# Verify backup file size
SUPABASE_SIZE=$(stat -c%s "$BACKUP_DIR/supabase_all_${TIMESTAMP}.sql.gz" 2>/dev/null || stat -f%z "$BACKUP_DIR/supabase_all_${TIMESTAMP}.sql.gz")
if [ "$SUPABASE_SIZE" -lt 1000 ]; then
    echo "ERROR: Supabase backup file suspiciously small!" >&2
    exit 1
fi
echo "  ✓ Supabase backup: $(numfmt --to=iec-i --suffix=B $SUPABASE_SIZE)"

# Backup Wiki.js PostgreSQL
echo "Backing up Wiki.js database..."
WIKIJS_PASSWORD=$(grep POSTGRES_PASSWORD /home/john/homelab/wikijs/.env | cut -d'=' -f2)
PGPASSWORD="$WIKIJS_PASSWORD" docker exec wikijs-db \
  pg_dump -U wikijs -d wikijs | gzip > "$BACKUP_DIR/wikijs_${TIMESTAMP}.sql.gz"

# Backup n8n data volume
echo "Backing up n8n data..."
docker run --rm \
  -v n8n_data:/source:ro \
  -v "$BACKUP_DIR":/dest \
  alpine tar czf /dest/n8n_data_${TIMESTAMP}.tar.gz -C /source .

# Backup PocketBase data volume
echo "Backing up PocketBase data..."
docker run --rm \
  -v pocketbase_data:/source:ro \
  -v "$BACKUP_DIR":/dest \
  alpine tar czf /dest/pocketbase_data_${TIMESTAMP}.tar.gz -C /source .

# Backup Ollama models manifest (not full models - they're re-pullable)
echo "Backing up Ollama models manifest..."
docker exec ollama ollama list > /home/john/homelab/ollama/models-manifest.txt
cp /home/john/homelab/ollama/models-manifest.txt "$BACKUP_DIR/ollama_manifest_${TIMESTAMP}.txt"

# Upload all backups to B2
echo "Uploading backups to B2..."
s3cmd put "$BACKUP_DIR"/*.gz "$BACKUP_DIR"/*.txt "s3://${B2_BACKUP_BUCKET}/homelab/" 2>&1

# Cleanup local backups older than 3 days
echo "Cleaning up old local backups..."
find "$BACKUP_DIR" -name "*.gz" -mtime +3 -delete
find "$BACKUP_DIR" -name "*.txt" -mtime +3 -delete

echo ""
echo "========================================="
echo "Backup completed: $(date)"
echo "Backups uploaded to: s3://${B2_BACKUP_BUCKET}/homelab/"
echo "========================================="
```

**Make executable and schedule:**
```bash
chmod +x /home/john/homelab/scripts/backup-all-to-b2.sh

# Test backup manually
/home/john/homelab/scripts/backup-all-to-b2.sh

# Add to crontab (daily at 3 AM)
crontab -e

# Add line:
0 3 * * * /home/john/homelab/scripts/backup-all-to-b2.sh >> /home/john/logs/homelab_backup.log 2>&1
```

### Restore Procedures

**Restore all Supabase databases (includes postgres + n8n):**
```bash
# Download backup from B2
s3cmd get s3://haugaard-homelab-backups/homelab/supabase_all_20251116_030000.sql.gz /tmp/

# Restore all databases
gunzip < /tmp/supabase_all_20251116_030000.sql.gz | \
  docker exec -i supabase-db psql -U postgres
```

**Restore Wiki.js database:**
```bash
# Download backup
s3cmd get s3://haugaard-homelab-backups/homelab/wikijs_20251116_030000.sql.gz /tmp/

# Restore
gunzip < /tmp/wikijs_20251116_030000.sql.gz | \
  docker exec -i wikijs-db psql -U wikijs -d wikijs
```

**Restore Docker volume (e.g., n8n data):**
```bash
# Download backup
s3cmd get s3://haugaard-homelab-backups/homelab/n8n_data_20251116_030000.tar.gz /tmp/

# Stop service
cd /home/john/homelab/n8n
docker compose down

# Remove old volume
docker volume rm n8n_data

# Create new volume
docker volume create n8n_data

# Restore data
docker run --rm \
  -v n8n_data:/dest \
  -v /tmp:/source \
  alpine sh -c "cd /dest && tar xzf /source/n8n_data_20251116_030000.tar.gz"

# Restart service
docker compose up -d
```

**Restore Ollama models from manifest:**
```bash
# Download manifest
s3cmd get s3://haugaard-homelab-backups/homelab/ollama_manifest_latest.txt /tmp/

# Re-pull each model
while read line; do
    model=$(echo $line | awk '{print $1}')
    docker exec ollama ollama pull "$model"
done < /tmp/ollama_manifest_latest.txt
```

### Backup Verification

List all backups in B2:
```bash
s3cmd ls s3://haugaard-homelab-backups/homelab/
```

Check backup sizes:
```bash
s3cmd du s3://haugaard-homelab-backups/homelab/
```

---

## Quick Reference

### Connection Strings

**Supabase (from apps):**
```javascript
const supabase = createClient(
  'https://supabase.haugaard.dev',
  'your-anon-key'
)
```

**Supabase PostgreSQL Connection:**

**FROM OTHER DOCKER CONTAINERS (on homelab-net):**
```
Host: supabase-db
Port: 5432
Database: postgres (or n8n for n8n workflows)
User: postgres
Password: [from .env]
```

**FROM EXTERNAL CLIENTS (laptop, IDE, database tools):**

PostgreSQL cannot be accessed via Caddy (wrong protocol). Use SSH tunnel:

```bash
# Create SSH tunnel:
ssh -L 5432:localhost:5432 vps8

# Keep terminal open, then connect in another terminal/app:
Host: localhost
Port: 5432
Database: postgres
User: postgres
Password: [from .env]
```

**Examples:**
```bash
# psql from laptop:
psql -h localhost -p 5432 -U postgres -d postgres

# DataGrip / TablePlus:
Host: localhost
Port: 5432
Database: postgres
User: postgres
Password: [your password]
```

**SECURITY NOTE:** Never expose PostgreSQL directly to the internet. Always use SSH tunnels for external access.

**Wiki.js PostgreSQL:**
```
Host: wikijs-db
Port: 5432
Database: wikijs
User: wikijs
Password: [from .env]
```

**n8n:**
```
URL: https://n8n.haugaard.dev
```

**Ollama API:**
```bash
curl https://ollama.haugaard.dev/api/generate -d '{
  "model": "llama3.2:3b",
  "prompt": "Your prompt here"
}'
```

**PocketBase:**
```javascript
import PocketBase from 'pocketbase'
const pb = new PocketBase('https://pocketbase.haugaard.dev')
```

### Common Commands

**Start all services:**
```bash
cd /home/john/homelab
for dir in caddy supabase n8n wikijs ollama pocketbase; do
  cd $dir && docker compose up -d && cd ..
done
```

**Stop all services:**
```bash
cd /home/john/homelab
for dir in caddy supabase n8n wikijs ollama pocketbase; do
  cd $dir && docker compose down && cd ..
done
```

**Restart specific service:**
```bash
cd /home/john/homelab/[service]
docker compose restart
```

**View logs:**
```bash
docker logs -f [container-name]
```

**Check resource usage:**
```bash
docker stats
```

**Run health check:**
```bash
/home/john/homelab/scripts/health-check.sh
```

**Run backup:**
```bash
/home/john/homelab/scripts/backup-all-to-b2.sh
```

### Service URLs

- **Supabase**: https://supabase.haugaard.dev
- **Supabase REST API**: https://supabase.haugaard.dev/rest/v1/
- **n8n**: https://n8n.haugaard.dev
- **Wiki.js**: https://wikijs.haugaard.dev
- **Ollama**: https://ollama.haugaard.dev
- **PocketBase**: https://pocketbase.haugaard.dev
- **PocketBase Admin**: https://pocketbase.haugaard.dev/_/

### SSH Access

```bash
# Connect to VPS8
ssh vps8

# SSH with command
ssh vps8 'docker ps'
```

### Docker Volume Management

**List volumes:**
```bash
docker volume ls
```

**Inspect volume:**
```bash
docker volume inspect [volume-name]
```

**Remove unused volumes:**
```bash
docker volume prune
```

---

## Troubleshooting

### Container Won't Start

```bash
# Check logs
docker logs [container-name]

# Check docker-compose configuration
cd /home/john/homelab/[service]
docker compose config

# Rebuild container
docker compose down
docker compose up -d --force-recreate
```

### Service Returns 502 Bad Gateway

```bash
# Check if backend container is running
docker ps | grep [service-name]

# Check backend container logs
docker logs [container-name]

# Check Caddy logs
docker logs caddy

# Verify service is listening on correct port
docker exec [container-name] netstat -tlnp
```

### SSL Certificate Issues

```bash
# Check Caddy logs for certificate errors
docker logs caddy | grep -i cert

# Verify DNS is correct
nslookup [subdomain].haugaard.dev

# Check if ports 80 and 443 are accessible
curl -I http://[subdomain].haugaard.dev

# Verify UFW allows ports 80 and 443
sudo ufw status | grep -E "80|443"
```

### Database Connection Errors

```bash
# Test database connectivity
docker exec [db-container] psql -U [user] -c "SELECT 1"

# Check database logs
docker logs [db-container]

# Verify database is healthy
docker ps --filter "name=[db-container]" --format "{{.Status}}"
```

### Out of Disk Space

```bash
# Check disk usage
df -h

# Check Docker disk usage
docker system df

# Remove unused images and containers
docker system prune -a

# Remove old backups
find /tmp/homelab_backups -mtime +7 -delete
```

---

## Next Steps

After completing this deployment:

1. **Document Everything in Wiki.js**
   - Record any issues you encountered
   - Document custom configurations
   - Create runbooks for common operations

2. **Set Up Monitoring Alerts**
   - Consider deploying Uptime Kuma for visual monitoring
   - Use n8n to create workflows that monitor service health
   - Set up Discord/Slack notifications for failures

3. **Test Backup Restoration**
   - Practice restoring from B2 backups
   - Verify backup integrity
   - Document restore procedures in Wiki.js

4. **Deploy Your First App**
   - Use Supabase for backend
   - Deploy frontend to Cloudflare Pages
   - Test the full stack

5. **Optimize Resource Usage**
   - Monitor VPS8 performance
   - Identify bottlenecks
   - Consider splitting services if needed

---

**Congratulations on deploying the Haugaard Homelab infrastructure! 🚀**

**Guide Version:** 2.0
**Last Updated:** November 16, 2025
**Maintained By:** John Haugaard

**Production-Ready:** ✅ All critical issues resolved
**Security Hardened:** ✅ UFW firewall, localhost binding, secure backup scripts
**Backup Strategy:** ✅ Daily automated backups to B2 with restore procedures
**Monitoring:** ✅ Health check script and resource monitoring
**Learning Value:** ✅ Progressive complexity with hands-on experience at every layer
