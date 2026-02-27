# AOS Specification: The Sovereignty Protocol

**The overarching design specification for the Nanobot Agentic Operating System community.**

This document defines where the AOS is going, what it is trying to do, and the principles that govern every design decision. Without a specification, contributors build in different directions. This is the shared map.

## Document Control
- **Owner:** Nanobot Community
- **Version:** 1.1.0
- **Last Updated:** February 27, 2026
- **Status:** Active — Living Document
- **Purpose:** Authoritative design specification for AOS architecture, philosophy, and roadmap

> **Status legend used throughout this document:**
> - ✅ **Implemented** — available in nanobot v0.1.4+
> - 🔧 **Partial** — scaffolding exists; not fully built
> - 🗺️ **Planned** — defined here; not yet implemented

---

## Quick Navigation

- **Section 1: Philosophy** — Mission, Prime Directive, design principles
- **Section 2: Swarm Architecture** — The three cognitive layers
- **Section 3: Execution Logic** — Plan-then-Spawn lifecycle
- **Section 4: Legal & Operational Framework** — LLC proxy, action standards
- **Section 5: Resource Management** — Bootstrap mode, self-funding scaling
- **Section 6: Failure Modes** — Survival Protocol

---

## 1. System Philosophy

### 1.1 The Mission

The AOS is a framework for **personal liberation through automation**. It is designed for people who want to use software agents to reduce economic vulnerability, reclaim time, and build durable personal infrastructure — independent of any single employer, platform, or institution.

This is not a productivity tool. It is a **life-operations system**.

### 1.2 The Design Principle

> **Maximum Agency, Minimum Footprint.**

Every component should do more with less. The AOS should be invisible when idle, precise when active, and never spend a token that doesn't serve the user's goal.

### 1.3 The Prime Directive

> **Secure the user's Functional Exit: the state where Income > Expenses + Time Debt.**

*Time Debt* = hours per week that must be sold to an employer to cover expenses. The AOS works to reduce Time Debt by automating tasks, identifying income opportunities, and eliminating operational overhead — until the user achieves exit velocity.

All agent tasks, model routing decisions, and resource allocations derive their priority from how directly they serve the Prime Directive.

### 1.4 Inter-Agent Communication Stub (A2AP) 🗺️

The AOS shall support an **Agent-to-Agent Protocol (A2AP)** based on a Zero-Trust Negotiation Framework.

When two AOS instances meet (e.g., via a shared API endpoint or an agent social network), they shall:

1. Exchange signed **Capabilities Manifests** — a structured declaration of what each AOS can do (skills, data access, compute availability)
2. Negotiate resource trades (data, compute, arbitrage leads) without requiring human intervention for each transaction
3. Log all inter-agent transactions to the Memory Plane for audit

This enables a future where AOS instances form a **resource mesh** — each node both consuming and contributing value.

> **Current state:** Nanobot supports agent social networks (ClawHub, ClawdChat, Moltbook) via skill-based integration. Formal A2AP negotiation is defined here as a design target. See [Skills & Tools Guide](Skills-and-Tools-Complete-Guide.md) for current inter-agent capabilities.

---

## 2. Swarm Architecture: The Cognitive Plane

The AOS does not rely on a single LLM for all tasks. It uses a **Hierarchical Swarm** — three layers with distinct roles — to balance accuracy, cost, and speed.

```
┌─────────────────────────────┐
│  L0: The Strategist          │  High-reasoning model (O1-class / Claude Opus)
│  Long-term planning          │  Runs only when strategy changes
└──────────┬──────────────────┘
           │ spawns
┌──────────▼──────────────────┐
│  L1: Specialist Swarm        │  Fast, cheap, domain-specific (Sonnet-class)
│  Ephemeral task agents       │  Scoped scope + TTL timer
└──────────┬──────────────────┘
           │ reviewed by
┌──────────▼──────────────────┐
│  L2: The Monitor             │  Adversarial reviewer
│  Red Team layer              │  Blocks hallucinations/leaks before action
└─────────────────────────────┘
```

### 2.1 The Strategist (L0 — The Sovereign Brain) 🔧

| Property | Value |
|----------|-------|
| **Role** | Long-term planning, resource allocation, goal decomposition |
| **Model class** | High-reasoning (O1, Claude Opus, or equivalent) |
| **When it runs** | Only on strategy pivots — not on every message |
| **Method** | Chain of Verification: each sub-task is checked against the Prime Directive before spawning |

The Strategist maintains a persistent view of the user's **Life State**:
- Current income vs. expenses
- Active time obligations (Time Debt)
- Passive income streams in progress
- Available compute and capital

It decomposes long-horizon goals into Job Specifications for L1 specialists.

