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
- Verify DM (Direct Message—private messages to the bot) policy is pairing mode by default.
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
- Verify deployed Nanobot version is **2026.2.23** or later. (Newer versions have security fixes)
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

## 10. Revision History
| Date | Version | Change |
|---|---|---|
| 2026-02-24 | 1.1.1 | Added explicit Nanobot minimum patched-version check and latest-advisory verification step |
| 2026-02-24 | 1.1.0 | Added explicit Windows-host Ollama exposure validation to Step 1 for native-Windows inference architecture alignment |
| 2026-02-23 | 1.0.0 | Initial monthly security validation runbook for Nanobot Agentic Operating System |

