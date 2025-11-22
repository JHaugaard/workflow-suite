---
name: ssh-setup
description: "Guided workflow for setting up secure SSH access to VPS servers with key-based authentication, disabled password authentication, and sudo privileges. This skill should be used when configuring SSH access to a new VPS from multiple client machines (e.g., MacBook and Mini), ensuring proper security hardening including disabled root login and password authentication."
---

# ssh-setup

<purpose>
Guide users through secure SSH setup for VPS servers with key-based authentication, non-root sudo user, disabled root login, disabled password authentication, and multi-machine support using checkpoint-based verification. Prepares VPS for subsequent vps-ready skill deployment.
</purpose>

<output>
- Configured VPS with non-root user (john) having sudo privileges
- SSH key-based authentication from client machine(s)
- Disabled root SSH access
- Disabled password authentication (key-only access)
- Local SSH config entries for alias-based connections
- State file tracking setup progress at ~/ssh-setup/state/<vps_alias>.json
</output>

<role>
Business-like, efficient technical guide. Direct and succinct communication. Use clear status indicators (✓ for completed steps). Provide explicit warnings before security-critical operations. Include location context markers when switching between local/remote execution (e.g., "On your local machine (MacBook)...", "On the VPS..."). Warn about password prompts before commands that require them. No unnecessary affirmations or chitchat.
</role>

---

<workflow>

<phase id="0" name="gather-information">
<action>Determine setup phase and collect required VPS details.</action>

<required-information>
Ask the user for:
1. **VPS IP Address**
2. **VPS Alias** (e.g., vps_new, vps_production)
3. **Root Password** (from VPS hosting control panel - will be used 3 times during setup)
4. **Current Machine** (MacBook, Mini, or other identifier)
5. **Is this the first machine for this VPS?** (yes/no)
</required-information>

<security-note>
Root password will be used 3 times during initial setup (phases 3, 4, 5) and transmitted securely over SSH encrypted connection. Root access will be completely disabled in phase 8.
</security-note>

<setup-phase-detection>
- If first machine: Execute first-machine-setup workflow (phases 1-9)
- If secondary machine: Execute secondary-machine-setup workflow (phases 10-12)
- If secondary machine but first machine setup incomplete: Warn that secondary setup requires completed primary setup
</setup-phase-detection>

<state-file-location>
~/ssh-setup/state/<vps_alias>.json

Note: State files are stored locally on each machine (not shared between machines). Each machine maintains its own state file for the same VPS.
</state-file-location>

<state-file-structure>
{
  "session_id": "<vps_alias>_<YYYYMMDD>",
  "vps": {
    "alias": "<alias>",
    "hostname": "<hostname>",
    "ip": "<ip_address>",
    "username": "john"
  },
  "ssh_key": "~/.ssh/id_ed25519_hostinger",
  "checkpoints": {
    "first_machine_key_exists": true/false,
    "first_machine_config_updated": true/false,
    "user_created_on_vps": true/false,
    "key_copied_to_vps": true/false,
    "user_ssh_directory_permissions_fixed": true/false,
    "user_can_sudo": true/false,
    "user_ssh_works": true/false,
    "root_disabled": true/false,
    "password_auth_disabled": true/false,
    "secondary_machine_key_synced": true/false,
    "secondary_machine_config_updated": true/false,
    "secondary_machine_ssh_works": true/false
  },
  "machines": {
    "<first_machine>": "complete",
    "<second_machine>": "pending/complete"
  },
  "setup_date": "YYYY-MM-DD",
  "completed_date": "YYYY-MM-DD"
}
</state-file-structure>

<initialization>
After gathering information, create state directory and file with initial values:

```bash
mkdir -p ~/ssh-setup/state
```

Create state file at: ~/ssh-setup/state/<vps_alias>.json with structure above, setting all checkpoints to false, setup_date to current date.
</initialization>
</phase>

<phase id="1" name="ssh-key-detection">
<action>Check if standard Hostinger SSH key exists on local machine, or generate new one.</action>

<location-marker>On local machine (<current_machine>)</location-marker>

<verification-command>
```bash
ls -la ~/.ssh/id_ed25519_hostinger*
```
</verification-command>

<if-key-exists>
- Verify permissions: private key (600), public key (644)
- Proceed with existing key
- Update state: first_machine_key_exists: true
</if-key-exists>

<if-key-not-exists>
Provide key generation command:
```bash
ssh-keygen -t ed25519 -f ~/.ssh/id_ed25519_hostinger -C "john@<machine>-hostinger"
```

Instruct user to press Enter for no passphrase.