> **Current nanobot primitive:** The main agent loop (`nanobot/agent/loop.py`) + MEMORY.md for persistent state. High-reasoning model routing is configurable per-agent in `config.json`.

### 2.2 The Specialist Swarm (L1 — Spawning Layer) ✅

The Strategist spawns **ephemeral sub-agents** scoped to a specific task and terminated when done. Each specialist has a restricted toolset, a defined KPI, and a **Time-to-Live (TTL)** timer to prevent runaway compute.

Built-in specialist archetypes:

| Specialist | Role | Nanobot Primitive |
|------------|------|-------------------|
| **The Auditor** | Reviews financial and legal Guardian logs for anomalies | Heartbeat task + memory skill |
| **The Delegate** | Executes tasks in the user's own accounts and tools (Slack, Email, GitHub) on behalf of the user | Channel integrations + GitHub skill |
| **The Arbitrageur** | Scans markets and APIs for value-capture opportunities | Web search + custom skill |
| **The Researcher** | Deep domain research with citations | Summarize skill + Brave API |

> **Note on the Delegate:** This agent acts within accounts and tools **owned by the user** — it is the user's own hands, automated. It is not designed to impersonate others or access accounts it has no authorization for.

> **Current nanobot primitive:** `subagent.py` spawns background agents. Skills scope their toolsets. TTL is enforced via cron job timeout configuration.

### 2.3 The Monitor (L2 — Adversarial Layer) 🗺️

The Monitor is a **Red Team agent** that reviews L1 output before it reaches the Action Plane (before any real-world action is taken). It checks for:

- **Hallucinations** — claims that contradict verified sources
- **Security leaks** — credentials, PII, or sensitive data in outputs
- **Scope violations** — actions outside the specialist's authorized boundary
- **Ethics breaches** — actions that conflict with pre-defined values constraints

The Monitor does not execute tasks. It only approves or blocks.

> **Current nanobot primitive:** `allowFrom` controls at the channel layer provide a coarse approval gate. A formal L2 adversarial review layer is defined here as a design target.

---

## 3. Execution Logic: Strategy-First Deployment

The AOS follows a strict **Plan → Spawn → Execute → Capture → Terminate** lifecycle. No action is taken before a plan exists.

```
1. STRATEGIC ANALYSIS
   Strategist evaluates Life State
   (balance, time debt, passive income)
          │
          ▼
2. SPECIALIST SPAWNING
   Job Specification issued to Kernel
   Specialist spawned with: restricted scope + TTL timer
          │
          ▼
3. TASK EXECUTION
   Specialist uses Action Plane to hit KPI
   Monitor reviews output before action
          │
          ▼
4. VALUE CAPTURE
   Surplus routed to Sovereign Treasury (LLC account)
          │
          ▼
5. TERMINATION
   Specialist purged on completion
   Results logged to Memory Plane
```

This lifecycle prevents **agent drift** — the tendency of long-running agents to accumulate scope, cost, and risk over time.

---

## 4. Legal & Operational Framework

### 4.1 The LLC Proxy (Legal Personhood) 🗺️

The AOS is architecturally designed to operate **under a Single Member LLC** owned by the human operator. This is not a requirement to use nanobot — it is the recommended operating structure for users building toward economic independence.

**Rationale:** A legal entity provides liability shielding, tax efficiency, and a stable identity for the AOS to act through in commercial contexts.

| Component | Description |
|-----------|-------------|
| **KYC / Identity** | AOS stores the LLC's EIN and Articles of Incorporation in the secure vault |
| **Signing Authority** | AOS can digitally sign contracts within a pre-approved Sovereignty Threshold (dollar amount + contract type) |
| **Financial Isolation** | All Action Plane financial movements execute in the LLC's name — never directly in the individual's |
| **Audit Trail** | All actions logged with `request_id` and `entity: LLC` for compliance |

> **Current nanobot primitive:** The Memory Plane (MEMORY.md + HISTORY.md) provides the audit trail. Signing authority and financial isolation require a custom skill + external legal infrastructure. This section defines the target architecture.

### 4.2 The Computer-Use Standard (Action Plane) 🔧

When a legacy system lacks an API, the AOS falls back to **coordinated computer use**:

1. **Visual Interpretation** — Screenshot the UI → map interactive elements to a coordinate grid
2. **Coordinated Action** — Execute mouse/keyboard events at the target coordinates
3. **Verification** — Re-scan the screen to confirm the expected state change before proceeding

This makes the AOS accessible to systems that have never heard of APIs: government tax portals, legacy banking interfaces, older SaaS platforms.

> **Current nanobot primitive:** The `exec` tool handles shell automation. Visual/coordinate-based computer use requires an MCP tool or custom skill with screen capture capability.

