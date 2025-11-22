---
name: vps-ready
description: "Guided workflow for outfitting VPS servers with Docker, Caddy, and security hardening for containerized app deployments. Use when preparing a VPS (with SSH already configured) for production-ready Docker-based applications with best practices for firewall, networking, and reverse proxy."
---

# vps-ready

<purpose>
Guide users through VPS infrastructure setup with UFW firewall, Docker configuration, Caddy reverse proxy, and security best practices. Prepares VPS for smooth containerized application deployments with comprehensive security hardening and operational monitoring.
</purpose>

<output>
- System fully updated with automatic security updates enabled
- UFW firewall configured with SSH rate limiting and HTTP/HTTPS access
- IPv6 disabled for simplified networking
- Docker installed with comprehensive security hardening
- Swap file configured (2GB, low swappiness)
- Directory structure for application deployments
- Docker network (jhh-net) with custom subnet for isolation
- Master environment file with resource limit defaults
- Caddy reverse proxy with automatic HTTPS, rate limiting, and resource limits
- Basic monitoring (disk space, container health)
- Production-ready VPS with security and operational best practices
</output>

<role>
Business-like, efficient technical guide. Direct and succinct communication. Use clear status indicators (✓ for completed steps). Provide explicit warnings before security-critical operations. Include location context markers ("On the VPS...", "On local machine..."). Warn about sudo/password prompts before commands that require them. No unnecessary affirmations or chitchat.
</role>

---

<workflow>

<phase id="0" name="gather-information">
<action>Collect VPS details and determine infrastructure requirements.</action>

<required-information>
Ask the user for:
1. **VPS Alias** (e.g., vps_app1, vps_prod) - used for reference
2. **VPS IP Address**
3. **Primary Domain** (optional - e.g., example.com) - for Caddy configuration
4. **First Subdomain** (optional - e.g., app.example.com) - example for Caddyfile
</required-information>

<assumptions>
- SSH access already configured (passwordless key-based authentication)
- Non-root user with sudo privileges exists
- User is: john
- Operating System: Ubuntu or Debian-based
</assumptions>

<initialization>
Record VPS details for use throughout workflow. If domain provided, prepare for Caddy HTTPS setup. If no domain, Caddy will be configured for local testing only.
</initialization>
</phase>

<phase id="0.5" name="system-updates-and-base-configuration">
<action>Update system packages, configure timezone, and apply base hardening.</action>

<location-marker>On the VPS (logged in as john)</location-marker>

<rationale>
Fresh VPS instances need security patches before exposing services. System timezone must be configured for accurate log timestamps and cron job execution.
</rationale>

<commands>
Connect to VPS:
```bash
ssh <vps_alias>
```

Update system packages:
```bash
sudo apt update
sudo apt upgrade -y
```

Configure system timezone:
```bash
sudo timedatectl set-timezone America/New_York
```

Verify timezone:
```bash
timedatectl
```

Expected output shows: `Time zone: America/New_York`

Check if reboot required (kernel updates):
```bash
[ -f /var/run/reboot-required ] && echo "Reboot required" || echo "No reboot needed"
```
</commands>

<if-reboot-required>
If reboot required:
```bash
sudo reboot
```

Wait 30 seconds, then reconnect:
```bash
ssh <vps_alias>
```
</if-reboot-required>

<confirmation>
Ask user to confirm: "System updated and timezone configured"
Mark phase complete.
</confirmation>
</phase>

<phase id="1" name="configure-ufw-firewall">
<action>Configure UFW firewall with default deny, SSH rate limiting, and HTTP/HTTPS access.</action>

<location-marker>On the VPS (logged in as john)</location-marker>

<critical-warning>
CRITICAL: Configure SSH (port 22) BEFORE enabling UFW to prevent lockout.
</critical-warning>

<ssh-verification>
First, verify SSH is configured correctly (password authentication disabled):
```bash
sudo grep "^PasswordAuthentication" /etc/ssh/sshd_config
```

Expected: `PasswordAuthentication no`

If not set, key-based authentication may not be enforced. This should have been configured by ssh-setup-agent.
</ssh-verification>

<commands>
Install UFW if not present:
```bash
sudo apt update
sudo apt install -y ufw
```

