# Emergency Recovery and Troubleshooting

**Purpose:** Diagnose and recover from system failures, connection loss, or configuration corruption without losing data or requiring full rebuild.

## 1. System Health Diagnostics

Before attempting recovery, understand the current state.

### 1.1 Quick Status Check

```bash
# Check if nanobot is running
pgrep -l nanobot

# Check gateway status
nanobot status --all

# List connected nodes (if distributed)
nanobot nodes list

# Review recent logs
tail -50 /var/log/nanobot/gateway.log
```

### 1.2 Connectivity Diagnostic

```bash
# Test LLM provider connectivity
nanobot test-provider --provider openrouter
nanobot test-provider --provider ollama --host localhost:11434

# Test channel integrations
nanobot test-channel --channel discord
nanobot test-channel --channel slack

# Verify Obsidian vault access
nanobot test-vault --path /path/to/vault
```

### 1.3 Configuration Validation

```bash
# Validate configuration file syntax
nanobot config validate

# Check for deprecated settings
nanobot config audit

# Show active configuration
nanobot config show --include-secrets=false  # Never include secrets
```

## 2. Common Issues & Quick Fixes

### 2.1 "Cannot connect to LLM provider"

**Symptoms:**
- Responses timeout
- "Provider unreachable" errors
- No tokens being consumed

**Diagnosis:**
```bash
# Check network connectivity
curl -I https://api.openrouter.ai  # For OpenRouter
ping <ollama-host>                  # For local Ollama
```

**Fix Options:**

**Option A: Verify credentials** (most common)
```bash
# Check if API key is set
echo $OPENROUTER_API_KEY

# If empty, reload from .env
source ~/.nanobot/.env
nanobot gateway restart
```

**Option B: Check firewall** (VPS → cloud provider)
```bash
# Verify port accessibility
sudo ufw status
sudo ufw allow out 443  # HTTPS for cloud providers

# For Ollama on local network
sudo ufw allow in 11434
```

**Option C: Reset provider routing**
```bash
# Temporarily use fallback provider
nanobot config set llm.provider fallback

# Test with a simple prompt
/model test "Say hello"

# Once working, switch back to preferred provider
nanobot config set llm.provider openrouter
```

### 2.2 "Connection refused" from Discord/Slack

**Symptoms:**
- Messages sent to bot are ignored
- "Unauthorized" errors in channel logs
- Gateway won't start

**Diagnosis:**
```bash
# Check if gateway is listening
netstat -tlnp | grep nanobot

# Verify channel token is valid
nanobot channel list --show-auth-status
```

**Fix:**

**Option A: Regenerate channel token**
```bash
# For Discord
nanobot channel discord reset-token
# Copy new token to Discord application settings

# For Slack
nanobot channel slack regenerate-token
# Update Slack app credentials
```

**Option B: Re-authorize channel**
```bash
# Deactivate channel
nanobot channel discord disable

# Re-authenticate
nanobot channel discord oauth --redirect http://localhost:8080
# Follow the browser prompt

# Reactivate
nanobot channel discord enable
```

### 2.3 "Obsidian vault locked / file permission denied"

**Symptoms:**
- Cannot write to vault
- "Permission denied" on vault paths
- Notes saved to temp instead of vault

**Diagnosis:**
```bash
# Check vault path accessibility
ls -la /path/to/obsidian/vault

# Check if nanobot process has rights
stat /path/to/obsidian/vault
```

**Fix:**

**Option A: Fix permissions**
```bash
# Give nanobot read/write access
chmod 755 /path/to/obsidian/vault
chmod 644 /path/to/obsidian/vault/*.md

# Or: Change ownership to nanobot user
sudo chown -R nanobot:nanobot /path/to/obsidian/vault
```

**Option B: Temporarily disable vault auto-save**
```bash
nanobot config set obsidian.auto_save false

# Manually trigger saves when ready
nanobot vault flush
```

**Option C: Recover from backup**
```bash
# If vault is corrupted, restore from backup
cp -r /backup/obsidian/vault /path/to/obsidian/vault

# Recrawl vault index
nanobot vault reindex
```

### 2.4 "Out of memory" / VRAM full (local Ollama)

**Symptoms:**
- Model unloads repeatedly
- Slow responses
- GPU errors in logs

**Diagnosis:**
```bash
# Check VRAM usage
nvidia-smi

# Monitor over time
watch nvidia-smi  # Ctrl+C to exit

# Check Ollama memory settings
ollama show llama2
```

