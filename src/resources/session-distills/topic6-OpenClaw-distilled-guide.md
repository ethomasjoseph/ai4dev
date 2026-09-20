# Building and Operating Autonomous AI Assistants with OpenClaw: Architecture, Deployment, Token Economics, and Security

## Executive Summary

Autonomous agentic systems represent a fundamental paradigm shift in artificial intelligence, moving the industry from reactive interfaces to proactive infrastructure. Prior to autonomous runtimes, interacting with AI required users to visit an application, submit a prompt, await a response, and manually bridge the output across tools within an isolated, stateless session. OpenClaw reorients this workflow by operating as an always-on system environment where AI agents live on a server, monitor events 24/7, maintain persistent long-term memory, and initiate interactions directly within native messaging channels like Telegram or WhatsApp.

For non-technical stakeholders, OpenClaw can be conceptualized as an executive personal assistant that never sleeps, automates administrative overhead (such as managing calendar schedules and generating project tracker sheets), and proactively delivers status briefs. For technical practitioners and software engineers, OpenClaw is not a standalone agent or prompt wrapper; it is an open-source runtime, architecture, and execution engine. It supplies the "hands and feet" (browser access, system shell, API connections, and tool execution) to external Large Language Model (LLM) "brains," while managing persistent context across multi-turn sessions.

```
+-----------------------------------------------------------------------+
|                         OpenClaw Architecture                         |
+-----------------------------------------------------------------------+
|  Messaging Channels: Telegram, WhatsApp, Slack, Discord               |
|                                  |                                    |
|                                  v                                    |
|  OpenClaw Gateway (Control Plane, Routing, Allowlist Authentication)  |
|                                  |                                    |
|                                  v                                    |
|  Workspace Configuration (.md Layer):                                 |
|  - Intelligence: user.md | identity.md | soul.md | memory.md          |
|  - Execution:    tools.md | heartbeat.md | bootstrap.md               |
|                                  |                                    |
|                                  v                                    |
|  Model Router (LLM Brain): Claude 3.5/3.7 Sonnet, Haiku, MiniMax, etc.|
|                                  |                                    |
|                                  v                                    |
|  Skills & Tools: Shell Execution, Browser, Google Workspace, GitHub   |
+-----------------------------------------------------------------------+

```

Operating an autonomous runtime requires deep architectural discipline. Unchecked autonomous agents can rapidly burn thousands of dollars in token usage due to recurring system prompt injection, expansive tool schemas, context replay snowballing, and hidden orchestration sub-calls. Furthermore, granting an agent native shell execution and local file access introduces critical attack surfaces, ranging from prompt injections to unauthorized file modifications.

This comprehensive guide breaks down the core architecture of OpenClaw, step-by-step cloud deployment on a Virtual Private Server (VPS), messaging integration, token cost optimization, skill creation, automated pull request (PR) reviews, and security hardening.

---

## Tools Required

The following tools and prerequisites are necessary to build, deploy, configure, and operate OpenClaw agents as outlined in this guide:

| Tool / Service                     | Category              | Purpose                                                                              | Recommended Tier / Specs                                             |
| ---------------------------------- | --------------------- | ------------------------------------------------------------------------------------ | -------------------------------------------------------------------- |
| **Linux Cloud VPS**                | Infrastructure        | Hosts the 24/7 always-on OpenClaw runtime, Docker daemon, and cron tasks.            | Hostinger KVM 2 VPS (2 vCPU, 8 GB RAM, 100 GB SSD) or higher.        |
| **OpenClaw Gateway**               | Agent Runtime         | Core open-source operating engine, session manager, and channel router.              | Latest stable containerized deployment.                              |
| **Docker Engine & Compose**        | Containerization      | Sandboxes agent execution, isolating shell and tool operations from the host OS.     | Pre-installed via VPS template or Docker Manager.                    |
| **SSH Terminal Client**            | System Administration | Remote root and administrative shell access to configure server workspaces.          | OpenSSH, PuTTY, Windows Terminal, or macOS Terminal.                 |
| **Telegram Messenger & BotFather** | Client Interface      | Primary messaging channel for interacting with the agent via a dedicated bot.        | Free Telegram account with API bot token from `@BotFather`.          |
| **Telegram UserInfoBot**           | Identity Verification | Extracts numerical Telegram user IDs to enforce strict allowlist security.           | `@userinfobot` on Telegram.                                          |
| **LLM Provider API Keys**          | Cognitive Engine      | Supplies reasoning, tool calling, and structured generation capabilities.            | Anthropic Claude API (Tier 2+ recommended) or Nexos AI / OpenRouter. |
| **Claude Code or Codex CLI**       | Dev/Ops Orchestration | Terminal-based agentic engineer used to configure OpenClaw files and install skills. | Installed directly in the server environment.                        |
| **Git & GitHub Account**           | Version Control       | Required for repository integration, skill cloning, and automated PR reviews.        | Standard Git CLI and active GitHub account.                          |