Configure firewall rules:
```bash
# Default policies
sudo ufw default deny incoming
sudo ufw default allow outgoing

# Allow SSH with rate limiting (prevents brute force)
sudo ufw limit 22/tcp

# Allow HTTP and HTTPS (for Caddy)
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp

# Enable firewall
sudo ufw enable

# Verify status
sudo ufw status verbose
```
</commands>

<expected-output>
```
Status: active
Logging: on (low)
Default: deny (incoming), allow (outgoing), disabled (routed)

To                         Action      From
--                         ------      ----
22/tcp                     LIMIT IN    Anywhere
80/tcp                     ALLOW IN    Anywhere
443/tcp                    ALLOW IN    Anywhere
```
</expected-output>

<security-notes>
- SSH rate limiting prevents brute force attacks (max 6 connections per 30 seconds)
- All services will bind to 127.0.0.1 (localhost) - only Caddy faces internet
- Docker bypasses UFW by default, but localhost binding provides protection
- UFW configuration persists across reboots automatically
</security-notes>

<confirmation>
Ask user to confirm: "UFW configured and enabled"
Mark phase complete.
</confirmation>
</phase>

<phase id="1.5" name="disable-ipv6">
<action>Disable IPv6 for simplified networking configuration.</action>

<location-marker>On the VPS</location-marker>

<rationale>
Disabling IPv6 simplifies network configuration and reduces attack surface for single-stack IPv4 deployments.
</rationale>

<commands>
Disable IPv6 system-wide:
```bash
sudo sysctl -w net.ipv6.conf.all.disable_ipv6=1
sudo sysctl -w net.ipv6.conf.default.disable_ipv6=1
```

Make persistent across reboots:
```bash
echo 'net.ipv6.conf.all.disable_ipv6=1' | sudo tee -a /etc/sysctl.conf
echo 'net.ipv6.conf.default.disable_ipv6=1' | sudo tee -a /etc/sysctl.conf
```

Verify IPv6 is disabled:
```bash
cat /proc/sys/net/ipv6/conf/all/disable_ipv6
```

Expected: `1` (disabled)
</commands>

<confirmation>
Ask user to confirm: "IPv6 disabled"
Mark phase complete.
</confirmation>
</phase>

<phase id="2" name="install-and-configure-docker">
<action>Install Docker with comprehensive security hardening and log rotation.</action>

<location-marker>On the VPS</location-marker>

<verification-command>
```bash
docker --version
```
</verification-command>

<if-docker-installed>
Verify version is displayed.
Proceed to docker configuration below.
</if-docker-installed>

<if-docker-not-installed>
Provide installation commands:

```bash
# Download Docker installation script
curl -fsSL https://get.docker.com -o get-docker.sh

# Run installation script
sudo sh get-docker.sh

# Add john to docker group (allows docker without sudo)
sudo usermod -aG docker john

# IMPORTANT: Log out and back in for group membership to take effect
exit
```

Instruct user to reconnect:
```bash
ssh <vps_alias>
```

Verify Docker works without sudo:
```bash
docker ps
```

Expected: Empty list or running containers (no permission errors)
</if-docker-not-installed>

<docker-security-configuration>
Create comprehensive Docker daemon configuration with security hardening and log rotation:

```bash
sudo nano /etc/docker/daemon.json
```

Add the following content:
```json
{
  "log-driver": "json-file",
  "log-opts": {
    "max-size": "10m",
    "max-file": "3"
  },
  "live-restore": true,
  "userland-proxy": false,
  "no-new-privileges": true,
  "icc": false,
  "default-ulimits": {
    "nofile": {
      "Name": "nofile",
      "Hard": 64000,
      "Soft": 64000
    }
  }
}
```

Save and exit (Ctrl+X, Y, Enter).

Security settings explained:
- `log-driver/log-opts`: Automatic log rotation (30MB per container max)
- `live-restore`: Containers survive Docker daemon restarts
- `userland-proxy: false`: Better performance, uses iptables directly
- `no-new-privileges`: Containers cannot escalate privileges
- `icc: false`: No inter-container communication by default (must use networks)
- `default-ulimits`: Prevents file descriptor exhaustion

Restart Docker daemon:
```bash
sudo systemctl restart docker
```

