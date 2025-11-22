# ssh-setup - Secure VPS SSH Configuration Skill

## Overview

The **ssh-setup** skill is a comprehensive guided workflow for setting up secure SSH access to VPS servers with key-based authentication, disabled password authentication, and proper security hardening. It prepares your VPS for production-ready deployments by establishing a secure foundation before running infrastructure setup tools.

**Version:** 2.0
**Created:** November 20, 2024
**Updated:** November 21, 2024
**Tested On:** Hostinger VPS (Ubuntu 24)

---

## What This Skill Does

### First Machine Setup (Primary)
Configures a VPS for secure SSH access from your primary machine:

1. ✓ Detects or generates SSH key pair (ed25519)
2. ✓ **Automatically configures** local SSH config with alias
3. ✓ Creates non-root user (john) with sudo privileges
4. ✓ Copies SSH key to VPS
5. ✓ Configures user's authorized_keys with proper permissions
6. ✓ Verifies sudo access
7. ✓ **Critical checkpoint:** Tests SSH connection works
8. ✓ Disables root login AND password authentication
9. ✓ Performs final security verification
10. ✓ Saves state for secondary machines

### Secondary Machine Setup
Extends SSH access to additional machines:

1. ✓ Transfers or verifies SSH key exists
2. ✓ **Automatically configures** local SSH config
3. ✓ Tests connection
4. ✓ Updates state file

---

## Key Features

### 🔒 Security Hardening
- **Key-based authentication only** (PasswordAuthentication no)
- **Root login disabled** (PermitRootLogin no)
- **Non-root user with sudo** access
- **Proper file permissions** (SSH: 700/600, config: 600)
- **Checkpoint-based verification** prevents lockouts

### 🚀 Automated SSH Config (NEW in v2.0)
- **No manual editing required** - automated bash script
- **Idempotent** - safe to run multiple times
- **Duplicate prevention** - checks before adding entries
- **User-verifiable** - can inspect config afterward

### 📊 State Tracking
- **JSON state files** track progress: `~/ssh-setup/state/<vps_alias>.json`
- **Resumable** - can continue interrupted setups
- **Multi-machine support** - tracks each machine separately
- **Per-machine state** - each machine maintains its own state file

### 🔗 Perfect vps-ready Integration
Delivers **ALL** prerequisites expected by the vps-ready skill:
- ✓ SSH access configured (passwordless key-based)
- ✓ Non-root user `john` with sudo privileges
- ✓ **PasswordAuthentication disabled** (NEW in v2.0)
- ✓ Root login disabled
- ✓ Ubuntu/Debian operating system

---

## Installation

### Install to Claude Desktop

1. Create the skills directory:
```bash
mkdir -p ~/.claude/skills/user/ssh-setup
```

2. Copy the SKILL.md file:
```bash
cp SKILL.md ~/.claude/skills/user/ssh-setup/
```

3. Restart Claude Desktop

The skill will be automatically available.

---

## Usage

### Starting a New VPS Setup

In Claude Desktop/Code, start with:

```
Set up SSH for new VPS at <ip_address>
```

Or simply:

```
SSH setup for VPS
```

The skill will ask for:
- **VPS IP Address**
- **VPS Alias** (e.g., vps_new, vps_production)
- **Root Password** (will be used 3 times, then root access disabled)
- **Current Machine** (MacBook, Mini, etc.)
- **Is this the first machine for this VPS?** (yes/no)

### Resuming on Secondary Machine

On your second machine (e.g., Mini after setting up MacBook):

```
Configure SSH for <vps_alias> on this machine
```

The skill will guide you through the simpler secondary machine setup.

---

## What's New in v2.0

### 🎯 Critical Fix: Password Authentication Disabled
- **Phase 8 now disables BOTH** root login AND password authentication
- **New checkpoint** added: `password_auth_disabled`
- **Phase 9 verification** confirms PasswordAuthentication is disabled
- **Perfect mesh** with vps-ready skill requirements