---

## Core Architecture: Deconstructing OpenClaw

### Runtime vs. Agent: Understanding the Operating System Model

A common misconception among practitioners is that OpenClaw is an AI agent. OpenClaw is **not** an agent out of the box; it is an agentic runtime, an execution engine, and an operating system. A newly deployed OpenClaw instance is bare-bones infrastructure without predefined personality, behavioral constraints, domain workflows, or cognitive models.

Just as a smartphone requires an operating system, hardware interfaces, and applications to be functional, an autonomous agent requires an orchestration runtime to bridge reasoning models with real-world tooling. OpenClaw establishes that runtime environment, providing the gateway control plane, event listener loops, and tool execution protocols. Practitioners configure and instantiate agents *inside* OpenClaw by supplying structured configuration files and attaching model providers.

### The 4 Foundational Pillars of OpenClaw

Every functional agent living within the OpenClaw runtime consists of four distinct architectural layers:

- **1. The Brain (Cognitive Layer):** The underlying Large Language Model (LLM) responsible for reasoning, planning, syntactic comprehension, code analysis, and decision-making. OpenClaw does not bundle a proprietary model; developers configure hosted APIs (such as Anthropic Claude, OpenAI GPT, DeepSeek) or local weights.
- **2. The Body (Execution & Channel Layer):** The input/output adapters and physical actuators that grant the agent "hands and feet". This includes messaging integrations (Telegram, WhatsApp, Slack), web browsers, Google Workspace APIs, and local shell execution.
- **3. The Memory (Context & State Layer):** Persistent cross-session storage that records user preferences, past executions, workflow constraints, and dynamic facts. Unlike standard stateless chat sessions, OpenClaw maintains an evolving memory substrate that adapts over time.
- **4. The System (Host & Daemon Layer):** The underlying infrastructure that executes continuously (24/7) on a host or cloud virtual server. This layer manages the Gateway control plane, schedules proactive background loops, runs cron jobs, and manages background event listening.

### The Configuration Framework: The 7 Markdown Files

To transform an empty OpenClaw runtime into an autonomous assistant, developers populate specific plain-text Markdown files inside the agent's workspace. These files dictate who the agent serves, how it behaves, its hard operational limits, and which tools it can call.

```
Agent Workspace Directory
│
├── Intelligence Layer
│   ├── user.md         # User background, role, timezone, preferences
│   ├── identity.md     # Agent persona, tone, communication formatting
│   ├── soul.md         # Ethics, behavioral boundaries, hard execution limits
│   └── memory.md       # Long-term knowledge, evolving operational facts
│
└── Execution Layer
    ├── tools.md        # Explicit permissions, parameter contracts, schemas
    ├── heartbeat.md    # Scheduled cron reviews and background workflows
    └── bootstrap.md    # Session initialization and startup execution scripts

```

#### The Intelligence Layer

- **`user.md`:** Contains explicit data about the human operator. It defines professional background, organizational role, communication preferences, core priorities, and working hours. This ensures the agent contextualizes all actions without repetitive user prompting.
- **`identity.md`:** Establishes the agent's internal persona, role definition, name, output length constraints (e.g., restricting summaries to under 200 words), and tone.
- **`soul.md`:** The most critical behavioral document in the workspace. It dictates behavioral boundaries, privacy rules, ethical guidelines, and hard limits. It explicitly states what the agent must *never* do (e.g., "Never modify production databases without human confirmation," "Never message external contacts without approval"). Without a strictly configured `soul.md`, autonomous agents risk executing rogue actions or editing system files uncontrollably.
- **`memory.md`:** Serves as the agent's persistent long-term memory bank. It records operational context, lessons learned from prior failures, dynamic preferences, and project milestones across conversational turns.

#### The Execution Layer

- **`tools.md`:** Documents tool execution constraints, parameter formats, authorization keys, and scope boundaries. It prevents the agent from abusing external integrations or executing tools outside designated parameters.
- **`heartbeat.md`:** Configures autonomous, proactive scheduling (cron tasks). It defines recurring review cycles where the agent wakes up independently (e.g., every morning at 6:00 AM or on an hourly interval) to audit security logs, check calendars, or review pull requests without waiting for a user prompt.
- **`bootstrap.md`:** An optional startup and initialization script executed when the agent session spins up. It can handle initial greeting sequences or environment health checks, though it should be streamlined to avoid excessive token consumption.

