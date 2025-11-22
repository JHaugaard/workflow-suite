# vps-ready Skill

## Overview

The vps-ready skill provides a comprehensive guided workflow for preparing VPS servers for production-ready containerized application deployments. It configures complete security hardening (UFW firewall, Docker security), automatic updates, resource management, monitoring, and establishes a robust infrastructure foundation following industry best practices.

**Use when:** Preparing a VPS (with SSH already configured) for deploying Docker-based applications and sites with production-grade security and operational readiness.

**Output:**
- System fully updated with automatic security updates enabled
- UFW firewall configured (SSH rate limiting, HTTP/HTTPS access)
- IPv6 disabled for simplified networking
- Docker installed with comprehensive security hardening
- Swap file configured (2GB, low swappiness)
- Docker network (jhh-net) with custom subnet for isolation
- Master environment file with resource limit defaults
- Caddy reverse proxy with automatic HTTPS, rate limiting, and resource limits
- Basic monitoring (disk space, container health)
- Production-ready VPS with security and operational best practices

---

## How It Works

When invoked, this skill will guide you through 11 comprehensive phases:

### Phase 0: Gather Information
Collect VPS alias, IP address, domain (optional)

### Phase 0.5: System Updates & Base Configuration (NEW)
- Update all system packages
- Configure system timezone (America/New_York)
- Handle kernel updates and reboots

### Phase 1: Configure UFW Firewall
- Verify SSH password authentication disabled
- Default deny incoming, SSH rate limiting
- Allow HTTP/HTTPS

### Phase 1.5: Disable IPv6 (NEW)
- Disable IPv6 system-wide
- Simplify networking configuration

### Phase 2: Install & Configure Docker
- Install Docker if needed
- **Comprehensive security hardening:**
  - Log rotation (30MB per container)
  - Live restore enabled
  - No inter-container communication by default
  - No privilege escalation
  - Unix socket only (NOT TCP)
  - Default ulimits configured

### Phase 2.5: Configure Swap (NEW)
- Create 2GB swap file
- Set swappiness to 10 (prefer RAM)
- Prevent OOM crashes

### Phase 3: Create Directory Structure
- Organized foundation at `/home/john/vps-setup/`
- Includes directories for apps, scripts, logs, monitoring

### Phase 4: Create Docker Network
- Network: `jhh-net` with custom subnet (172.18.0.0/16)
- Container-to-container communication using service names

### Phase 5: Create Master Environment File
- Shared configuration variables
- **Resource limit defaults** for consistent deployments
- Secured with chmod 600

### Phase 6: Deploy Caddy
- **Enhanced configuration:**
  - Rate limiting (100 requests/minute per IP)
  - Comprehensive security headers (HSTS, X-Frame-Options, etc.)
  - Admin API disabled for security
  - Resource limits (512MB RAM, 0.5 CPU)
  - Health checks enabled
  - Minimal logging

### Phase 7: Verify Caddy & HTTPS
- Test local access
- Verify health checks
- Verify resource limits applied
- Test HTTPS certificate acquisition (if domain provided)

### Phase 8: Setup Monitoring (NEW)
- Disk space monitoring (80% threshold, daily check)
- Container health monitoring (every 6 hours)
- Cron jobs configured
- Logs written to files for review

### Phase 9: Configure Automatic Updates (NEW)
- Install unattended-upgrades
- Automatic security updates enabled
- Optional email notifications

### Phase 10: Final Summary
- Complete security verification checklist
- Operational readiness checklist
- Infrastructure summary
- Next steps for application deployment

---

## Prerequisites

### Required
- **SSH Access:** Passwordless SSH already configured (e.g., via ssh-setup-agent skill)
- **VPS User:** Non-root user `john` with sudo privileges
- **Operating System:** Ubuntu or Debian-based Linux
- **Root Access:** Sudo privileges for firewall and Docker installation

### Optional
- **Domain Name:** For automatic HTTPS certificate acquisition via Caddy
- **DNS Configuration:** A records pointing to VPS IP if using domain (Cloudflare recommended)

---

## Security Features

