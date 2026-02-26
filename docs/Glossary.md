# Glossary of Terms

Quick reference for technical terms, acronyms, and concepts used throughout the Nanobot documentation.

---

## A

**Agent Orchestration** - Splitting large tasks into smaller, specialized agents that work in parallel. Example: one agent researches competitors while another agent verifies the findings.

**Allowlist** - A list of specific users, channels, or tools that are explicitly approved to use sensitive features. Only items on the allowlist are allowed; everything else is blocked.

**API** (Application Programming Interface) - A way for different programs to talk to each other. Think of it as a standardized menu that one program offers, so other programs know exactly what requests to make.

**Artifacts** - Files, notes, or records that the system creates and saves for later reference. In nanobot, artifacts are stored in Obsidian for audit trails and recovery.

**Auth** / **Authentication** - Proving who you are. Usually done with a password or API key.

---

## B

**Bot** - Short for robot. In this context, nanobot is a software bot that listens to messages and responds automatically.

**Bash** - A common command-line language used on Linux, Mac, and Windows (via WSL or Git Bash). We use it for installation and troubleshooting commands.

---

## C

**Channel** - A conversation space. Examples: Discord channel, Slack channel, Telegram group. Nanobot can listen to multiple channels at once.

**Channel-Agnostic** - Works the same way no matter which chat platform you use (Discord, Slack, Telegram, etc.). You don't need different rules for each platform.

**CLI** (Command-Line Interface) - A way to interact with a computer by typing commands instead of clicking buttons. All our installation and troubleshooting uses the CLI.

**Cron** - A tool that runs tasks on a schedule (e.g., "every day at 9 AM"). Named after the Roman god of time.

**Config** / **Configuration** - The settings file that tells nanobot how to behave (which channels to listen to, which LLM to use, etc.).

**Cost Ceiling** - A hard limit on how much money the system will spend. Once the limit is reached, no more requests are processed until the budget resets.

---

## D

**Deprecated** - Old technology that still works but is being phased out. Using deprecated features is not recommended.

**Dashboard** - A display that shows the current status of your system (e.g., "How many tasks completed today?" or "What's the CPU usage?").

**Delegation** - Asking someone (or something) else to do a task for you. Sub-agents delegate work to other agents.

---

## E

**Escalation** - Sending a decision "up the chain" because it needs human approval. Example: a high-cost operation gets escalated to a finance team member.

**Executor** - Something that runs commands. The nanobot executor handles web searches, file operations, shell commands, etc.

---

## F

**Fallback** - A backup plan. If your first choice fails, the fallback is automatically used instead.

**Firewall** - A security tool that controls which network traffic is allowed in and out of a computer.

---## G

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

**Injection** - Adding code to the system dynamically (without restarting). You can "inject" a new skill via Discord.

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

**Memory Plane** - The part of nanobot that stores information for later use. Obsidian is the memory store.

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

**Orchestration** - Coordinating multiple agents or tasks to work together smoothly.

**Operator** - A human user who controls nanobot. You're the operator.

---

## P

**Payload** - The data being sent or received. E.g., the message content is the payload.

**Permission** - The right to do something. "Shell execute permission" means you're allowed to run shell commands.

**Plane** - A layer of the system. Nanobot has three planes: Control (channels), Policy (routing), Memory (Obsidian).

**Policy** - A rule about how something should work. "Require approval for costs over $50" is a policy.

**Policy Plane** - The part of nanobot that makes decisions about which model to use, when to escalate, cost limits, etc.

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

**SSH** (Secure Shell) - A secure way to connect to and control a remote computer from your computer.

**Sub-Agent** - An independent agent spawned to handle a specific part of a task.

---

## T

**Tailscale** - A tool that creates a secure, private network between computers (even across the internet), so they can communicate safely.

**Token** - A temporary code that proves you're authorized. "API token" proves you have permission to use an API.

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

## X

(Currently empty)

---

## Y

(Currently empty)

---

## Z

(Currently empty)

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