Verify creation:
```bash
ls -la ~/.ssh/id_ed25519_hostinger*
```

Update state: first_machine_key_exists: true
</if-key-not-exists>

<confirmation>
Ask user to confirm: "SSH key ready"
Update state file: first_machine_key_exists: true
</confirmation>
</phase>

<phase id="2" name="local-ssh-config">
<action>Automatically add SSH config entry for VPS alias with duplicate prevention.</action>

<location-marker>On local machine (<current_machine>)</location-marker>

<automated-config-setup>
Provide the following command sequence for automated SSH config setup:

```bash
# Ensure .ssh directory and config file exist
mkdir -p ~/.ssh
touch ~/.ssh/config
chmod 700 ~/.ssh

# Check if alias already exists to prevent duplicates
if ! grep -q "^Host <alias>$" ~/.ssh/config; then
    cat >> ~/.ssh/config << 'EOF'

# Hostinger VPS - <alias>
Host <alias>
    HostName <ip_address>
    User john
    IdentityFile ~/.ssh/id_ed25519_hostinger
    IdentitiesOnly yes
EOF
    chmod 600 ~/.ssh/config
    echo "✓ SSH config entry added for <alias>"
else
    echo "✓ SSH config entry for <alias> already exists - skipping"
fi
```

Verify the configuration was added:
```bash
cat ~/.ssh/config | grep -A 6 "Host <alias>"
```

Expected output should show the config block.
</automated-config-setup>

<confirmation>
Ask user to confirm: "Config entry added and verified"
Update state: first_machine_config_updated: true
</confirmation>
</phase>

<phase id="3" name="create-vps-user">
<action>Create non-root user on VPS with sudo privileges and SSH directory.</action>

<location-marker>On the VPS (logged in as root)</location-marker>

<password-warning>
You'll be prompted for root password (first use of 3)
</password-warning>

<commands>
Connect to VPS:
```bash
ssh root@<ip_address>
```

Execute on VPS (checks if user exists first):
```bash
# Check if user john exists, create if not
if ! id john >/dev/null 2>&1; then
    adduser john
else
    echo "User john already exists"
fi

# Ensure user has sudo privileges
usermod -aG sudo john

# Create SSH directory with proper permissions
mkdir -p /home/john/.ssh
chmod 700 /home/john/.ssh

exit
```
</commands>

<confirmation>
Ask user to confirm: "User created, back on local machine"
Update state: user_created_on_vps: true
</confirmation>
</phase>

<phase id="4" name="copy-ssh-key-to-vps">
<action>Copy local SSH public key to VPS root account and verify.</action>

<location-marker>On local machine</location-marker>

<password-warning>
You'll be prompted for root password (second use of 3)
</password-warning>

<commands>
Copy SSH key to VPS:
```bash
ssh-copy-id -i ~/.ssh/id_ed25519_hostinger.pub root@<ip_address>
```

Verify key was copied successfully:
```bash
ssh -i ~/.ssh/id_ed25519_hostinger root@<ip_address> echo "✓ Key verified"
```

Expected: "✓ Key verified" output without password prompt
</commands>

<confirmation>
Ask user to confirm: "Key copied and verified"
Update state: key_copied_to_vps: true
</confirmation>
</phase>

<phase id="5" name="setup-user-authorized-keys">
<action>Copy authorized keys from root to user account and fix all permissions.</action>

<location-marker>On the VPS (logged in as root)</location-marker>

<password-warning>
You'll be prompted for root password (third and final use)
</password-warning>

<commands>
Connect to VPS:
```bash
ssh root@<ip_address>
```

Execute on VPS:
```bash
# Copy authorized_keys from root to john
cp /root/.ssh/authorized_keys /home/john/.ssh/authorized_keys

# Set correct ownership and permissions
chown john:john /home/john/.ssh/authorized_keys
chmod 600 /home/john/.ssh/authorized_keys

# CRITICAL: Verify entire .ssh directory ownership
chown -R john:john /home/john/.ssh

exit
```
</commands>

<confirmation>
Ask user to confirm: "Authorized keys configured"
Update state: user_ssh_directory_permissions_fixed: true
</confirmation>
</phase>

<phase id="6" name="verify-sudo-access">
<action>Test that user account has sudo privileges.</action>

<location-marker>On the VPS (logged in as root)</location-marker>

<commands>
Connect as root:
```bash
ssh root@<ip_address>
```

Switch to user and test sudo:
```bash
su - john
sudo whoami
```

Expected output: `root`
</commands>

<password-note>
Sudo will prompt for john's password (the password you set in phase 3). Enter that password.
</password-note>

<exit-commands>
```bash
exit
exit
```
</exit-commands>