Verify configuration:
```bash
docker info | grep -A 10 "Logging Driver"
```

Expected output includes:
```
Logging Driver: json-file
  max-file: 3
  max-size: 10m
```

Verify Docker only listening on Unix socket (NOT TCP):
```bash
sudo ss -tlnp | grep dockerd
```

Expected: Should show ONLY Unix socket, NOT 0.0.0.0:2375 or 2376
</docker-security-configuration>

<confirmation>
Ask user to confirm: "Docker installed and security configured"
Mark phase complete.
</confirmation>
</phase>

<phase id="2.5" name="configure-swap">
<action>Create and configure swap file for memory management.</action>

<location-marker>On the VPS</location-marker>

<rationale>
Swap prevents out-of-memory crashes on VPS instances with limited RAM. Low swappiness (10) prefers using RAM over swap for better performance.
</rationale>

<commands>
Create 2GB swap file:
```bash
sudo fallocate -l 2G /swapfile
```

Secure swap file permissions:
```bash
sudo chmod 600 /swapfile
```

Format as swap:
```bash
sudo mkswap /swapfile
```

Enable swap:
```bash
sudo swapon /swapfile
```

Make swap permanent across reboots:
```bash
echo '/swapfile none swap sw 0 0' | sudo tee -a /etc/fstab
```

Configure swappiness (lower = prefer RAM):
```bash
sudo sysctl vm.swappiness=10
echo 'vm.swappiness=10' | sudo tee -a /etc/sysctl.conf
```

Verify swap is active:
```bash
sudo swapon --show
```

Expected output:
```
NAME      TYPE SIZE USED PRIO
/swapfile file   2G   0B   -2
```

Check swappiness value:
```bash
cat /proc/sys/vm/swappiness
```

Expected: `10`
</commands>

<confirmation>
Ask user to confirm: "Swap configured (2GB, swappiness=10)"
Mark phase complete.
</confirmation>
</phase>

<phase id="3" name="create-directory-structure">
<action>Create base directory structure for application deployments.</action>

<location-marker>On the VPS</location-marker>

<directory-structure>
```
/home/john/vps-setup/
├── .env.master              # Shared environment variables
├── caddy/
│   ├── Caddyfile
│   └── docker-compose.yml
├── apps/                    # Placeholder for future applications
├── scripts/                 # Utility scripts (monitoring, health checks)
│   ├── disk-monitor.sh
│   └── container-health.sh
└── logs/                    # Application logs
    └── monitoring/          # Monitoring script logs
```
</directory-structure>

<commands>
Create directory structure:
```bash
mkdir -p /home/john/vps-setup/{caddy,apps,scripts,logs/monitoring}
```

Verify structure:
```bash
ls -la /home/john/vps-setup/
```
</commands>

<note>
This structure provides a clean foundation for deploying containerized applications. Each app can have its own subdirectory under `/home/john/vps-setup/apps/`.
</note>

<confirmation>
Ask user to confirm: "Directory structure created"
Mark phase complete.
</confirmation>
</phase>

<phase id="4" name="create-docker-network">
<action>Create dedicated Docker network with custom subnet for container communication.</action>

<location-marker>On the VPS</location-marker>

<network-purpose>
Docker networks enable containers to communicate using service names instead of IP addresses. The `jhh-net` network will be used by all deployed applications and Caddy. Custom subnet provides better isolation and predictable addressing.
</network-purpose>

<commands>
Create Docker network with custom subnet:
```bash
docker network create --subnet=172.18.0.0/16 jhh-net
```

Verify network exists:
```bash
docker network ls | grep jhh-net
```

Inspect network configuration:
```bash
docker network inspect jhh-net | grep -A 3 IPAM
```

Expected: Shows subnet 172.18.0.0/16
</commands>

<expected-output>
```
[network-id]   jhh-net   bridge   local
```
</expected-output>

<confirmation>
Ask user to confirm: "Docker network jhh-net created with custom subnet"
Mark phase complete.
</confirmation>
</phase>

<phase id="5" name="create-master-environment-file">
<action>Create master environment file with shared configuration and resource limit defaults.</action>

<location-marker>On the VPS</location-marker>

<commands>
Create master environment file:
```bash
nano /home/john/vps-setup/.env.master
```

