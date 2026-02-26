# Glossary of Terms

Quick reference for technical terms, acronyms, and concepts used throughout the Nanobot documentation.

## Document Control
- **Owner:**
- **Version:** 1.2.0
- **Last Updated:** 2026-02-26
- **Status:** Active

---

## A

**Agent Orchestration** - see *Orchestration*.

**Allowlist** - A list of specific users, channels, or tools that are explicitly approved to use sensitive features. Only items on the allowlist are allowed; everything else is blocked.

**API** (Application Programming Interface) - A way for different programs to talk to each other. Think of it as a standardized menu that one program offers, so other programs know exactly what requests to make.

**Approval Gate** - A policy rule that pauses execution and requires a human to explicitly authorize an action before it proceeds. Used for high-cost operations, sensitive tool calls, or escalated decisions. Defined in your governance policy and enforced by the Policy Plane.

**Artifacts** - Files, notes, or records that the system creates and saves for later reference. In nanobot, artifacts are stored in Obsidian for audit trails and recovery.

**Audit Trail** - A chronological record of all actions taken by nanobot: who requested what, which model was used, what it cost, and what was produced. Stored in Obsidian. Required for compliance, recovery, and debugging. Distinct from general logging in that audit trails are structured and append-only.

**Auth** / **Authentication** - Proving who you are. Usually done with a password or API key.

---

## B

**Bot** - Short for robot. In this context, nanobot is a software bot that listens to messages and responds automatically.

**Bash** - A common command-line language used on Linux, Mac, and Windows (via WSL or Git Bash). We use it for installation and troubleshooting commands.

---

## C

**Channel** - A conversation space. Examples: Discord channel, Slack channel, Telegram group. Nanobot can listen to multiple channels at once.

**Channel-Agnostic** - Works the same way no matter which chat platform you use (Discord, Slack, Telegram, etc.). You don't need different rules for each platform.

**Context Window** - The maximum amount of text an LLM can "see" at once, measured in tokens. Once a conversation exceeds the context window, older messages are cut off or must be summarized. This is why Memory Consolidation exists: it compresses old history to keep conversations within the context window without losing important information.

**Control Plane** - see *Plane*.

**CLI** (Command-Line Interface) - A way to interact with a computer by typing commands instead of clicking buttons. All our installation and troubleshooting uses the CLI.

**Cron** - A tool that runs tasks on a schedule (e.g., "every day at 9 AM"). Named after the Greek word *chronos* (χρόνος), meaning time.

**Config** / **Configuration** - The settings file that tells nanobot how to behave (which channels to listen to, which LLM to use, etc.).

**Cost Ceiling** - A hard limit on how much money the system will spend. Once the limit is reached, no more requests are processed until the budget resets.

---

## D

**Dashboard** - A display that shows the current status of your system (e.g., "How many tasks completed today?" or "What's the CPU usage?").

**Degraded Mode** - A fallback state where nanobot continues operating with reduced capability when a primary provider or service is unavailable. Example: if Claude (Tier A) is unreachable, nanobot automatically routes to a Tier B model instead of failing completely. Configured as part of the fallback chain in `config.json`.

**Delegation** - Asking a subagent to handle a specific part of a task on behalf of the orchestrating agent.

**Deprecated** - Old technology that still works but is being phased out. Using deprecated features is not recommended.

---

## E

**Escalation** - Sending a decision "up the chain" because it needs human approval. Example: a high-cost operation gets escalated to a finance team member.

**Executor** - Something that runs commands. The nanobot executor handles web searches, file operations, shell commands, etc.

---

## F

**Fallback** - A backup plan. If your first choice fails, the fallback is automatically used instead.

**Firewall** - A security tool that controls which network traffic is allowed in and out of a computer.

---

## G

**Gateway** - The main entry point for nanobot. It listens for incoming messages and routes them to the right handler.

**Governance** - The rules and policies that control how nanobot operates (who can use what, approval workflows, cost limits, etc.).

---

## H