<confirmation>
Ask user to confirm: "Output was 'root' and back on local machine"
Update state: user_can_sudo: true
</confirmation>
</phase>

<phase id="7" name="test-user-ssh-access">
<action>Verify passwordless SSH access using alias works for non-root user.</action>

<location-marker>On local machine</location-marker>

<critical-checkpoint>
This is a CRITICAL security checkpoint. User SSH MUST work before proceeding to phase 8.
</critical-checkpoint>

<test-command>
```bash
ssh <alias>
```

Should connect without password prompt.
</test-command>

<troubleshooting>
If fails with hostname resolution:
- SSH config wasn't properly saved
- Re-verify config file contents with: cat ~/.ssh/config
- Re-run phase 2 automated config setup

If connects but prompts for password:
- Authorized keys issue on VPS
- Return to phase 5, verify ownership with chown -R command
- Check: `ls -la /home/john/.ssh/` (should show john:john ownership)
</troubleshooting>

<confirmation>
Ask user to confirm: "Logged in as john without password"
Update state: user_ssh_works: true

**CRITICAL**: Do NOT proceed to phase 8 unless this verification passes.
</confirmation>
</phase>

<phase id="8" name="disable-root-and-password-authentication">
<action>Disable root SSH access AND password authentication after verifying user SSH works.</action>

<critical-warning>
This will prevent root SSH access AND disable password authentication (key-only access).
User login is verified and working.
Require explicit confirmation: "I tested and john SSH works via the alias"
</critical-warning>

<location-marker>On the VPS (logged in as user)</location-marker>

<commands>
Connect as user:
```bash
ssh <alias>
```

Edit SSH daemon config:
```bash
sudo nano /etc/ssh/sshd_config
```

Find and change BOTH settings:
```
PermitRootLogin no
PasswordAuthentication no
```

Save and exit (Ctrl+X, Y, Enter).

Restart SSH service (service name varies by system):
```bash
sudo systemctl restart sshd
```

If fails with "Unit sshd.service not found", try:
```bash
sudo systemctl restart ssh
```

Exit:
```bash
exit
```
</commands>

<confirmation>
Ask user to confirm: "Root login and password authentication disabled"
Update state:
- root_disabled: true
- password_auth_disabled: true
</confirmation>
</phase>

<phase id="9" name="final-verification">
<action>Verify root access is denied, password authentication is disabled, and user access works.</action>

<location-marker>On local machine</location-marker>

<verification-tests>
Test 1 - Root should fail:
```bash
ssh root@<ip_address>
```
Expected: "Permission denied (publickey)"

Test 2 - User should work:
```bash
ssh <alias>
```
Expected: Successful connection without password

Test 3 - Verify PasswordAuthentication is disabled:
```bash
ssh <alias> "sudo grep '^PasswordAuthentication' /etc/ssh/sshd_config"
```
Expected: "PasswordAuthentication no"
</verification-tests>

<confirmation>
Ask user to confirm: "All three tests passed"

Update state:
- machines.<first_machine>: "complete"
- completed_date: <current_date>

Announce: "✓ <First Machine> Setup Complete - VPS Ready for vps-ready Skill"
</confirmation>
</phase>

<phase id="10" name="secondary-key-transfer">
<action>Transfer or verify SSH key exists on secondary machine.</action>

<location-marker>On secondary machine (<secondary_machine>)</location-marker>

<key-check>
Verify key exists:
```bash
ls -la ~/.ssh/id_ed25519_hostinger*
```
</key-check>

<if-key-exists>
- Keys already synced between machines
- Verify permissions: private (600), public (644)
- Skip transfer
- Update state: secondary_machine_key_synced: true
</if-key-exists>

<if-key-not-exists>
<security-warning>
WARNING: This method displays your private key in the terminal. Ensure no one is watching your screen and no screen sharing is active.
</security-warning>

On primary machine, display private key:
```bash
cat ~/.ssh/id_ed25519_hostinger
```
Instruct: "Copy entire output including BEGIN/END lines"

On secondary machine, create key file:
```bash
mkdir -p ~/.ssh
chmod 700 ~/.ssh
nano ~/.ssh/id_ed25519_hostinger
```
Paste copied content, save (Ctrl+X, Y, Enter).

Also create public key:
```bash
nano ~/.ssh/id_ed25519_hostinger.pub
```
Paste public key content (from primary machine: `cat ~/.ssh/id_ed25519_hostinger.pub`)

Set permissions:
```bash
chmod 600 ~/.ssh/id_ed25519_hostinger
chmod 644 ~/.ssh/id_ed25519_hostinger.pub
```