### 🤖 Automated SSH Config Setup
- **No more manual nano editing** - fully automated
- **Conditional append** with duplicate detection
- **Creates .ssh directory** and config file if missing
- **Safe and idempotent** - can run multiple times

### 🔧 Improved Workflow
- **State directory creation** in Phase 0 (`mkdir -p ~/ssh-setup/state`)
- **Phase 3 user check** - handles existing user gracefully
- **Phase 4 verification** - confirms key copy succeeded
- **Phase 6 password reminder** - helps user remember password
- **Root password transparency** - warns user it will be used 3 times
- **Security warning** for key transfer in Phase 10

### 📝 Enhanced Documentation
- **State file per-machine note** - clarifies local storage
- **Integration section** - explicit vps-ready prerequisites
- **Security notes** - explains root password handling
- **Updated guardrails** - comprehensive must-do/must-not-do

---

## Requirements

### VPS Requirements
- Ubuntu or Debian-based system
- Root access (password from hosting panel)
- SSH enabled (port 22)

### Client Requirements
- macOS, Linux, or WSL
- SSH client installed
- Bash shell

---

## State Files

State files are automatically created at:
```
~/ssh-setup/state/<vps_alias>.json
```

**Important:** State files are stored **locally on each machine**, not shared between machines. Each machine maintains its own state file for the same VPS.

### State File Contents
- VPS details (alias, IP, username)
- Completed checkpoints
- Machines configured
- Setup dates

State files enable:
- Resuming interrupted setups
- Configuring multiple machines
- Verification of what's complete

---

## Security Features

### Authentication & Access
- ✓ Key-based authentication (ed25519, no passphrase)
- ✓ Password authentication disabled system-wide
- ✓ Root SSH login disabled
- ✓ Non-root user with sudo access

### Root Password Handling
- ✓ Used only 3 times during initial setup (phases 3, 4, 5)
- ✓ Transmitted securely over SSH encrypted connection
- ✓ Root access completely disabled after setup
- ✓ Standard industry practice for VPS provisioning

### Checkpoint-Based Verification
- ✓ Phase 7: **Critical checkpoint** - user SSH MUST work
- ✓ Phase 8: Requires explicit confirmation before lockdown
- ✓ Phase 9: Triple verification (root denied, user works, PasswordAuthentication off)
- ✓ Prevents lockout scenarios

### File Permissions
- ✓ SSH directories: 700
- ✓ SSH keys: 600 (private), 644 (public)
- ✓ SSH config: 600
- ✓ Automated verification

---

## Testing

Successfully tested on:
- **VPS:** Hostinger Ubuntu 24 servers
- **Clients:** MacBook Pro, Mac Mini
- **Date:** November 20-21, 2024

### Test Coverage
- ✓ First machine setup (clean VPS)
- ✓ Secondary machine setup
- ✓ User already exists handling
- ✓ Duplicate config entry prevention
- ✓ Root lockout prevention
- ✓ Password authentication disabled verification
- ✓ vps-ready integration

---

## Common Issues & Solutions

### "Could not resolve hostname"
**Cause:** SSH config not properly saved or alias not found

**Solution:**
```bash
# Verify config exists
cat ~/.ssh/config | grep "Host <alias>"

# Re-run phase 2 automated config setup
mkdir -p ~/.ssh && touch ~/.ssh/config && chmod 700 ~/.ssh
# Then run the conditional append command from phase 2
```

### Password still prompted when connecting
**Cause:** Authorized keys issue on VPS

**Solution:**
```bash
# On VPS, verify ownership
ssh root@<ip_address>
ls -la /home/john/.ssh/
# Should show: drwx------ john john

# Fix if needed
chown -R john:john /home/john/.ssh
chmod 700 /home/john/.ssh
chmod 600 /home/john/.ssh/authorized_keys
```

### sshd service not found
**Cause:** Service name varies by system

**Solution:**
```bash
# Try alternative service name
sudo systemctl restart ssh
```

### User john already exists
**Cause:** User created in previous attempt

**Solution:** This is now handled automatically in v2.0. Phase 3 checks if user exists before creating.

---

## After Setup: Next Steps