**Fix:**

**Option A: Unload unused models**
```bash
# List loaded models
ollama list

# Free VRAM immediately
ollama pull --unload-all

# Load only the model you need
ollama run mistral
```

**Option B: Switch to smaller model (temporary)**
```bash
# Switch from 70B to 8B to free VRAM
/model set llama3:8b

# Verify it's loaded
nanobot model status

# User can still access 70B with explicit request
# /model force llama3:70b  (when more context needed)
```

**Option C: Configure auto-unload**
```bash
# Set timeout to auto-unload models when idle
nanobot config set ollama.keep_alive 10m  # 10 minutes of idle time

# Or disable auto-unload for intensive work
nanobot config set ollama.keep_alive 0  # Never auto-unload
```

## 3. Recovery Procedures (Multi-Step)

### 3.1 Clean Restart (Preserves Configuration)

Use this when services are stuck or deadlocked.

```bash
# Step 1: Graceful shutdown
nanobot gateway stop
sleep 5

# Step 2: Verify all processes stopped
pgrep -l nanobot || echo "All nanobot processes stopped"

# Step 3: Clean temporary files
rm -rf /tmp/nanobot_*
rm -rf ~/.nanobot/cache/*

# Step 4: Restart with diagnostic mode
nanobot gateway run --verbose

# Step 5: Test basic functionality
# In another terminal:
nanobot test-provider --provider openrouter
```

### 3.2 Configuration Corruption Recovery

Use this if `~/.nanobot/config.json` is invalid or corrupted.

```bash
# Step 1: Backup corrupted config
cp ~/.nanobot/config.json ~/.nanobot/config.json.corrupt

# Step 2: Restore from last known good (if available)
cp ~/.nanobot/config.json.backup ~/.nanobot/config.json

# Step 3: Validate restored config
nanobot config validate

# Step 4: Compare with corrupted version to check what was lost
diff -u ~/.nanobot/config.json.corrupt ~/.nanobot/config.json

# Step 5: Re-apply any missing custom settings
# (Edit manually or use CLI)
nanobot config set llm.provider openrouter
nanobot config set channels.discord.enabled true

# Step 6: Restart and verify
nanobot gateway restart
nanobot status --all
```

### 3.3 Credential Reset (After Compromise or Expiration)

Use this if you suspect tokens/keys are compromised or tokens expired.

```bash
# Step 1: Revoke old credentials from provider
# For OpenRouter: Log in → API Settings → Delete old keys
# For Anthropic: Console → Dashboard → Revoke key
# For Discord: Server Settings → Integrations → OAuth2 → Reset

# Step 2: Generate new credentials at provider

# Step 3: Update .env file
nano ~/.nanobot/.env
# Update: OPENROUTER_API_KEY=<new_key>
# Or: ANTHROPIC_API_KEY=<new_key>

# Step 4: Reload environment
source ~/.nanobot/.env

# Step 5: Test new credentials
nanobot test-provider --provider openrouter

# Step 6: Restart gateway to apply
nanobot gateway restart

# Step 7: Verify in channel
# Send test message in Discord/Slack to confirm working
```

### 3.4 Vault Corruption Recovery

Use this if notes are corrupted, links broken, or vault index outdated.

```bash
# Step 1: Backup current vault
tar -czf ~/obsidian-backup-$(date +%Y%m%d_%H%M%S).tar.gz /path/to/vault

# Step 2: Check vault integrity
nanobot vault check --full

# Step 3: Reindex vault
nanobot vault reindex

# Step 4: Verify recovery
nanobot vault search --test "example query"

# Step 5: If still broken, restore from backup
# Restore to timestamped directory first to validate
mkdir /tmp/vault-restore
tar -xzf ~/obsidian-backup-<date>.tar.gz -C /tmp/vault-restore

# Once verified, move to production
rm -rf /path/to/vault
mv /tmp/vault-restore/vault /path/to/vault

# Step 6: Reindex restored vault
nanobot vault reindex
```

## 4. Data Integrity Checks

### 4.1 Verify Recent Exports (Backup Validation)