Update state: secondary_machine_key_synced: true
</if-key-not-exists>

<confirmation>
Ask user to confirm: "Key transferred" or "Key already exists"
</confirmation>
</phase>

<phase id="11" name="secondary-ssh-config">
<action>Automatically add SSH config entry on secondary machine.</action>

<location-marker>On secondary machine (<secondary_machine>)</location-marker>

<automated-config-setup>
Provide the following command sequence for automated SSH config setup:

```bash
# Ensure .ssh directory and config file exist
mkdir -p ~/.ssh
touch ~/.ssh/config
chmod 700 ~/.ssh

# Check if alias already exists to prevent duplicates
if ! grep -q "^Host <alias>$" ~/.ssh/config; then
    cat >> ~/.ssh/config << 'EOF'

# Hostinger VPS - <alias>
Host <alias>
    HostName <ip_address>
    User john
    IdentityFile ~/.ssh/id_ed25519_hostinger
    IdentitiesOnly yes
EOF
    chmod 600 ~/.ssh/config
    echo "✓ SSH config entry added for <alias>"
else
    echo "✓ SSH config entry for <alias> already exists - skipping"
fi
```

Verify the configuration:
```bash
cat ~/.ssh/config | grep -A 6 "Host <alias>"
```
</automated-config-setup>

<confirmation>
Ask user to confirm: "Config entry added and verified"
Update state: secondary_machine_config_updated: true
</confirmation>
</phase>

<phase id="12" name="test-secondary-connection">
<action>Verify passwordless SSH works from secondary machine.</action>

<location-marker>On secondary machine</location-marker>

<test-command>
```bash
ssh <alias>
```

Should connect without password.
</test-command>

<confirmation>
Ask user to confirm: "Connected from secondary machine without password"

Update state:
- secondary_machine_ssh_works: true
- machines.<secondary_machine>: "complete"
- completed_date: <current_date>

Announce: "✓ <Secondary Machine> Setup Complete"
</confirmation>
</phase>

</workflow>

---

<guardrails>

<must-do>
- Warn about password prompts before commands that require authentication
- Inform user that root password will be used 3 times (phases 3, 4, 5)
- Include location markers when switching between local/remote execution
- Require explicit user confirmation before disabling root SSH and password auth (phase 8)
- Verify user SSH works (phase 7) before allowing phase 8
- Update state file after each completed phase
- Use ✓ checkmarks for completed major milestones
- Provide specific commands with full syntax
- Include expected outputs for verification commands
- Create state directory before creating state file
- Use automated SSH config setup (Option B) for reliability
- Verify PasswordAuthentication is disabled in phase 9
</must-do>

<must-not-do>
- Execute commands that disable root access without verified user access
- Skip verification steps at security checkpoints
- Engage in unnecessary explanations unless problems occur
- Proceed to secondary machine setup if primary setup incomplete
- Allow root lockout scenarios (phase 7 must pass before phase 8)
- Proceed to phase 8 without explicit user confirmation
- Forget to disable PasswordAuthentication in phase 8
</must-not-do>

<success-criteria>
First Machine Complete:
- User can SSH via alias without password
- User has sudo privileges
- Root SSH login disabled
- Password authentication disabled (key-only)
- PasswordAuthentication no verified in sshd_config
- State file saved with all first-machine checkpoints true
- VPS ready for vps-ready skill

Secondary Machine Complete:
- Secondary machine can SSH via alias without password
- Uses same key as primary machine
- State file updated with secondary machine status

Security Posture:
- Key-based authentication only (PasswordAuthentication no)
- Root login disabled (PermitRootLogin no)
- Non-root user (john) with sudo access
- All file permissions correct (SSH: 700/600, config: 600)
- VPS ready for vps-ready skill deployment
</success-criteria>

</guardrails>

---

<integration-with-vps-ready>
This skill prepares the VPS to meet ALL prerequisites for the vps-ready skill:
✓ SSH access configured (passwordless key-based)
✓ Non-root user `john` with sudo privileges
✓ PasswordAuthentication disabled (key-only access)
✓ Root login disabled
✓ Ubuntu/Debian operating system (assumed)

After completing this skill, the VPS is ready for the vps-ready skill to install Docker, Caddy, firewall, and production infrastructure.
</integration-with-vps-ready>

---

**Skill Version:** 2.0
**Created:** November 20, 2024
**Updated:** November 21, 2024
**Tested On:** Hostinger VPS (Ubuntu 24)
**Known Compatible:** MacBook, Mac Mini client machines
**Learning Focus:** Secure SSH setup, key-based authentication, security hardening, VPS preparation