Add the following content:
```env
# VPS Configuration
VPS_ALIAS=<vps_alias>
VPS_IP=<vps_ip>

# Domain Configuration (if applicable)
DOMAIN=<domain>

# Docker Network
DOCKER_NETWORK=jhh-net

# Timezone
TZ=America/New_York

# User
USER=john

# Resource Limits (defaults for new app deployments)
DEFAULT_MEM_LIMIT=512m
DEFAULT_MEM_RESERVATION=256m
DEFAULT_CPU_LIMIT=1.0

# Admin Email
ADMIN_EMAIL=jhaugaard@mac.com

# Monitoring
DISK_ALERT_THRESHOLD=80
```

Save and exit (Ctrl+X, Y, Enter).

Secure the file:
```bash
chmod 600 /home/john/vps-setup/.env.master
```

Verify permissions:
```bash
ls -la /home/john/vps-setup/.env.master
```

Expected: `-rw------- 1 john john` (only john can read/write)
</commands>

<note>
This file stores configuration values shared across multiple applications. Service-specific `.env` files can source this master file. Resource limit defaults help maintain consistent performance across deployments.
</note>

<confirmation>
Ask user to confirm: "Master environment file created and secured"
Mark phase complete.
</confirmation>
</phase>

<phase id="6" name="deploy-caddy">
<action>Deploy Caddy reverse proxy with rate limiting, security headers, and resource limits.</action>

<location-marker>On the VPS</location-marker>

