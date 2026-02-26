# Nanobot Agentic Operating System Security Validation Runbook

## 1. Purpose
This runbook defines the recurring security validation steps for the Nanobot Agentic Operating System.

## 2. Frequency and Ownership
- **Frequency:** Monthly (minimum), and after major config changes
- **Owner:** Security/Platform Owner
- **Operator:** Assigned Operator
- **Reviewer:** Assigned Reviewer

## 3. Preconditions
- Access to gateway host
- Access to Discord server settings
- Access to Nanobot config and logs
- Access to Obsidian operational records

## How to Copy & Paste Commands in Linux

When you encounter bash commands in validation steps below:

**Quick reference:**
- **Fastest:** Highlight → middle-click in terminal
- **Reliable:** Highlight → `Ctrl+Shift+C` → `Ctrl+Shift+V` to paste
- **Fallback:** Highlight → `Ctrl+C` → right-click Paste

Middle-click paste is native to Linux/Mac terminals and requires no additional steps.

## 4. Validation Sequence
Execute in this order to ensure consistent evidence collection.

### Step 1: Gateway Exposure Check
- Verify gateway bind is loopback-only. (Loopback = only accessible from this computer)
- Confirm no unintended public bind (**0.0.0.0**—which means "accessible to the whole internet") is active.
- Validate remote access path is SSH (Secure Shell—encrypted remote access) tunnel or Tailscale network-based.
- Validate Ollama on local Windows host is reachable from VPS (Virtual Private Server) only via trusted Tailscale path and is not publicly exposed.

**Pass Criteria**
- Gateway is loopback-bound and remote access is controlled.

**Fail Criteria**
- Any direct public exposure without approved exception.

### Step 2: DM Policy and Allowlist Check

**DM** = Direct Message (private messages sent to the bot, not in public channels)
- Verify DM policy is pairing mode by default.
- Validate unknown sender behavior (no command execution before approval).
- Verify allowlist entries are current and minimal. (Allowlist = list of approved people/channels)

**Pass Criteria**
- DM pairing flow works and allowlist scope is least-privilege.

### Step 3: Trust Boundary Check
- Confirm no mutually untrusted operator groups share one gateway trust boundary.
- Validate separation model (separate gateway/host/OS user) where required.

**Pass Criteria**
- Trust boundaries are documented and enforced.

### Step 4: Sandbox Policy Check
- Verify non-main/shared channels run under sandbox profile where required.
- Confirm high-risk tools are not broadly exposed in shared contexts.

**Pass Criteria**
- Sandbox rules match policy for shared/non-main sessions.

### Step 5: Security Audit Execution
- Run `nanobot security audit --deep` (automated security check for vulnerabilities)
- Verify deployed Nanobot version is v0.1.4 or later. (Newer versions have security fixes)
- Review latest Nanobot security advisories and confirm no unpatched High/Critical findings apply to deployed version.
- Capture findings and classify severity. (Severity = how serious the issue is)
- Confirm critical findings have immediate mitigation plan.

**Pass Criteria**
- No unresolved critical findings.
- Deployed version is at or above the current patched minimum for published advisories.

### Step 6: Secrets and Token Hygiene
- Confirm tokens/secrets are in **.env** (configuration file with secrets—NOT shared in code) or approved secret store only.
- Confirm no secrets (API keys, passwords) are present in notes, docs, or source-controlled config.
- Validate token rotation readiness and last rotation date. (Rotation = changing tokens regularly, like changing passwords)

**Pass Criteria**
- Secret handling policy is fully compliant.

### Step 7: Logging and Evidence Integrity
- Confirm required telemetry fields exist (**request_id**, **project_id**, **workflow_domain**, status, timestamps, model/cost fields where applicable).
- Confirm incident and change records are persisted to Obsidian.

**Pass Criteria**
- Security-relevant logs are complete and traceable end-to-end.

## 5. Evidence to Capture
- Command output snippets (including security audit)
- Nanobot version output (**Nanobot --version**) and advisory review date
- Config screenshots or sanitized config excerpts
- Allowlist review record
- Sandbox policy verification record
- Remediation tickets with owners and due dates

## 6. Remediation SLA
- **Critical:** immediate containment + fix plan same day
- **High:** remediation within 7 days
- **Medium:** remediation within 30 days
- **Low:** backlog with documented rationale

## 7. Runbook Output Record Template
```markdown
# Security Validation Report - <YYYY-MM-DD>
- Run ID:
- Owner:
- Operator:
- Reviewer:
- Environment:

## Results by Step
1. Gateway Exposure Check: Pass/Fail
2. DM Policy and Allowlist Check: Pass/Fail
3. Trust Boundary Check: Pass/Fail
4. Sandbox Policy Check: Pass/Fail
5. Security Audit Execution: Pass/Fail
6. Secrets and Token Hygiene: Pass/Fail
7. Logging and Evidence Integrity: Pass/Fail

## Findings
- Critical:
- High:
- Medium:
- Low:

## Remediation Plan
- Item:
  - Owner:
  - Due Date:
  - Status:

## Final Decision
- Overall Status: Pass / Conditional Pass / Fail
- Next Review Date:
```

## 8. Go/No-Go Rule
- **Go:** all critical controls pass and no unresolved critical findings.
- **No-Go:** any unresolved critical finding or trust-boundary violation.

## 9. Related Documents
- [Nanobot Build Procedure](Nanobot-Build-Procedure.md)
- [Master Index](Master-Index.md)
- [Tools & Skills Reference](Tools-and-Skills-Reference.md)
- [Governance Policies & Config Examples](Governance-Policies-and-Config-Examples.md)