**Hardening** - Securing a system by removing vulnerabilities, applying patches, configuring firewalls, and restricting access. A "hardened" VPS has security best practices applied (firewall rules, SSH key-only auth, no unnecessary services running, etc.).

**Heartbeat** - A periodic check to make sure the system is still alive and healthy. Like taking a pulse.

**Hot-Reload** - Updating code or configuration without needing to restart the system. Changes take effect immediately.

**HTTPS** - A secure version of HTTP (the protocol used to transfer web pages). The "S" stands for Secure.

---

## I

**Inference** - When an AI model processes text and generates a response. "Running inference" means asking the AI a question.

**Injection** - Installing or activating a new skill or tool at runtime, without restarting the system. You can inject a skill by pasting a repository link or using a `/skill install` command in chat. Distinct from code hot-reload (see *Hot-Reload*).

**Integration** - Connecting two systems so they can work together. Discord integration means nanobot can listen to Discord.

---

## J

**JSON** (JavaScript Object Notation) - A standardized format for storing and sharing data. Looks like:
```json
{
  "name": "Alice",
  "age": 30
}
```

---

## K

**KPI** (Key Performance Indicator) - A measurable way to track if something is working well. Examples: "cost per successful task" or "errors per day."

---

## L

**LLM** (Large Language Model) - A powerful AI trained on lots of text. Examples: Claude, GPT-4, Qwen. Nanobot uses LLMs to understand questions and generate responses.

**Log** / **Logging** - Recording what happens in a system so you can review it later. Nanobot logs all requests for security and debugging.

---

## M

**MCP** (Model Context Protocol) - A standard way to connect AI models to external tools (databases, file systems, APIs). Think of it as a universal connector.

**Memory Consolidation** - Summarizing old conversations automatically, so the system doesn't get bogged down with endless history.

**Memory Plane** - see *Plane*.

**Middleware** - Software that sits in the middle of two other systems and helps them communicate.

**Model Routing** - Deciding which AI model to use for each task. "Route this to Claude because it's complex" or "Route this to a cheap model because it's simple."

---

## N

**Node** - A single computer in a network. A "VPS node" is a virtual computer in the cloud.

**Notification** - A message sent to alert someone about something important.

---

## O

**OAuth** - A secure way to log in with your existing account (e.g., "Sign in with Discord").

**Obsidian** - A note-taking app used by nanobot to store memory, audit trails, and artifacts. It runs on your computer.

**Orchestration** - Coordinating multiple agents or tasks to work together toward a shared goal. Example: one subagent researches competitors while a second verifies findings, and a third writes the report. The orchestrator manages the handoffs and assembles the result.

**Operator** - A human user who controls nanobot. You're the operator.

---

## P

**Payload** - The data being sent or received. E.g., the message content is the payload.

**Permission** - The right to do something. "Shell execute permission" means you're allowed to run shell commands.

**Plane** - One of the three architectural layers of the Nanobot AOS. Separating the system into planes keeps concerns independent: you can change your chat platform without touching your LLM policy, and change your LLM policy without changing how data is stored.
- **Control Plane** — The message intake layer. Receives requests from Discord, Slack, Telegram, etc. and routes them into the system.
- **Policy Plane** — The governance layer. Decides which model to use, enforces cost limits, applies approval gates, and manages RBAC.
- **Memory Plane** — The storage layer. Obsidian stores artifacts, audit trails, session history, and knowledge for replay and recovery.

**Policy** - A rule about how something should work. "Require approval for costs over $50" is a policy.

**Policy Plane** - see *Plane*.

**PR** (Pull Request) - A way to propose changes to code on GitHub. "Create a PR" means suggest a code change.

---

## Q

**Query** - A question or search request.

---

## R

**RBAC** (Role-Based Access Control) - A security system where people have roles (operator, reviewer, owner), and each role has different permissions.

**REST API** - A standard way to request information from a program over the internet. Obsidian has a REST API so nanobot can read and write notes.