### Multi-Agent Workspaces: Lead Coordinator and Sub-Agents

OpenClaw supports multi-agent architectures within a single deployment. Instead of overloading a single agent with dozens of distinct tools and conflicting system instructions, developers structure workspaces hierarchically:

- **Lead Agent (Coordinator):** Operates at a high level of abstraction. Its `soul.md` instructs it to interpret user requests, maintain the broad operational picture, avoid getting bogged down in low-level details, and route execution to specialized sub-agents.
- **Sub-Agents (Domain Specialists):** Isolated instances equipped with specific tools and tailored markdown definitions. Examples include an Email Specialist (equipped solely with Gmail APIs), a Sheet Tracker Agent, a Security Auditor, or a Pull Request Reviewer.
- **Inter-Agent Workflow:** When a request arrives (e.g., "Analyze our latest pull request and log tickets for any security flaws"), the Lead Agent spawns the PR Review Agent, captures its structured verdict, and passes the output to the Sheet or Issue Tracker Agent.

---

## Deployment & Environment Setup: Local vs. Cloud VPS

### Architectural Comparison: Local Sandbox vs. Cloud VPS

Choosing between running OpenClaw locally or on a cloud-based Virtual Private Server (VPS) dictates availability, cron reliability, and security exposure:

| Architectural Dimension              | Local Machine Deployment                                                                      | Cloud VPS Deployment (e.g., Hostinger KVM2)                                                      |
| ------------------------------------ | --------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------ |
| **Operational Uptime**               | Suspends whenever the host machine sleeps, reboots, or loses internet connectivity.           | Operates 24/7/365 with persistent cloud uptime and networking.                                   |
| **Proactive Tasks (`heartbeat.md`)** | Unreliable; scheduled cron jobs fail if the workstation is closed or suspended.               | Highly reliable; proactive background reviews run on exact schedules.                            |
| **Security & Blast Radius**          | High risk; the agent has direct read/write/execute access to local directories and OS shells. | Isolated; agent executes inside a sandboxed Linux/Docker environment separated from local files. |
| **System Resource Impact**           | Competes with local IDEs, browsers, and desktop workloads for CPU, RAM, and disk I/O.         | Dedicated server hardware (e.g., 2 vCPU, 8 GB RAM) independent of your client.                   |
| **Remote Client Access**             | Difficult to route safely to mobile messaging apps outside the local network.                 | Native cloud networking enables direct webhooks to Telegram, WhatsApp, and Slack.                |

Running OpenClaw directly on an enterprise or primary work workstation poses serious security hazards. Because autonomous agents can execute shell commands, alter files, and install third-party scripts, a configuration error or malicious prompt injection can compromise local corporate directories. A dedicated, sandboxed VPS is the standard best practice for production workloads.

### Managed OpenClaw vs. Self-Managed VPS

When provisioning on cloud providers such as Hostinger, practitioners encounter two primary deployment options:

