# SSH Setup Agent - Quick Start

## 30-Second Installation

**Step 1: Download the files**
Download the `ssh-setup-agent` folder from Claude

**Step 2: Install to Claude Desktop**
```bash
mkdir -p ~/.claude/skills/user/ssh-setup-agent
cp ~/Downloads/ssh-setup-agent/SKILL.md ~/.claude/skills/user/ssh-setup-agent/
```

**Step 3: Restart Claude Desktop**

**Step 4: Start using it**
```
SSH Setup Agent - configure VPS at 1.2.3.4
```

Done! Follow the prompts.

---

## Your First VPS Setup

### What You Need
- VPS IP address (from hosting control panel)
- Root password (from hosting control panel)
- 10 minutes

### Example Session

**You say:**
```
Set up SSH for new VPS
```

**Agent asks:**
- VPS IP Address: `31.97.100.193`
- VPS Alias: `vps_new`
- Root Password: `[paste from hosting panel]`
- Current Machine: `MacBook`
- First machine?: `yes`

**Agent guides you through:**
1. Key detection/creation (2 min)
2. SSH config setup (1 min)
3. VPS user creation (2 min)
4. Key transfer (1 min)
5. Permission fixes (1 min)
6. Security verification (2 min)
7. Root disable (1 min)

**Result:**
```bash
ssh vps_new
# You're in! No password needed.
```

---

## Adding Your Second Machine

Later on your Mini/Desktop:

**You say:**
```
SSH Setup Agent - Mini for vps_new
```

**Agent asks:**
- Same VPS details
- Answers "Is this first machine?" → `no`

**Agent guides you through:**
1. Key transfer (2 min)
2. SSH config (1 min)
3. Connection test (30 sec)

**Result:**
```bash
ssh vps_new
# Works from this machine too!
```

---

## What Makes This Different

**Traditional setup problems:**
- ❌ Forget steps
- ❌ Wrong permissions
- ❌ Lock yourself out
- ❌ No verification gates
- ❌ Copy/paste errors

**With this agent:**
- ✅ Checkpoint-based workflow
- ✅ Automatic verification
- ✅ Safety gates before lockdown
- ✅ State tracking across machines
- ✅ Clear error messages
- ✅ Resume capability

---

## Real Example

**VPS:** Hostinger 31.97.100.193  
**Date:** November 20, 2024  
**Machines:** MacBook → Mini  
**Time:** 25 minutes total  
**Issues Found:** 1 (directory ownership - agent detected and fixed)  
**Result:** Both machines connecting securely ✓

---

## Pro Tips

**Test VPS Setup:**
- Use Hostinger 1-month trial ($3-4)
- Spin up, test, destroy, repeat
- Perfect for learning/refining

**State Files:**
- Saved at `~/ssh-setup-agent/state/<vps_alias>.json`
- Track what's done
- Enable resume on interruption
- Support multiple machines

**Security First:**
- Agent verifies SSH works before disabling root
- Checkpoints prevent lockout
- You confirm critical operations
- Always have hosting panel access as backup

---

## Next Steps

1. **Install** (commands above)
2. **Test** with trial VPS (optional but recommended)
3. **Use** on real VPS when confident
4. **Add** second machine when needed

**Remember:** This is YOUR agent. It works how you work. No surprises, just guided security setup.

---

**Version:** 1.0  
**Last Updated:** November 20, 2024  
**Status:** Production-ready (tested successfully)