**Revert** - Go back to a previous state. "Revert this file" means restore an old version.

**Recovery** - Restoring a system after an error or crash. We have recovery procedures in the Emergency guide.

---

## S

**Sandbox** - An isolated environment where untrusted code can run safely without affecting the rest of the system.

**Session** - A conversation or work period. Each user gets their own session so conversations don't mix.

**Skill** - A pre-built bundle of tools configured for a specific task. Example: the "Obsidian" skill reads/writes notes.

**SKILL.md** - The metadata file at the root of every custom skill folder. Defines the skill's name, description, version, required tools, permissions, and workflow instructions in YAML front matter + Markdown. Nanobot reads this file to register and validate a skill. See [Skills & Tools Complete Guide](Skills-and-Tools-Complete-Guide.md) for the full format.

**SOUL.md** - A root-level personality file at `~/.nanobot/SOUL.md`. Loaded on every startup, it defines persistent behavioral instructions for a nanobot instance: tone, default workflow style, and standing routines (e.g., nightly journal). Think of it as the agent's standing operating procedure.

**SSH** (Secure Shell) - A secure way to connect to and control a remote computer from your computer.

**Subagent** - An independent agent spawned to handle a specific part of a task. Subagents have their own context and memory, run in parallel, and report results back to the parent agent. See also *Orchestration*.

---

## T

**Tailscale** - A tool that creates a secure, private network between computers (even across the internet), so they can communicate safely.

**Tier** / **Model Tier** - A cost and capability classification for LLM models used in the AOS routing policy:
- **Tier A (Premium):** Claude Opus, GPT-4o — highest capability, highest cost. Used for complex research, synthesis, high-stakes decisions.
- **Tier B (Standard):** Claude Sonnet/Haiku, GPT-4o-mini — balanced. Used for most production tasks.
- **Tier C (Budget):** Qwen, DeepSeek, local Ollama — lowest cost, often free. Used for routine tasks like bookkeeping, summaries, high-volume channels.

Channel-to-tier mapping is defined in your governance policy. See [Security & Governance Policies](Security-and-Governance-Policies.md).

**Token** - This word has two distinct meanings in nanobot documentation. Context makes the meaning clear:
1. **Auth token** — A secret string that proves you are authorized to use a service (e.g., a Discord bot token or API key). Treat like a password.
2. **LLM token** — A unit of text (roughly one word or subword) used to measure how much text an AI model processes. LLM costs are priced per million tokens. Example: a 500-word message uses ~650 tokens.

**Tool** - An action nanobot can perform. Examples: web search, file read, shell execute.

**Troubleshoot** - Figure out what's wrong and fix it.

---

## U

**UFW** (Uncomplicated Firewall) - A firewall used on Linux systems to control network traffic.

**Uptime** - How long the system has been running without interruption. "99% uptime" means it's down for only 1% of the time.

---

## V

**Vault** (Obsidian) - The folder where Obsidian stores all your notes.

**VPS** (Virtual Private Server) - A rented computer in the cloud that you can control and customize.

---

## W


**Webhook** - A way for one system to automatically send data to another system when something happens. Example: "When a PR is created, send a message to Discord."

**Workspace** - The folder where nanobot stores its files and configuration.

**WSL** (Windows Subsystem for Linux) - A feature in Windows that lets you run a real Linux environment directly on your Windows computer, without needing a virtual machine. Useful for running Linux tools, bash scripts, and development workflows natively.

**WSL2** - The second generation of Windows Subsystem for Linux. WSL2 uses a lightweight virtual machine for better performance and full Linux kernel compatibility. Recommended for most users who want the best Linux experience on Windows.

---

## X — Z