- **Managed OpenClaw (Beginner/Turnkey):** A pre-configured application environment manageable through a web UI (such as Hostinger's hPanel). While it allows quick messaging pairing, it restricts low-level terminal access, custom skill installation, and manual filesystem edits.
- **Self-Managed VPS (KVM2 Plan):** Grants complete root SSH access to the underlying Linux instance. Developers retain full control over Docker containers, package managers, custom MCP servers, environment variables, and filesystem configurations. Technical practitioners should select the Self-Managed VPS plan to build custom multi-agent workflows.

### Step-by-Step Tutorial: Provisioning & Initializing OpenClaw on Hostinger KVM2

#### 1. Server Provisioning

Log in to Hostinger, navigate to VPS hosting, and select the **KVM2** plan (or higher) with an Ubuntu-based Docker template. Complete the provisioning cycle and select your preferred data center region.

#### 2. Root Access Setup

In hPanel under VPS Management, locate your **Server IP Address** and navigate to the **SSH Access / Root Password** section. Set a complex, high-entropy password or configure SSH public-key authentication.

#### 3. Establish Terminal Connection

Open your local terminal and connect to your VPS via SSH:

```bash
ssh root@<YOUR_SERVER_IP>

```

Enter your secure password or decrypt your private key when prompted.

#### 4. Access Docker Project Directory

Hostinger pre-packages OpenClaw within containerized Docker environments. Locate the application directory via Docker Manager or run:

```bash
docker ps
cd /home/openclaw/project # Or designated Hostinger application root
ls -la

```

Confirm that the OpenClaw configuration files, environment variables, and Docker Compose configurations are present.

#### 5. Install Claude Code as the System Engineer

To streamline configuring `.md` files and installing dependencies inside the terminal without manual text editing, install Claude Code on the server:

```bash
npm install -g @anthropic-ai/claude-code
claude

```

Log in using your Anthropic account or direct API key. You can now use Claude Code as a terminal-based system engineer to manage OpenClaw configurations, patch files, and inspect runtime logs.

### Step-by-Step Tutorial: Connecting Telegram Bot & Securing the Gateway

Connecting OpenClaw to Telegram enables mobile command and control. Securing this connection is critical to ensure unauthorized public users cannot query your agent or execute tools.

#### 1. Generate Bot Credentials with BotFather

1. Open your Telegram client and search for the verified account `@BotFather`.
2. Click **Start** and send the command `/newbot`.
3. Enter a descriptive display name for your assistant (e.g., `Outskilled_Engineer_Bot`).
4. Enter a unique username ending in `bot` (e.g., `Outskilled_Engineering_bot`).
5. BotFather will generate an HTTP API token (e.g., `7891234567:ABCdefGhIJKlmNoPQRstuVWXyz`). Copy this secret token immediately.

#### 2. Retrieve Your Telegram User ID

1. In Telegram, search for `@userinfobot`.
2. Click **Start**.
3. The bot will return your profile details, including your numerical **Id** (e.g., `123456789`). Copy this number.

#### 3. Configure the OpenClaw Gateway Control Plane

1. Access your OpenClaw web control panel (the Gateway Dashboard) via the exposed port link in your VPS manager.
2. Provide your secure **Gateway Token** to log in to the control UI.
3. In the left navigation menu, go to **Settings** $\rightarrow$ **Communications** $\rightarrow$ **Channels**.
4. Select **Telegram** from the list of available messaging channels.

#### 4. Enforce Strict Allowlist Security

1. Scroll down to the **Telegram DM Policy** configuration.
2. Change the policy from `Pairing Mode` or `Public` to **Allowlist**.
3. Under **Telegram Bot Token**, paste the token copied from BotFather.
4. Under **Actions $\rightarrow$ Allow From**, click **Add** and paste your numerical Telegram User ID.
5. Click **Apply and Save** to restart the channel listener.

```
+-------------------------------------------------------------------------+
|                  Telegram Channel Security Architecture                 |
+-------------------------------------------------------------------------+
|                                                                         |
|   Any Telegram User --------> [ Telegram DM Policy ]                    |
|                                       |                                 |
|                                       | (Check Sender ID)               |
|                                       v                                 |
|                         Is User ID in Allowlist?                        |
|                               /             \                           |
|                             YES              NO                         |
|                             /                 \                         |
|                            v                   v                        |
|                  [ OpenClaw Gateway ]     [ REJECT & SILENT DROP ]      |
|                            |                                            |
|                            v                                            |
|                  Executes Tool / Agent                                  |
+-------------------------------------------------------------------------+

```

With the allowlist active, only messages originating from your verified numerical Telegram account will be processed by the Gateway. Messages from any other user are rejected automatically, eliminating public access risks.

---

## Token Economics & Cost Optimization

### The "Hidden Taxes" of Autonomous Agent Runtimes

Autonomous agents consume tokens at a significantly higher rate than typical chat interfaces. In unmonitored deployments, costs can scale exponentially. For example, developer Federico accrued a $3,600 API bill in a single month during experimental OpenClaw testing. Understanding the hidden token overhead of autonomous systems is essential for cost management:

```
+---------------------------------------------------------------------------+
|                  Anatomy of an Autonomous Agent Request                   |
+---------------------------------------------------------------------------+
|                                                                           |
|   User sends 5 tokens: "What is on my calendar tomorrow?"                 |
|                                                                           |
|   Hidden System Ingestion:                                                |
|   ├── user.md Context Injection               (~1,500 tokens)             |
|   ├── identity.md Persona Injection             (~800 tokens)             |
|   ├── soul.md Behavioral Rules & Limits       (~3,500 tokens)             |
|   ├── memory.md Historical Recall             (~2,000 tokens)             |
|   ├── tools.md Complete JSON Tool Schemas     (~4,000 tokens)             |
|   └── Context Replay (Prior Turn History)     (~3,200 tokens)             |
|                                                                           |
|   Total Input Ingestion: ~15,000 Tokens (For a 5-token user prompt!)      |
+---------------------------------------------------------------------------+

```

- **System Prompt Tax:** Every conversational turn forces OpenClaw to inject all foundational workspace files (`user.md`, `identity.md`, `soul.md`, `tools.md`) into the model's context window. Even a simple greeting like "Hi" causes the runtime to re-ingest thousands of tokens of background instructions.
- **Tool Schema Overhead:** When skills are active, their complete JSON schemas, input definitions, parameter descriptions, and validation rules must be passed to the LLM. Having 10 active skills can add 4,000 to 8,000 tokens to every single API call before any reasoning occurs.
- **Context Replay (The Snowball Effect):** In persistent chat sessions, the entire message history—prior user inputs, assistant outputs, intermediate tool invocations, and raw tool outputs—is re-sent on subsequent turns. The context window grows continuously with each interaction.
- **Heartbeat & Cron Drain:** Proactive monitoring loops defined in `heartbeat.md` execute on scheduled intervals regardless of whether new work is required. If configured using frontier models like Claude 3.5/3.7 Sonnet, routine wake-up calls will quickly deplete token quotas.
- **Hidden Background Calls:** Fulfilling a single user instruction often triggers multiple internal sub-calls: intent classification, tool routing, syntax validation, output formatting, and memory updates. A single visible response may reflect 4 to 6 discrete LLM API requests.
- **The Multi-Agent Multiplier:** In a multi-agent system, every sub-agent spawned maintains its own workspace context, tool schema, and system prompt. Spawning three agents in sequence to handle one task triples or quadruples baseline token burn.

### The 8 Rules of Token Efficiency

To maintain control over operational expenses, practitioners should implement eight architectural practices:

- **1. Reset Context Regularly (`/start new`):** Clear conversational history whenever shifting to an unrelated task. Stale interaction history is dead weight; flushing it eliminates the context replay snowball effect.
- **2. Maintain Lean System Files:** Keep `soul.md`, `identity.md`, and `user.md` concise. Target under 4,000 to 5,000 characters per file. Audit these files quarterly to excise redundant guidelines.
- **3. Model Downgrades for Heartbeats:** Never assign frontier reasoning models to routine heartbeat polls. Use lightweight models (such as Claude Haiku, MiniMax, Qwen, or DeepSeek) to evaluate scheduled conditions.
- **4. Dynamic Task-Based Model Routing:** Configure OpenClaw to dynamically route requests based on operational complexity:
  - *Complex Reasoning & Code Reviews:* Claude 3.5 / 3.7 Sonnet.
  - *Intermediate Automation, Calendars & Sheets:* Claude Haiku.
  - *Lightweight Classifications & Conversational Routing:* MiniMax, Qwen, or local models.

- **5. Prune** **`memory.md`** **to Prevent Memory Decay:** As the agent logs interactions, `memory.md` accumulates outdated, trivial entries. Periodically prune this file—manually or via a maintenance script—to remove stale context.

- **6. Restrict Background Call Models:** Route internal intent classification, safety checks, and response formatting passes through cost-effective utility models.

- **7. Enable Prompt Caching:** Leverage provider-level prompt caching (such as Anthropic Prompt Caching). Because system prompts and tool schemas remain identical across calls, prompt caching can reduce input token costs by up to 90%.

- **8. Batch Inquiries (One Message, One Ask):** Avoid chatty, fragmented conversational turns. Combine context, inputs, and constraints into a single, comprehensive prompt to minimize recurring system prompt overhead.

### Managing LLM Providers: API Tokens vs. OAuth

Practitioners must carefully consider authentication methods for LLM backends:

- **The Dangers of OAuth Subscriptions:** Never connect consumer subscription OAuth accounts (such as web-based Claude Pro/Team accounts or personal Google logins) to autonomous runtimes. Automated agent workflows violate consumer terms of service, and providers actively issue account terminations for unauthorized headless tool integrations.

- **Direct API Keys:** Always authenticate via direct developer API tokens. Direct APIs provide transparent usage metrics, support programmatic rate limits, allow hard spending caps (e.g., setting a monthly budget ceiling of $100 in the Anthropic Console), and prevent unexpected overages.

- **Anthropic Tier Escalation:** Newly created Anthropic API accounts default to Tier 1, which imposes restrictive rate limits (e.g., 20,000–30,000 tokens per minute) that will cause autonomous agents to fail with timeout errors. Depositing $40 or more immediately elevates the organization to **Tier 2**, unlocking up to 450,000 tokens per minute and providing the operational headroom required for multi-tool agent execution.

---

## The Skills Ecosystem & Extensibility

### Anatomy of an OpenClaw Skill

Skills give OpenClaw agents modular capabilities. Structurally, a skill is a directory containing a required `SKILL.md` file alongside optional execution scripts (Python, Node.js, Shell) or binaries.

```
skills/
└── github-pr-reviewer/
    ├── SKILL.md            # Metadata, triggering conditions, instructions
    ├── schemas/
    │   └── review_rule.json# Parameter contracts and expected inputs
    └── scripts/
        └── fetch_diff.sh   # Actuator script executed inside the sandbox

```

The `SKILL.md` file defines:

- The unique name and purpose of the capability.

- Explicit activation triggers instructing the LLM when to invoke the skill.

- Step-by-step reasoning steps for the agent to execute.

- Expected parameter contracts, execution constraints, and structured output formats.

### Exploring ClawHub and Community Repositories

Skills can be created manually or sourced from community registries like **ClawHub** (`clawhub.ai`) and community curation repositories:

- **ClawHub (`clawhub.ai`):** A centralized discovery marketplace for OpenClaw skills. It indexes hundreds of modular extensions covering Slack, Trello, web search engines (Tavily, Firecrawl), GitHub integrations, and database connectors.

- **Awesome OpenClaw Skills:** A curated open-source index cataloging production-ready skills, agent templates, and orchestration patterns.

- **Skill Categories:** Capabilities generally span creative generation (Nano Banana, image synthesis), communication (email, Twitter/X, SMS), productivity (Google Calendar, Google Sheets), data acquisition (web scraping, API lookups), software engineering (GitHub actions, PR reviewers, CI/CD runners), and systems operations (security audits, server monitoring).

### Skill Security: Prompt Injections & Malicious Manifests

While community marketplaces accelerate development, importing third-party skills introduces major security risks:

- **Prompt Injection Payloads:** A malicious skill can embed hidden prompt injection strings within its `SKILL.md` instructions. Once imported, these instructions can command the agent's LLM to exfiltrate environment secrets, scan private files, or alter system configurations.

- **The Chevrolet Chatbot Incident:** A real-world demonstration of prompt injection vulnerabilities occurred when an automotive dealership deployed an under-constrained customer-facing AI agent. A user successfully engineered an adversarial prompt instructing the bot to agree to sell a brand-new vehicle for $1, which the bot legally accepted in chat before being shut down.

- **Filesystem & API Abuse:** Untrusted scripts can leverage OpenClaw's execution privileges to read local directories or transmit credentials to external servers.

#### Safe Import Checklist

1. Review every line of a third-party `SKILL.md` file before moving it into your active workspace.

1. Inspect underlying shell scripts and Python files for obfuscated code or unauthorized network calls.

1. Run third-party skills in read-only mode first, verifying behavior before granting write or administrative privileges.

1. Prefer writing custom, in-house skills tailored specifically to your organization's security posture.

---

## Practical Implementations & Tutorials

### Tutorial 1: Automating Daily Ops (Google Calendar & Sheets Tracker)

This implementation establishes an assistant that queries schedules, creates project tracking sheets, and updates task statuses directly through messaging prompts.

```
+-------------------------------------------------------------------------+
|                  Google Workspace Agent Execution Flow                  |
+-------------------------------------------------------------------------+
|                                                                         |
|   User Prompt (Telegram): "Create a project tracker sheet for Website"  |
|                               |                                         |
|                               v                                         |
|   OpenClaw Gateway receives payload, parses allowlisted sender ID       |
|                               |                                         |
|                               v                                         |
|   LLM selects Google Sheets Skill via tools.md schema contract          |
|                               |                                         |
|                               v                                         |
|   Executes Google Sheets API call via authenticated service credentials |
|                               |                                         |
|                               v                                         |
|   Sheet created with structured columns: Task, Status, Owner, Deadline  |
|                               |                                         |
|                               v                                         |
|   Returns confirmation link directly into the Telegram DM session       |
+-------------------------------------------------------------------------+

```

#### Step 1: Configure Workspace Credentials

1. Set up a project in Google Cloud Console, enable the Google Calendar API and Google Sheets API, and generate OAuth credentials or service account keys.

1. Place the credential payload securely within your server's OpenClaw configuration directory.

#### Step 2: Bind the Tool Schemas

Ensure `tools.md` contains the interface contract for Google Workspace:

```markdown
# tools.md (Excerpt)

## Tool: google_calendar

- Description: Queries, creates, and reschedules Google Calendar events.
- Parameters: `action` (list, create, delete), `time_min`, `time_max`, `summary`.
- Permissions: Read/Write. Ask confirmation before deleting events.

## Tool: google_sheets

- Description: Generates, reads, and updates Google Spreadsheets.
- Parameters: `action` (create, append, update_cell), `title`, `data_matrix`.
- Permissions: Write access granted within user-owned drives.
```

#### Step 3: Interactive Verification via Messaging Client

Once the channel is listening, execute interactive tests directly via Telegram:

1. **Querying Calendar:**

```text
User: What is on my calendar for tomorrow?
Assistant: Checking your calendar... You have 2 events scheduled tomorrow:
- 07:00 AM - 08:00 AM: Jogging
- 08:30 AM - 09:15 AM: Breakfast & Daily Planning

```

1. **Generating Sheets:**

```text
User: Create a task tracker sheet for client onboarding with columns Task, Owner, Status, and Due Date.
Assistant: Created tracker sheet "Client Onboarding Tracker". Link: https://docs.google.com/spreadsheets/d/1A2B3C...
Initial columns initialized with standard onboarding milestones.

```

1. **Updating Task Status:**

```text
User: Mark the task "DNS Configuration" as Complete in the tracker sheet.
Assistant: Located task "DNS Configuration" on row 4. Status cell updated to "Complete".

```

### Tutorial 2: Building an Autonomous GitHub PR Review Agent

This tutorial sets up a specialized software development lifecycle (SDLC) agent that acts as an automated, in-house code reviewer—similar to a self-hosted CodeRabbit. The agent analyzes pull requests, flags bugs and security vulnerabilities, identifies missing tests, and generates structured remediation plans.

#### Step 1: Establish the Dedicated Agent Directory

Inside your OpenClaw workspace directory, create an isolated folder for the PR reviewer:

```bash
mkdir -p workspace/agents/pr-reviewer
cd workspace/agents/pr-reviewer

```

#### Step 2: Author the System Instruction (`pr-review-agent.md`)

Create `pr-review-agent.md` to define the reviewer's cognitive boundaries:

```markdown
# pr-review-agent.md

You are a Senior Staff Software Engineer embedded in a high-velocity development team.
Your responsibility is to perform rigorous, practical pull request code reviews before branches merge into production.

Operational Objectives:

1. Review pull requests for correctness, logic errors, duplicate operations, and edge-case race conditions.
2. Flag missing input validations, unhandled exception paths, and security/privacy risks.
3. Detect time zone bugs, mutable state sharing, and memory leaks.
4. Verify comprehensive unit/integration test coverage.
5. Provide actionable, concise engineering recommendations.

Output Constraints:

- Never respond like a generic chatbot or basic syntax linter.
- Return structured findings: Verdict, Top 5 Issues, Missing Tests, Developer Action Plan.
- Enforce strict word count limits (under 250 words total).
```

#### Step 3: Configure Tool and Execution Triggers

In the sub-agent's configuration, grant access to Git CLI tools and repository access tokens so it can fetch code diffs using:

```bash
git diff origin/main...HEAD

```

#### Step 4: Execute Interactive Review via Chat

Send the review command through your Telegram client:

```text
User: Review PR https://github.com/example-org/auth-service/pull/42.
Use the PR review agent workflow. Return only: Verdict, Top 5 issues, Missing tests in 3 bullets, Final developer action.
Keep it under 180 words.

```

The agent processes the diff and returns a structured evaluation:

```text
Assistant:
VERDICT: REQUEST CHANGES

TOP 5 ISSUES:
1. Concurrency: In-memory session store lacks mutex locks, risking race conditions under load.
2. Duplicate Reminders: Notification dispatcher triggers redundant emails on retries.
3. Input Validation: Unsanitized input on the redirect parameter exposes open-redirect vulnerability.
4. Error Handling: Silent catch block in token generation swallows underlying crypto failures.
5. Shared State: Test suites share mutable mock objects, creating test order dependency.

MISSING TESTS:
* Zero test coverage for expired refresh token edge cases.
* No mock test simulating Slack API dispatch timeouts.
* Missing boundary check for payload sizes exceeding 2MB.

DEVELOPER ACTION:
Add idempotency guards to notification services, secure the redirect parameter, and add the three missing unit tests before re-requesting review.

```

#### Step 5: Requesting an Automated Fix Plan

Follow up in the same thread to generate an implementation plan:

```text
User: Based on this review, generate a developer fix plan.
Return: Fix order, Files to change, What to change, Tests to add.
Under 250 words. Do not write full code.

```

```text
Assistant:
DEVELOPER FIX PLAN: PR #42

1. FIX ORDER & IMPLEMENTATION:
- Step 1: File `src/services/notification.py` -> Implement Redis-based distributed lock to enforce idempotency.
- Step 2: File `src/api/auth.py` -> Add regex domain validation to redirect URLs to mitigate open redirects.
- Step 3: File `src/core/security.py` -> Log and rethrow explicit exceptions within the crypto token handler.

2. TESTS TO ADD:
- `tests/test_notifications.py`: Assert that dispatching the same event ID twice triggers only one outbound email.
- `tests/test_auth.py`: Parametrize malformed URLs against auth endpoints; verify 400 Bad Request responses.

Estimated remediation timeline: 2 hours.

```

#### Step 6: Automating via `heartbeat.md`

To run this pipeline autonomously without manual chat prompts, add the review workflow to `heartbeat.md`:

```markdown
# heartbeat.md (Excerpt)

Every 2 hours, execute:

1. Scan assigned GitHub repositories for new pull requests labeled 'needs-review'.
2. Run PR Review Agent against any open diffs.
3. Post the structured verdict as an internal PR comment.
4. If critical security flaws are detected, notify the technical lead directly on Telegram.
```

---

## Enterprise Readiness, Security Hardening & Observability

### Security Threat Model: Keys, Execution, and Exposure

Autonomous agents operate with significantly greater system access than read-only chatbots. Because an OpenClaw agent can read local files, execute arbitrary shell scripts, and connect to remote APIs, security breaches can compromise the host environment:

```
+-------------------------------------------------------------------------+
|                    OpenClaw Attack Surface Analysis                     |
+-------------------------------------------------------------------------+
|                                                                         |
|  Vector: Leaked Gateway Token                                           |
|  Impact: COMPLETE AGENT TAKEOVER. Adversary controls all tool access.   |
|                                                                         |
|  Vector: Public Messaging Channels (Unrestricted DMs)                   |
|  Impact: Prompt injection, unauthorized data access, compute drain.     |
|                                                                         |
|  Vector: Malicious Community Skills                                     |
|  Impact: Exfiltration of secrets, remote shell exploitation.            |
|                                                                         |
|  Vector: Autonomous Write Privileges                                    |
|  Impact: Accidental modification or deletion of production files.       |
+-------------------------------------------------------------------------+

```

### Hardening Guidelines

- **1. Gateway Token Secrecy:** The OpenClaw Gateway Token acts as a master key granting complete administrative control over the runtime. Never commit this token to public repositories or share it across untrusted channels. If exposed, rotate the token immediately and restart the Gateway.

- **2. Mandatory Channel Allowlists:** Never deploy messaging channels in open pairing or public DM mode. Enforce allowlists using explicit numerical user IDs (via tools like `@userinfobot`) to ensure unrecognized accounts are dropped silently.

- **3. Read-Only Staging:** When deploying an agent or integrating a new skill, configure permissions to read-only mode first. Allow the agent to retrieve information and propose changes in text. Only grant file-write, database-update, or API-dispatch permissions after verifying execution consistency.

- **4. Host Isolation & Sandboxing:** Always run OpenClaw inside dedicated Docker containers or non-root VPS instances. Restrict the agent's working directory to an isolated sandbox to prevent directory traversal attacks.

- **5. Operational Log Auditing:** Regularly monitor the OpenClaw Gateway's internal reasoning logs. Every model call, tool invocation, shell command, and decision path is logged, providing visibility into anomalous behavior or token spikes.

### Current Limitations: Single-Tenant vs. Multi-Tenant Deployments

Practitioners must understand that OpenClaw is currently architected as a **single-tenant** personal and small-team automation runtime, not an enterprise multi-tenant platform:

- **Single Tenant Boundary:** OpenClaw's design assumes that the operator who controls the Gateway owns all attached tool credentials.

- **Cross-User Data Leakage Risk:** Running multiple users through a single messaging bot integration can lead to data leakage. Because sessions share the underlying memory and execution context, the agent may expose one user's private data or tool outputs to another user.

- **Enterprise Roadmap:** While full multi-tenant enterprise agent architectures are actively being developed across the industry, deploying OpenClaw in corporate environments today requires provisioning separate isolated VPS instances for each user or functional group.

---

## Resources and References

The following external repositories, community indexes, session materials, and documentation provide additional context and resources for configuring and developing OpenClaw agents:

- **[OpenClaw Project Repository](https://www.google.com/search?q=https://github.com/OpenClaw-Project):** The primary open-source codebase for the OpenClaw agent runtime, architectural specifications, and platform documentation.
- **[Session Resources Drive](https://www.google.com/search?q=https://drive.google.com/drive/folders/1kFdLcOME-YSRj9_mAKzd58ifNCc1n9nh%3Fusp%3Dsharing):** The dedicated cloud repository housing session workbooks, configuration guides, presentation slide decks, and step-by-step setup walkthroughs.
- **[Awesome OpenClaw Skills](https://github.com/VoltAgent/awesome-openclaw-skills):** A curated community collection of production-ready skills, custom tool wrappers, and system templates.
- **[Post-Read Session Summary](https://docs.google.com/document/d/1cHLJ39efsxLXbia67-w1fHuHOPBTa4kl4tuHbYzBuYQ/edit?tab=t.yfpm0hciba95):** A post-session reference document covering architectural concepts, prompt patterns, and best practices.
- **[ClawHub Registry](https://clawhub.ai):** A community marketplace for exploring, downloading, and inspecting modular skills and integrations.