### Firewall & Network
✓ **UFW Firewall** - Default deny incoming, SSH rate limiting (anti brute-force)
✓ **IPv6 Disabled** - Simplified networking, reduced attack surface
✓ **Localhost Binding** - Application containers bind to 127.0.0.1 (not exposed)
✓ **Caddy Gateway** - Only Caddy faces internet (ports 80, 443)

### Docker Security
✓ **Unix Socket Only** - Docker daemon NOT exposed on TCP
✓ **No New Privileges** - Containers cannot escalate privileges
✓ **Inter-Container Isolation** - No communication by default (must use networks)
✓ **Live Restore** - Containers survive daemon restarts
✓ **Default ulimits** - Prevents resource exhaustion

### Application Security
✓ **Security Headers** - HSTS, X-Frame-Options, X-Content-Type-Options, Referrer-Policy, Permissions-Policy
✓ **Rate Limiting** - 100 requests/minute per IP (configurable)
✓ **Admin API Disabled** - Caddy admin interface not exposed
✓ **Resource Limits** - Prevent runaway container resource usage
✓ **Docker Log Rotation** - Automatic cleanup prevents disk exhaustion
✓ **Secured Environment Files** - chmod 600 on sensitive configuration

### System Security
✓ **Automatic Security Updates** - Unattended-upgrades configured
✓ **SSH Password Auth Disabled** - Key-only authentication verified
✓ **System Timezone Configured** - Accurate log timestamps

---

## Infrastructure Created

### Directory Structure
```
/home/john/vps-setup/
├── .env.master              # Shared environment variables (chmod 600)
├── caddy/
│   ├── Caddyfile           # Reverse proxy with rate limiting
│   └── docker-compose.yml  # Caddy with resource limits & health checks
├── apps/                    # Future application deployments
├── scripts/                 # Monitoring scripts
│   ├── disk-monitor.sh     # Disk space monitoring (80% threshold)
│   └── container-health.sh # Container health monitoring
└── logs/
    └── monitoring/         # Monitoring logs
        ├── disk-alerts.log
        └── container-health.log
```

### Docker Network
- **Name:** `jhh-net`
- **Type:** Bridge network
- **Subnet:** 172.18.0.0/16 (custom)
- **Purpose:** Container-to-container communication using service names