## 10. Common Mistakes & Solutions

### ❌ Mistake 1: Security Audit Shows Findings But No Remediation Plan
**Problem:** Audit fails with "3 High severity findings"; validation stops  
**Why:** Findings documented, but no owner/timeline assigned  
**Fix:**
1. For each High/Critical finding, create remediation ticket immediately:
   ```markdown
   # Remediation: [Issue name]
   - Owner: @specific_person (not "team")
   - Due Date: [7 days from today for High]
   - Priority: High
   - Ticket: <link>
   ```
2. Don't mark validation as "Conditional Pass" without assigned owner + date
3. Follow up: Check status 3 days before due date

### ❌ Mistake 2: Token Rotation Never Happens - Old Tokens Still Active
**Problem:** Last rotation was 6 months ago; old Discord token still working  
**Why:** No automated reminder; no rotation policy enforced  
**Fix:**
1. **Set up rotatio auto-reminders:**
   ```bash
   nanobot schedule "Rotate Discord token" cron "0 9 1 * *"  # 1st of month at 9am
   nanobot schedule "Rotate API keys" cron "0 9 15 * *"     # 15th of month
   ```
2. **Document last rotation date:**
   ```markdown
   - Discord token: Last rotated [DATE] - Due [DATE+90days]
   - OpenRouter API: Last rotated [DATE] - Due [DATE+90days]
   - GitHub token: Last rotated [DATE] - Due [DATE+90days]
   ```
3. **Enforce in config:**
   ```json
   {
     "secrets": {
       "rotation_policy": "every_90_days",
       "expiry_alert_days": 14
     }
   }
   ```

### ❌ Mistake 3: Sandbox Rules Documented But Not Enforced
**Problem:** Security says "shared channels run in sandbox"; in reality, they don't  
**Why:** Sandbox policy written but config doesn't enforce it  
**Fix:**
1. **Check actual config enforces sandbox:**
   ```json
   {
     "channels": {
       "#general": {
         "sandbox": true,
         "allowed_tools": ["web_search", "message_send"],
         "disallowed_tools": ["shell_exec", "file_write"]
       },
       "#devops": {
         "sandbox": false,
         "allowed_tools": "all"
       }
     }
   }
   ```
2. **Verify it's actually enforced:**
   ```bash
   nanobot config --validate
   # If validation passes, config is syntactically correct
   # But config enforcement = separate system check
   ```
3. **Test sandbox actually blocks:**
   ```
   /in #general shell_exec ls
   # Should get: "Blocked: shell_exec not allowed in #general (sandbox)"
   ```

### ❌ Mistake 4: Secrets in Git History - Can't Be Removed
**Problem:** Someone committed API key to GitHub 3 months ago; it's still in history  
**Why:** Used plain `git rm` instead of removing from history  
**Fix:**
1. **If accidental commit is recent**, use filter-branch:
   ```bash
   git filter-branch --force --index-filter \
     'git rm --cached --ignore-unmatch config.json' \
     -- --all
   ```
2. **Force push to remote:**
   ```bash
   git push origin --force --all
   ```
3. **Immediately revoke the compromised token in the service (GitHub, Discord, etc.)**
4. **Add to .gitignore to prevent future commits:**
   ```bash
   echo "config.json" >> .gitignore
   echo ".env" >> .gitignore
   echo "secrets/* >> .gitignore
   git add .gitignore && git commit -m "Add secrets to gitignore"
   ```

### ❌ Mistake 5: Trust Boundary Violation - Multiple Untrusted Groups Share Gateway
**Problem:** Engineering team and Marketing team both use same gateway; engineer accesses marketing's Slack token  
**Why:** Didn't enforce separation; all users on same process  
**Fix:**
```yaml
❌ No trust boundary enforcement:
gateway:
  users: ["eng-team", "marketing-team"]  # Same process!

✅ Separate trust boundaries:
gateway_engineering:
  users: ["eng-team"]
  bind: "127.0.0.1:8000"

gateway_marketing:
  users: ["marketing-team"]
  bind: "127.0.0.1:8001"

# Even better: Separate hosts/VMs
```
2. **Implement:** Create separate gateway instances for each trust group
3. **Verify separation:**
   ```bash
   ps aux | grep nanobot
   # Should see multiple nanobot processes with different ports
   ```

### ❌ Mistake 6: Allowlist Has Old Users Who Left Company
**Problem:** Employee left 6 months ago; bot still accepts DMs from their old Discord account  
**Why:** Never removed from allowlist config  
**Fix:**
1. **Quarterly audit:**
   ```json
   {
     "dms": {
       "allowlist": [
         "alice (joined 2026-01-15)",
         "bob (joined 2025-09-01)",
         "dave (REVOKED 2026-02-01 - left company)"  // Mark as revoked
       ]
     }
   }
   ```
2. **Enforce allowlist strictly:**
   ```bash
   nanobot config dms.allowlist --validate
   # Check for stale/revoked entries
   ```
3. **When employee leaves:**
   - Remove from allowlist immediately
   - Revoke any API tokens they created
   - Check `~/.nanobot/logs/` for any emergency access patterns

---

## 11. Revision History
| Date | Version | Change |
|---|---|---|
| 2026-02-24 | 1.1.1 | Added explicit Nanobot minimum patched-version check and latest-advisory verification step |
| 2026-02-24 | 1.1.0 | Added explicit Windows-host Ollama exposure validation to Step 1 for native-Windows inference architecture alignment |
| 2026-02-23 | 1.0.0 | Initial monthly security validation runbook for Nanobot Agentic Operating System |