No entries currently. [Suggest a term](https://github.com/EgoNoBueno/Nanobot-Agentic-Operating-System/issues)

---

## Common Acronyms Quick Reference

| Acronym | Meaning | Used For |
|---------|---------|----------|
| **API** | Application Programming Interface | Connecting systems |
| **CLI** | Command-Line Interface | Typing commands |
| **HTTP/HTTPS** | HyperText Transfer Protocol (Secure) | Web communication |
| **JSON** | JavaScript Object Notation | Data format |
| **KPI** | Key Performance Indicator | Measuring success |
| **LLM** | Large Language Model | AI model (Claude, GPT, etc.) |
| **MCP** | Model Context Protocol | Connecting tools |
| **OAuth** | Open Authentication | Secure login |
| **PR** | Pull Request | Code changes on GitHub |
| **RBAC** | Role-Based Access Control | User permissions |
| **SSH** | Secure Shell | Remote computer access |
| **UFW** | Uncomplicated Firewall | Network security |
| **VPS** | Virtual Private Server | Cloud computer |

---

## Useful Concepts Explained Simply

**What's an LLM?**
An LLM (Large Language Model) is artificial intelligence trained on massive amounts of text. It's really good at understanding language and writing. Claude, GPT-4, and Qwen are all LLMs.

**What's an API Key?**
An API key is like a password for a service. When you sign up for OpenAI, they give you an API key. You put that key in your config so OpenAI knows it's you making requests. Treat it like a password: don't share it.

**What's a VPS?**
A VPS (Virtual Private Server) is a computer you rent in the cloud. It's like having a dedicated computer running somewhere else. You can SSH into it and run nanobot on it 24/7.

**What's Obsidian?**
Obsidian is a note-taking app. It runs on your computer and stores notes as markdown files in a folder. Nanobot can read and write to Obsidian, so it remembers things across conversations.

---

## Common Misunderstandings Clarified

**❌ "Nanobot is only for Discord"**  
✅ **Truth:** Nanobot works with Discord, Slack, Telegram, Feishu, WhatsApp, Email, and more. The platform doesn't matter—the features work the same.

**❌ "API Key = Password"**  
✅ **Truth:** Similar but different. A password proves *who you are*. An API key lets a program do things *on your behalf*. Treat both as secrets!

**❌ "LLM = ChatGPT"**  
✅ **Truth:** ChatGPT is one LLM. Claude, Gemini, GPT-4, Qwen, and Ollama are also LLMs. An LLM is the category; ChatGPT is one example.

**❌ "Bash = Python"**  
✅ **Truth:** Both are programming languages, but different uses. Bash is for command-line/shell tasks (installing software, running scripts). Python is for writing complex programs. Our installation uses Bash; nanobot itself uses Python internally.

**❌ "Allowlist = Allowance (like parents give kids pocket money)"**  
✅ **Truth:** An allowlist is a security list of *approved things*. Only items on the list are allowed; everything else is blocked. Think of it like a nightclub bouncer with a list of approved guests.

**❌ "Token = Cryptocurrency"**  
✅ **Truth:** In our docs, "token" has two meanings: (1) a unit of text (~1 word), used for counting AI usage cost, or (2) a secret string (like API key) used for authentication. Not cryptocurrency! Context makes the meaning clear.

**❌ "MCP = a new AI model"**  
✅ **Truth:** MCP (Model Context Protocol) is a *standard* for connecting external tools/services to AI models. Like an adapter that lets nanobot use databases, file systems, APIs, and custom business logic.

**❌ "Vault = where nanobot stores money"**  
✅ **Truth:** In our docs, "vault" always means an Obsidian vault—a folder containing your notes that nanobot can read/write. It's called a "vault" because it's secure and locked down.

---

**Have a term that's confusing? Submit an issue or let us know!**

---

## Revision History

| Date | Version | Change |
|---|---|---|
| 2026-02-26 | 1.2.0 | Added 6 missing AOS terms: Approval Gate, Audit Trail, Context Window, Degraded Mode, SKILL.md, Tier/Model Tier |
| 2026-02-26 | 1.1.0 | Review pass: merged Agent Orchestration into Orchestration, added Control Plane + SOUL.md entries, expanded Plane and Token entries, fixed Injection definition, fixed Sub-Agent → Subagent, collapsed empty X/Y/Z sections |