### Caddy Configuration
- **Image:** `caddy:2.7-alpine`
- **Ports:** 80 (HTTP), 443 (HTTPS)
- **Email:** jhaugaard@mac.com (for Let's Encrypt)
- **Features:**
  - Automatic HTTPS
  - Rate limiting (100 req/min per IP)
  - Enhanced security headers
  - Resource limits (512MB RAM, 0.5 CPU)
  - Health checks
  - Admin API disabled

### System Configuration
- **Swap:** 2GB with swappiness=10
- **Timezone:** America/New_York
- **IPv6:** Disabled
- **Automatic Updates:** Security updates enabled

---

## Usage

### Starting VPS Readiness

In Claude Desktop/Code, start with:

```
Make my VPS production-ready at <ip_address>
```

Or:

```
I need to prepare my VPS for Docker deployments
```

The skill will ask for:
- VPS Alias (e.g., vps_app1)
- VPS IP Address
- Primary Domain (optional)
- First Subdomain (optional - for Caddyfile example)

---

## After VPS Ready: Deploying Your First App

### 1. Create Application Directory
```bash
ssh <vps_alias>
mkdir -p /home/john/vps-setup/apps/my-app
cd /home/john/vps-setup/apps/my-app
```

### 2. Create docker-compose.yml
```yaml
version: '3.8'

services:
  my-app:
    image: my-app:latest
    container_name: my-app
    restart: unless-stopped
    ports:
      - "127.0.0.1:8080:8080"  # Bind to localhost only
    env_file:
      - ../../.env.master  # Source shared config
      - .env               # App-specific config
    networks:
      - jhh-net
    # Use resource limits from .env.master
    deploy:
      resources:
        limits:
          memory: ${DEFAULT_MEM_LIMIT}
          cpus: ${DEFAULT_CPU_LIMIT}
        reservations:
          memory: ${DEFAULT_MEM_RESERVATION}
    # Health check (adjust endpoint as needed)
    healthcheck:
      test: ["CMD", "wget", "--no-verbose", "--tries=1", "--spider", "http://localhost:8080/health"]
      interval: 30s
      timeout: 10s
      retries: 3

networks:
  jhh-net:
    external: true
```

### 3. Update Caddyfile
```bash
nano /home/john/vps-setup/caddy/Caddyfile
```

Add your subdomain:
```caddyfile
app.example.com {
    import security
    import ratelimit
    reverse_proxy my-app:8080
}
```

Reload Caddy:
```bash
docker exec caddy caddy reload --config /etc/caddy/Caddyfile
```

### 4. Deploy Application
```bash
cd /home/john/vps-setup/apps/my-app
docker compose up -d
```

### 5. Verify
```bash
# Check container
docker ps | grep my-app

# Check health
docker inspect --format='{{.State.Health.Status}}' my-app

# Check logs
docker logs my-app

# Test endpoint
curl https://app.example.com
```

---

## Connecting to External Services

### Homelab Services (if applicable)
Your VPS can connect to external services like:
- **Supabase:** `https://supabase.haugaard.dev`
- **n8n:** `https://n8n.haugaard.dev`
- **Ollama:** `https://ollama.haugaard.dev`

Store credentials in application `.env` files:
```env
# App-specific .env
SUPABASE_URL=https://supabase.haugaard.dev
SUPABASE_ANON_KEY=your-key-here
```

---

## Common Operations

### View Firewall Status
```bash
sudo ufw status verbose
```

### Check All Running Containers
```bash
docker ps
```

### View Caddy Logs
```bash
docker logs -f caddy
```

### Restart Caddy
```bash
cd /home/john/vps-setup/caddy
docker compose restart
```

### Check Disk Usage
```bash
df -h
docker system df
```

### Inspect Docker Network
```bash
docker network inspect jhh-net
```

### View Monitoring Logs
```bash
tail -f /home/john/vps-setup/logs/monitoring/disk-alerts.log
tail -f /home/john/vps-setup/logs/monitoring/container-health.log
```

### Check Swap Usage
```bash
free -h
sudo swapon --show
```

### View Automatic Update Logs
```bash
cat /var/log/unattended-upgrades/unattended-upgrades.log
```

### Manual Security Update Check
```bash
sudo unattended-upgrades --dry-run
```

---

## Security Verification

Run these commands to verify your VPS security posture:

```bash
# 1. SSH password authentication disabled
sudo grep "^PasswordAuthentication" /etc/ssh/sshd_config
# Expected: PasswordAuthentication no

# 2. UFW firewall active
sudo ufw status
# Expected: Status: active

# 3. IPv6 disabled
cat /proc/sys/net/ipv6/conf/all/disable_ipv6
# Expected: 1

# 4. Docker only on Unix socket (not TCP)
sudo ss -tlnp | grep dockerd
# Expected: No output (only Unix socket)

# 5. Docker security settings
docker info | grep -E "live-restore|icc"
# Expected: Live Restore Enabled: true

# 6. Swap active with low swappiness
sudo swapon --show && cat /proc/sys/vm/swappiness
# Expected: Swap listed, swappiness = 10

# 7. Environment file secured
ls -la /home/john/vps-setup/.env.master
# Expected: -rw------- 1 john john

# 8. Caddy health check
docker inspect --format='{{.State.Health.Status}}' caddy
# Expected: healthy

# 9. Monitoring scripts executable
ls -l /home/john/vps-setup/scripts/*.sh
# Expected: -rwxr-xr-x (executable)

# 10. Cron jobs configured
crontab -l | grep -E "disk-monitor|container-health"
# Expected: Two monitoring cron jobs listed
```

---

## Troubleshooting

### UFW Enabled but SSH Disconnected
**Prevention:** The skill configures SSH (port 22) BEFORE enabling UFW.

**Recovery:** Access VPS via hosting provider's web console, run:
```bash
sudo ufw allow 22/tcp
```

### Docker Requires Sudo
**Cause:** User not in docker group or need to re-login.

**Fix:**
```bash
sudo usermod -aG docker john
exit
ssh <vps_alias>
docker ps  # Should work without sudo
```

### Caddy Certificate Errors
**Cause:** DNS not configured or not propagated.

**Fix:**
1. Verify DNS A record: `nslookup app.example.com`
2. Ensure record points to VPS IP
3. Ensure Cloudflare is in "DNS only" mode (gray cloud)
4. Wait for DNS propagation (usually minutes, up to 48 hours)
5. Check Caddy logs: `docker logs caddy | grep -i cert`

### Container Can't Communicate
**Cause:** Container not on jhh-net network.

**Fix:** Add to docker-compose.yml:
```yaml
networks:
  - jhh-net

networks:
  jhh-net:
    external: true
```

### Caddy Unhealthy
**Cause:** Health check failing.

**Fix:**
```bash
docker logs caddy
docker inspect --format='{{.State.Health}}' caddy
```

### Monitoring Not Logging
**Cause:** Scripts not executable or cron jobs not configured.

**Fix:**
```bash
chmod +x /home/john/vps-setup/scripts/*.sh
crontab -l  # Verify cron jobs exist
```

---

## Version History

### v2.0 (November 21, 2024)
**Major Update - Production Hardening**

**New Phases:**
- Phase 0.5: System updates & timezone configuration
- Phase 1.5: IPv6 disable
- Phase 2.5: Swap configuration (2GB, swappiness=10)
- Phase 8: Monitoring setup (disk space, container health)
- Phase 9: Automatic security updates (unattended-upgrades)

**Enhanced Security:**
- Docker daemon comprehensive security hardening
  - Unix socket only (NOT TCP)
  - Inter-container communication disabled by default
  - No privilege escalation
  - Live restore enabled
  - Default ulimits configured
- SSH password authentication verification
- Custom Docker network subnet (172.18.0.0/16)

**Enhanced Caddy:**
- Rate limiting (100 requests/minute per IP)
- Enhanced security headers (Permissions-Policy, etc.)
- Resource limits (512MB RAM, 0.5 CPU)
- Health checks enabled
- Admin API disabled
- Minimal logging configuration

**Operational Improvements:**
- Basic monitoring with cron jobs
- Disk space alerts (80% threshold)
- Container health monitoring
- Expanded .env.master with resource limit defaults
- Comprehensive verification checklists
- Useful commands reference

**Documentation:**
- Security verification checklist
- Operational readiness checklist
- Complete infrastructure summary
- Enhanced troubleshooting guide

### v1.0 (November 20, 2024)
**Initial Release**

- UFW firewall configuration with SSH rate limiting
- Docker installation and log rotation setup
- Docker network creation (jhh-net)
- Caddy reverse proxy deployment with automatic HTTPS
- Security headers configuration (HSTS, X-Frame-Options, etc.)
- Master environment file with proper permissions
- Directory structure for organized deployments
- Comprehensive next steps for application deployment

---

## What's New in v2.0

### Production-Grade Security
- **Unix Socket Only:** Docker daemon secured against network attacks
- **Container Isolation:** Inter-container communication disabled by default
- **No Privilege Escalation:** Containers cannot gain additional privileges
- **IPv6 Disabled:** Simplified networking, reduced attack surface
- **Automatic Updates:** Security patches installed automatically

### Resource Management
- **Swap File:** 2GB swap prevents OOM crashes on low-memory VPS
- **Resource Limits:** Default memory and CPU limits for all containers
- **Caddy Limits:** Prevents reverse proxy from consuming excessive resources

### Operational Readiness
- **Disk Monitoring:** Daily checks alert at 80% usage
- **Health Monitoring:** Container health checked every 6 hours
- **Cron Automation:** Monitoring runs automatically
- **Verification Checklists:** Comprehensive security and operational verification

### Enhanced Caddy
- **Rate Limiting:** Protects against abuse (100 req/min per IP)
- **Enhanced Headers:** Additional security headers (Permissions-Policy)
- **Health Checks:** Docker monitors Caddy health and restarts if needed
- **Admin API Disabled:** Removes unnecessary attack surface

---

**Version:** 2.0
**Last Updated:** November 21, 2024
**License:** Personal use
**Author:** Built for learning and production-ready VPS management
**Focus:** Security hardening, Docker best practices, resource management, operational monitoring, smooth deployments