```bash
# Check when was the last successful backup
ls -lah ~/.nanobot/backups/

# Validate backup integrity
tar -tzf ~/.nanobot/backups/backup-latest.tar.gz | head -20

# Test restore to temp location
mkdir /tmp/backup-test
tar -xzf ~/.nanobot/backups/backup-latest.tar.gz -C /tmp/backup-test

# Verify key files exist
test -f /tmp/backup-test/config.json && echo "Config backed up ✓"
test -d /tmp/backup-test/vault && echo "Vault backed up ✓"
test -d /tmp/backup-test/logs && echo "Logs backed up ✓"
```

### 4.2 Audit Log Verification

```bash
# Check if audit logs exist and recent
ls -lah ~/.nanobot/logs/audit.log

# Verify recent entries
tail -30 ~/.nanobot/logs/audit.log | grep -i "error\|warning"

# Count errors in last 24h
grep "$(date -d yesterday +%Y-%m-%d)" ~/.nanobot/logs/audit.log | grep -i error | wc -l

# Export logs for external review
tar -czf ~/nanobot-logs-$(date +%Y%m%d).tar.gz ~/.nanobot/logs/
```

## 5. Troubleshooting Quick Reference

| Symptom | Likely Cause | First Check | Quick Fix |
|---------|-------------|------------|----------|
| No responses | Provider offline or bad token | `nanobot test-provider` | Update token or switch provider |
| Slow responses | Out of memory or network latency | `nvidia-smi` / `ping provider` | Unload models or check network |
| Bot unresponsive | Gateway crashed | `pgrep nanobot` | `nanobot gateway restart` |
| Obsidian errors | Vault locked or permission denied | `ls -la /vault/path` | Fix permissions or disable auto-save |
| High costs | Expensive model routing | `nanobot cost status` | Switch to cheaper model tier |
| Channel disconnected | Auth token expired | `nanobot channel list` | Regenerate token |
| Configuration lost | File corrupted or deleted | Check `.backup` suffixed files | Restore from backup |

## 6. Emergency Contact List (Template)

Keep this updated with your actual contacts:

```markdown
# Emergency Contacts

**Primary Provider Support:**
- OpenRouter: support@openrouter.ai
- Anthropic: help@anthropic.com
- OpenAI: support@openai.com

**Cloud Provider (if using VPS):**
- Provider: [Your VPS Provider]
- Support: [Support URL]
- Account: [Your Account ID]

**Internal Support:**
- Primary Operator: [Your Name/Email]
- Backup Operator: [Backup Person/Email]
- On-Call: [Rotation Schedule]

**Monitoring/Alerts:**
- Critical Alert Channel: [Discord/Slack]
- Email: [Your Alert Email]
```

## 7. Preventive Maintenance

### 7.1 Daily Checks

```bash
#!/bin/bash
# Save as ~/.nanobot/daily-check.sh

# Check service health
nanobot status --all

# Monitor costs
nanobot cost today

# Verify connections
nanobot test-provider --provider openrouter
nanobot test-channel --channel discord

# Show recent errors
tail -20 /var/log/nanobot/gateway.log | grep -i error
```

### 7.2 Weekly Backup

```bash
#!/bin/bash
# Run weekly: crontab -e → add: 0 0 * * 0 ~/.nanobot/weekly-backup.sh

BACKUP_DIR=~/.nanobot/backups
DATE=$(date +%Y%m%d)

# Backup configuration
tar -czf $BACKUP_DIR/config-$DATE.tar.gz ~/.nanobot/config.json

# Backup vault
tar -czf $BACKUP_DIR/vault-$DATE.tar.gz /path/to/obsidian/vault

# Keep only last 4 weeks
find $BACKUP_DIR -name "*.tar.gz" -mtime +28 -delete

echo "Backups completed: $(ls -1 $BACKUP_DIR | wc -l) files"
```

### 7.3 Monthly Security Audit

```bash
# Run monthly security checks
nanobot security audit --full

# Validate allowlists
nanobot config show allowlist

# Review recent API usage
nanobot logs export --format json --days 30 > api-usage-$(date +%Y%m).json

# Update channel tokens if not rotated in 90 days
grep -r "token_created" ~/.nanobot/ | grep -v "$(date -d '3 months ago' +%Y-%m)"
```

## 8. See Also

- [Security Validation Runbook](Security-Validation-Runbook.md) — Monthly security procedures
- [Governance Policies](Governance-Policies-and-Config-Examples.md) — Access control and escalation
- [AOS Startup Procedure](AOS-Startup-Procedure.md) — Initial setup and boot sequence
- [Cost Calculator](Cost-Calculator-and-Optimization.md) — Monitor spend to detect anomalies