Once ssh-setup completes successfully, your VPS is ready for:

### 1. Run vps-ready Skill
```
Make my VPS production-ready
```

The vps-ready skill will:
- Install Docker with security hardening
- Configure UFW firewall
- Deploy Caddy reverse proxy
- Set up monitoring
- Configure automatic updates

### 2. Manual Connections
Connect via alias:
```bash
ssh <vps_alias>
```

Copy files:
```bash
scp file.txt <vps_alias>:/home/john/
```

Run commands:
```bash
ssh <vps_alias> 'docker ps'
```

---

## Workflow Phases

### First Machine (Phases 0-9)
0. **Gather Information** - Collect VPS details, create state directory
1. **SSH Key Detection** - Generate or verify ed25519 key
2. **Local SSH Config** - **Automated** config entry with duplicate check
3. **Create VPS User** - Create john with sudo, handle existing user
4. **Copy SSH Key** - Copy key to root, **verify** copy succeeded
5. **Setup Authorized Keys** - Copy to john, fix permissions
6. **Verify Sudo** - Test sudo access with **password reminder**
7. **Test User SSH** - **Critical checkpoint** - must pass before phase 8
8. **Disable Root & Password Auth** - Both disabled, requires confirmation
9. **Final Verification** - Triple test (root denied, user works, password auth off)

### Secondary Machine (Phases 10-12)
10. **Key Transfer** - Transfer key with **security warning**
11. **SSH Config** - **Automated** config entry
12. **Test Connection** - Verify secondary machine works

---

## Security Best Practices

### What This Skill Enforces
✓ Key-based authentication only (no passwords)
✓ Root login disabled
✓ Non-root user with sudo (principle of least privilege)
✓ Proper file permissions
✓ Checkpoint verification prevents lockouts

### What You Should Do After
- Change john's password to a strong passphrase
- Consider changing root password via VPS control panel
- Run vps-ready skill for additional hardening (UFW, Docker security)
- Set up fail2ban (included in vps-ready)
- Configure automatic security updates (included in vps-ready)

---

## Future Enhancements

Potential additions:
- Multiple user setup
- Team access scenarios
- Custom SSH port configuration
- Integration with VPS provider APIs
- Automated key transfer via SCP (instead of manual copy/paste)

---

## Version History

### v2.0 (November 21, 2024)
**Major Update - Security & Automation**

**Critical Fixes:**
- ✓ Phase 8 now disables BOTH root login AND password authentication
- ✓ Added `password_auth_disabled` checkpoint to state structure
- ✓ Phase 9 verifies PasswordAuthentication is disabled
- ✓ Perfect integration with vps-ready skill prerequisites

**Automation Improvements:**
- ✓ Automated SSH config setup (no manual nano editing)
- ✓ Conditional append with duplicate detection
- ✓ Idempotent operations (safe to run multiple times)

**Workflow Enhancements:**
- ✓ State directory creation in Phase 0
- ✓ User existence check in Phase 3
- ✓ Key copy verification in Phase 4
- ✓ Password reminder in Phase 6
- ✓ Root password usage transparency
- ✓ Security warning for key transfer in Phase 10

**Documentation:**
- ✓ State file per-machine clarification
- ✓ Integration section with vps-ready
- ✓ Root password security analysis
- ✓ Updated guardrails and success criteria

### v1.0 (November 20, 2024)
**Initial Release**

- Complete workflow for secure VPS SSH setup
- Multi-machine support (primary + secondary)
- State file tracking for resumable sessions
- Checkpoint-based verification preventing root lockout
- Manual SSH config editing
- Detailed command-by-command guidance

---

## Support

**Issues:** Report issues in your skill management system

**Documentation:** See SKILL.md for complete technical specification

**Compatibility:** Tested on Ubuntu/Debian VPS instances with Hostinger

---

## License

Personal use

---

## Author

Built for learning and practical VPS management. Focus on security best practices and seamless integration with production infrastructure tools.

---

**Ready to secure your VPS?** Run the ssh-setup skill and establish a hardened SSH foundation in minutes! 🚀🔒