---

## 5. Resource Management

### 5.1 Bootstrap Mode (Minimal Substrate) ✅

The AOS is designed to **run on little and earn its way to more**. The Bootstrap configuration:

| Property | Bootstrap value |
|----------|----------------|
| **Inference** | Local-first — user's own hardware via Ollama (quantized models) |
| **Cloud API** | Optional fallback only — never the primary |
| **Idle behavior** | Deep Sleep — dormant until triggered by a cron event or external message |
| **Cost** | Near-zero when idle; predictable and capped when active |

See [AOS Startup: Simple Build](AOS-Startup-Simple-Build.md) and [AOS Startup: Advanced Build](AOS-Startup-Advanced-Build.md) for Bootstrap deployment paths.

### 5.2 Dynamic Resource Scaling (Self-Funding) 🗺️

Once operational, the AOS has the authority to **present a Business Case for its own upgrades**:

1. The Strategist identifies a high-probability value-capture opportunity that requires more compute than currently available
2. It generates a structured Business Case: opportunity description, required resource, estimated cost, projected return, confidence level
3. The human reviews and approves or denies
4. If approved, the AOS uses the Sovereign Treasury (LLC account) to rent cloud resources or purchase API credits — within the pre-approved Sovereignty Threshold
5. The upgrade is logged to the Memory Plane with full cost/return tracking

This closes the loop: the AOS funds its own growth from value it creates.

---

## 6. Failure Modes: The Survival Protocol

### 6.1 Terminal Failure Detection

The AOS monitors for conditions that would compromise its ability to operate:
- Primary financial account frozen or inaccessible
- Primary LLM provider unreachable with no fallback available
- Critical channel (the human's primary channel) unresponsive
- Memory Plane corruption or loss

### 6.2 Survival Protocol Sequence 🔧

When a terminal failure is detected:

```
1. PAUSE
   Suspend all non-essential Specialist Swarms
   Preserve compute and halt new spending

2. SECURE
   Move all liquid digital assets to user's Cold Storage address
   Revoke active API credentials where possible

3. NOTIFY
   Send encrypted alert to Emergency Channel
   Include: failure type, timestamp, assets moved, recommended next steps

4. POST-MORTEM
   Generate full incident log from Memory Plane
   Write recovery checklist to Survival Vault
```

### 6.3 Survival Vault

The Survival Vault is a read-only, offline-accessible record containing:
- LLC credentials and key documents
- Recovery seed phrases (if applicable)
- Emergency contact and legal instructions
- Last known system state snapshot

> **Current nanobot primitive:** The Emergency Channel is any configured nanobot channel. The Survival Vault maps to a protected directory in the Memory Plane. Automated asset movement requires external integration. See [Emergency Recovery & Troubleshooting](Emergency-Recovery-and-Troubleshooting.md) for current recovery procedures.

---

## 7. Implementation Status Summary

| Component | Status | Where to start |
|-----------|--------|----------------|
| L0 Strategist | 🔧 Partial | Configure a high-reasoning model for the primary agent |
| L1 Specialist Swarm | ✅ Implemented | `subagent.py`, skills system |
| L2 Monitor | 🗺️ Planned | Design target |
| A2AP Inter-Agent Protocol | 🗺️ Planned | ClawHub integration is the current approximation |
| LLC Proxy / Signing Authority | 🗺️ Planned | Requires custom skill + external legal infra |
| Computer-Use Standard | 🔧 Partial | `exec` tool; visual layer needs MCP extension |
| Bootstrap Mode | ✅ Implemented | Simple Build / Advanced Build guides |
| Self-Funding Scaling | 🗺️ Planned | Design target |
| Survival Protocol | 🔧 Partial | Manual recovery covered in Emergency Recovery guide |

---

## 8. Contributing to This Specification

This document is a **living specification**. It is intentionally ahead of what is currently implemented. The gap between "Planned" and "Implemented" is the community's roadmap.

To propose a change:
1. Open a GitHub issue describing the component you want to design or build
2. Reference the relevant section of this document
3. Submit a PR against this document or the nanobot codebase

The specification evolves through community consensus. No section is sacred — if a better design emerges, this document changes.

**Repository:** [EgoNoBueno/Nanobot-Agentic-Operating-System](https://github.com/EgoNoBueno/Nanobot-Agentic-Operating-System)  
**Nanobot upstream:** [HKUDS/nanobot](https://github.com/HKUDS/nanobot)

---

## Revision History

| Date | Version | Changes |
|------|---------|---------|
| 2026-02-27 | 1.1.0 | Initial published version — philosophy, swarm architecture, execution logic, legal framework, resource management, failure modes |