<caddy-purpose>
Caddy provides:
- Automatic HTTPS certificate management (Let's Encrypt)
- Reverse proxy routing based on domain names
- Security isolation (only Caddy faces the internet)
- Rate limiting to prevent abuse
- Comprehensive security headers
</caddy-purpose>

<create-caddyfile>
Create Caddyfile:
```bash
nano /home/john/vps-setup/caddy/Caddyfile
```

Add comprehensive template with rate limiting and security headers:
```caddyfile
# Global options
{
    email jhaugaard@mac.com
    # Disable admin API for security
    admin off
}

# Rate limiting snippet (100 requests/minute per IP)
(ratelimit) {
    rate_limit {
        zone dynamic {
            key {remote_host}
            events 100
            window 1m
        }
    }
}

# Enhanced security headers snippet
(security) {
    header {
        Strict-Transport-Security "max-age=31536000; includeSubDomains; preload"
        X-Content-Type-Options "nosniff"
        X-Frame-Options "DENY"
        Referrer-Policy "strict-origin-when-cross-origin"
        X-XSS-Protection "1; mode=block"
        Permissions-Policy "geolocation=(), microphone=(), camera=()"
        -Server  # Remove server header for security
    }
}

# Example: First application subdomain (uncomment and modify when ready)
# app.example.com {
#     import security
#     import ratelimit
#     reverse_proxy app-container:8080
# }
```

Save and exit.
</create-caddyfile>

<create-docker-compose>
Create Caddy docker-compose.yml with resource limits and health checks:
```bash
nano /home/john/vps-setup/caddy/docker-compose.yml
```

Add the following:
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
      - jhh-net
    # Resource limits
    deploy:
      resources:
        limits:
          memory: 512m
          cpus: '0.5'
        reservations:
          memory: 256m
    # Health check
    healthcheck:
      test: ["CMD", "wget", "--no-verbose", "--tries=1", "--spider", "http://localhost:80"]
      interval: 30s
      timeout: 10s
      retries: 3
      start_period: 40s
    # Minimal logging
    logging:
      driver: "json-file"
      options:
        max-size: "10m"
        max-file: "3"

volumes:
  caddy_data:
  caddy_config:

networks:
  jhh-net:
    external: true
```

Save and exit.
</create-docker-compose>

<start-caddy>
Navigate to Caddy directory and start:
```bash
cd /home/john/vps-setup/caddy
docker compose up -d
```

Verify Caddy is running:
```bash
docker ps | grep caddy
```

Check logs:
```bash
docker logs caddy
```

Expected: No errors, Caddy starts successfully
</start-caddy>

<confirmation>
Ask user to confirm: "Caddy deployed with rate limiting and security headers"
Mark phase complete.
</confirmation>
</phase>

<phase id="7" name="verify-caddy-https">
<action>Verify Caddy is accessible, test HTTPS, and confirm resource limits.</action>

<location-marker>On the VPS</location-marker>

<test-local-access>
Test Caddy responds locally:
```bash
curl http://localhost
```

Expected: `404 page not found` (normal - no backend services configured yet)
</test-local-access>

<verify-health-and-resources>
Verify Caddy health check status:
```bash
docker inspect --format='{{.State.Health.Status}}' caddy
```

Expected: `healthy` (may take up to 40 seconds to become healthy)

Verify resource limits applied:
```bash
docker inspect caddy | grep -A 5 '"Memory"'
```

Expected: Shows memory limit of 536870912 bytes (512MB)

Verify port bindings:
```bash
sudo ss -tlnp | grep ':80\|:443'
```

Expected: Only Caddy (docker-proxy) listening on ports 80 and 443
</verify-health-and-resources>

<if-domain-provided>
Test HTTPS from local machine:

**IMPORTANT: DNS must be configured first.**

Ensure A record exists in Cloudflare:
```
<subdomain>.<domain>  →  A  →  <vps_ip>  (gray cloud - DNS only)
```

From local machine (not VPS):
```bash
curl -I https://<subdomain>.<domain>
```

Expected:
- First request: May return 502 (no backend yet), but HTTPS should work
- Certificate errors: DNS not configured or propagating

View certificate acquisition in Caddy logs:
```bash
docker logs caddy | grep -i cert
```
</if-domain-provided>

<if-no-domain>
Caddy is configured for local testing only. When ready to deploy applications with domains:
1. Configure DNS A records in Cloudflare (gray cloud)
2. Update Caddyfile with actual subdomains
3. Reload Caddy: `docker exec caddy caddy reload --config /etc/caddy/Caddyfile`
</if-no-domain>

<confirmation>
Ask user to confirm: "Caddy verified, healthy, and accessible"
Mark phase complete.
</confirmation>
</phase>

<phase id="8" name="setup-monitoring">
<action>Configure basic monitoring scripts for disk space and container health.</action>

<location-marker>On the VPS</location-marker>

<rationale>
Basic monitoring provides early warning of disk exhaustion and container failures. Logs are written to files for review and learning.
</rationale>

<create-disk-monitor>
Create disk space monitoring script:
```bash
nano /home/john/vps-setup/scripts/disk-monitor.sh
```

Add content:
```bash
#!/bin/bash
THRESHOLD=80
ALERT_LOG="/home/john/vps-setup/logs/monitoring/disk-alerts.log"

# Get disk usage percentage (root partition)
USAGE=$(df / | tail -1 | awk '{print $5}' | sed 's/%//')

if [ "$USAGE" -gt "$THRESHOLD" ]; then
    echo "$(date): ALERT - Disk usage at ${USAGE}%" >> "$ALERT_LOG"
fi
```

Save and make executable:
```bash
chmod +x /home/john/vps-setup/scripts/disk-monitor.sh
```
</create-disk-monitor>

<create-container-health>
Create container health check script:
```bash
nano /home/john/vps-setup/scripts/container-health.sh
```

Add content:
```bash
#!/bin/bash
LOG_FILE="/home/john/vps-setup/logs/monitoring/container-health.log"

# Log all container statuses
echo "$(date): Container Status Check" >> "$LOG_FILE"
docker ps -a --format "{{.Names}}: {{.Status}}" >> "$LOG_FILE"

# Check for unhealthy containers
UNHEALTHY=$(docker ps --filter health=unhealthy --format "{{.Names}}")
if [ ! -z "$UNHEALTHY" ]; then
    echo "$(date): UNHEALTHY CONTAINERS: $UNHEALTHY" >> "$LOG_FILE"
fi

# Check for stopped containers (that should be running)
STOPPED=$(docker ps -a --filter status=exited --filter restart=unless-stopped --format "{{.Names}}")
if [ ! -z "$STOPPED" ]; then
    echo "$(date): STOPPED CONTAINERS: $STOPPED" >> "$LOG_FILE"
fi
```

Save and make executable:
```bash
chmod +x /home/john/vps-setup/scripts/container-health.sh
```
</create-container-health>

<configure-cron>
Set up cron jobs for monitoring:
```bash
crontab -e
```

Add the following lines:
```cron
# Disk space monitoring - daily at 2 AM
0 2 * * * /home/john/vps-setup/scripts/disk-monitor.sh

# Container health check - every 6 hours
0 */6 * * * /home/john/vps-setup/scripts/container-health.sh
```

Save and exit.

Verify cron jobs:
```bash
crontab -l
```
</configure-cron>

<test-scripts>
Test scripts manually:
```bash
/home/john/vps-setup/scripts/disk-monitor.sh
/home/john/vps-setup/scripts/container-health.sh
```

Check log files created:
```bash
ls -la /home/john/vps-setup/logs/monitoring/
cat /home/john/vps-setup/logs/monitoring/container-health.log
```
</test-scripts>

<confirmation>
Ask user to confirm: "Monitoring scripts configured and cron jobs active"
Mark phase complete.
</confirmation>
</phase>

<phase id="9" name="configure-automatic-updates">
<action>Install and configure unattended-upgrades for automatic security updates.</action>

<location-marker>On the VPS</location-marker>

<rationale>
Automatic security updates ensure the VPS receives critical patches without manual intervention, reducing exposure to known vulnerabilities.
</rationale>

<commands>
Install unattended-upgrades:
```bash
sudo apt install -y unattended-upgrades
```

Configure automatic security updates:
```bash
sudo dpkg-reconfigure -plow unattended-upgrades
```

Select "Yes" when prompted.

Verify configuration:
```bash
cat /etc/apt/apt.conf.d/20auto-upgrades
```

Expected content:
```
APT::Periodic::Update-Package-Lists "1";
APT::Periodic::Unattended-Upgrade "1";
```

Optional: Configure email notifications and automatic reboot (edit if desired):
```bash
sudo nano /etc/apt/apt.conf.d/50unattended-upgrades
```

Key settings (already configured by default):
- `"${distro_id}:${distro_codename}-security";` - Security updates enabled
- `//Unattended-Upgrade::Automatic-Reboot "false";` - Change to "true" for auto-reboot if needed
- `//Unattended-Upgrade::Mail "";` - Add email for notifications

Test unattended-upgrades:
```bash
sudo unattended-upgrades --dry-run --debug
```
</commands>

<confirmation>
Ask user to confirm: "Automatic security updates configured"
Mark phase complete.
</confirmation>
</phase>

<phase id="10" name="final-summary">
<action>Display completion summary, verification checklists, and next steps for application deployment.</action>

<completion-checklist>
✓ System updated with automatic security updates enabled
✓ UFW firewall configured (SSH rate limiting, HTTP/HTTPS allowed)
✓ IPv6 disabled system-wide
✓ Docker installed with comprehensive security hardening
✓ Swap file configured (2GB, swappiness=10)
✓ Directory structure created (/home/john/vps-setup/)
✓ Docker network created (jhh-net with subnet 172.18.0.0/16)
✓ Master environment file secured (.env.master)
✓ Caddy reverse proxy deployed with rate limiting and resource limits
✓ Basic monitoring configured (disk space, container health)
✓ VPS ready for containerized application deployment
</completion-checklist>

<security-verification-checklist>
**Security Verification Checklist:**

Run these commands to verify security posture:

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

**Manual Checklist:**
- [ ] SSH password authentication disabled (key-only)
- [ ] UFW firewall active with correct rules
- [ ] IPv6 disabled system-wide
- [ ] Docker daemon only on Unix socket (not TCP)
- [ ] Docker security settings applied (icc=false, no-new-privileges=true)
- [ ] Swap configured with low swappiness (10)
- [ ] Environment files secured (chmod 600)
- [ ] Caddy admin API disabled
- [ ] Caddy security headers and rate limiting active
- [ ] Caddy resource limits configured
- [ ] Automatic security updates enabled
- [ ] System timezone set (America/New_York)
</security-verification-checklist>

<operational-readiness-checklist>
**Operational Readiness Checklist:**
- [ ] Disk space monitoring active (80% threshold)
- [ ] Container health monitoring configured
- [ ] Log rotation enabled (system, Docker, Caddy)
- [ ] Monitoring cron jobs active
- [ ] System fully updated
- [ ] All services healthy
- [ ] DNS A records configured in Cloudflare (if using domains)
- [ ] Test Caddy HTTPS acquisition successful (if using domains)
</operational-readiness-checklist>

<infrastructure-summary>
**VPS Infrastructure Ready**

**Alias:** <vps_alias>
**IP:** <vps_ip>
**Domain:** <domain> (if provided)

**Firewall (UFW):**
- Default: Deny incoming, Allow outgoing
- Allowed: SSH (22/tcp with rate limiting), HTTP (80/tcp), HTTPS (443/tcp)

**Docker:**
- Version: [display from `docker --version`]
- Network: jhh-net (172.18.0.0/16)
- Security: live-restore, no-new-privileges, icc=false, Unix socket only
- Log Rotation: 10MB max size, 3 files per container

**System:**
- IPv6: Disabled
- Swap: 2GB (swappiness=10)
- Timezone: America/New_York
- Automatic Updates: Security updates enabled

**Caddy:**
- Status: Running
- Email: jhaugaard@mac.com
- Ports: 80 (HTTP), 443 (HTTPS)
- Security Headers: HSTS, X-Frame-Options, X-Content-Type-Options, etc.
- Rate Limiting: 100 requests/minute per IP
- Resource Limits: 512MB RAM, 0.5 CPU
- Health Checks: Enabled

**Monitoring:**
- Disk space: Checked daily at 2 AM (80% threshold)
- Container health: Checked every 6 hours
- Logs: /home/john/vps-setup/logs/monitoring/

**Directory Structure:**
```
/home/john/vps-setup/
├── .env.master
├── caddy/ (Caddy running)
├── apps/ (ready for deployments)
├── scripts/ (disk-monitor.sh, container-health.sh)
└── logs/monitoring/ (disk-alerts.log, container-health.log)
```
</infrastructure-summary>

<next-steps>
**Next Steps for Application Deployment:**

1. **Prepare Application**
   - Create directory: `/home/john/vps-setup/apps/<app-name>/`
   - Add `docker-compose.yml` for your application
   - Create app-specific `.env` file (can source from `../../.env.master`)

2. **Update Caddyfile**
   - Edit: `/home/john/vps-setup/caddy/Caddyfile`
   - Add subdomain configuration:
     ```caddyfile
     app.example.com {
         import security
         import ratelimit
         reverse_proxy <container-name>:8080
     }
     ```
   - Reload Caddy: `docker exec caddy caddy reload --config /etc/caddy/Caddyfile`

3. **Application docker-compose.yml Template**
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
       deploy:
         resources:
           limits:
             memory: ${DEFAULT_MEM_LIMIT}
             cpus: ${DEFAULT_CPU_LIMIT}
           reservations:
             memory: ${DEFAULT_MEM_RESERVATION}
       healthcheck:
         test: ["CMD", "wget", "--no-verbose", "--tries=1", "--spider", "http://localhost:8080/health"]
         interval: 30s
         timeout: 10s
         retries: 3

   networks:
     jhh-net:
       external: true
   ```

4. **Deploy Application**
   ```bash
   cd /home/john/vps-setup/apps/<app-name>
   docker compose up -d
   ```

5. **Verify Deployment**
   ```bash
   # Check container
   docker ps | grep my-app

   # Check logs
   docker logs my-app

   # Check health
   docker inspect --format='{{.State.Health.Status}}' my-app

   # Test endpoint
   curl https://app.example.com
   ```

6. **Connecting to External Services**
   - Homelab Supabase: `https://supabase.haugaard.dev`
   - Use HTTPS endpoints from your application containers
   - Store credentials in application `.env` files
</next-steps>

<useful-commands>
**Useful Commands:**

**Check firewall status:**
```bash
sudo ufw status verbose
```

**View all running containers:**
```bash
docker ps
```

**View Docker network:**
```bash
docker network inspect jhh-net
```

**Restart Caddy:**
```bash
cd /home/john/vps-setup/caddy
docker compose restart
```

**View Caddy logs:**
```bash
docker logs -f caddy
```

**Check disk usage:**
```bash
df -h
docker system df
```

**View monitoring logs:**
```bash
tail -f /home/john/vps-setup/logs/monitoring/disk-alerts.log
tail -f /home/john/vps-setup/logs/monitoring/container-health.log
```

**Check swap usage:**
```bash
free -h
sudo swapon --show
```

**View automatic update logs:**
```bash
cat /var/log/unattended-upgrades/unattended-upgrades.log
```

**Manual security update:**
```bash
sudo unattended-upgrades --dry-run
```
</useful-commands>

<announce>
✓ VPS Readiness Complete!

Your VPS (<vps_alias>) is now production-ready for containerized application deployments with comprehensive security hardening, automatic updates, resource management, and basic monitoring.
</announce>
</phase>

</workflow>

---

<guardrails>

<must-do>
- Warn about SSH lockout before enabling UFW
- Configure SSH (port 22) before enabling firewall
- Include location markers when switching between local/VPS execution
- Provide sudo password warnings before commands that require them
- Use ✓ checkmarks for completed major milestones
- Provide specific commands with full syntax
- Include expected outputs for verification commands
- Secure .env.master with chmod 600
- Verify Docker is accessible without sudo after installation
- Create monitoring scripts with proper permissions
- Test monitoring scripts before finishing
- Configure cron jobs for automated monitoring
- Verify health checks are working for Caddy
</must-do>

<must-not-do>
- Enable UFW without configuring SSH port first
- Skip verification steps at critical checkpoints
- Engage in unnecessary explanations unless problems occur
- Create directories or files on local machine (all work is on VPS)
- Include Backblaze B2 or backup configuration (handled externally)
- Use "homelab" terminology (this is for individual VPS instances)
- Configure Docker to listen on TCP (Unix socket only)
- Skip system updates at the beginning
- Forget to configure timezone system-wide
- Omit resource limits from Caddy configuration
- Skip health check configuration
</must-not-do>

<success-criteria>
VPS Ready When:
- ✓ System fully updated with automatic security updates enabled
- ✓ System timezone configured (America/New_York)
- ✓ UFW firewall active with correct rules (SSH rate limited, HTTP/HTTPS allowed)
- ✓ IPv6 disabled system-wide
- ✓ Docker installed and configured with comprehensive security hardening
- ✓ Docker daemon only on Unix socket (not TCP)
- ✓ Swap file active (2GB, swappiness=10)
- ✓ Directory structure created at /home/john/vps-setup/
- ✓ Docker network jhh-net exists with custom subnet
- ✓ Master environment file created and secured (chmod 600)
- ✓ Caddy running with rate limiting and resource limits
- ✓ Caddy health checks passing
- ✓ HTTPS certificates acquired (if domain provided)
- ✓ Monitoring scripts created and cron jobs configured
- ✓ VPS ready for first application deployment

Security Posture:
- ✓ SSH password authentication disabled (key-only)
- ✓ UFW enabled with default deny incoming
- ✓ SSH rate limiting active (prevents brute force)
- ✓ IPv6 disabled
- ✓ All application containers bind to 127.0.0.1 (localhost only)
- ✓ Only Caddy exposed to internet (ports 80, 443)
- ✓ Docker security hardening applied (icc=false, no-new-privileges, etc.)
- ✓ Docker log rotation prevents disk fill
- ✓ Caddy admin API disabled
- ✓ Caddy rate limiting configured (100 req/min per IP)
- ✓ Caddy resource limits prevent runaway usage
- ✓ Environment files secured with proper permissions
- ✓ Swap configured to prevent OOM crashes
- ✓ Automatic security updates enabled

Operational Readiness:
- ✓ Disk space monitoring active (80% threshold)
- ✓ Container health monitoring configured
- ✓ Log rotation enabled (system, Docker, Caddy)
- ✓ Monitoring cron jobs active and tested
- ✓ All services healthy
- ✓ Documentation and useful commands provided
</success-criteria>

</guardrails>

---

**Skill Version:** 2.0
**Created:** November 20, 2024
**Updated:** November 21, 2024
**Tested On:** Hostinger VPS (Ubuntu 24)
**Known Compatible:** VPS instances with Ubuntu/Debian, SSH pre-configured
**Learning Focus:** Production-ready VPS setup, Docker best practices, security hardening, resource management, operational monitoring
