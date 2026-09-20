# Orchestrating Modern AI Development: Building Complex Systems with Claude Code, Model Context Protocol (MCP), and Skills

Modern software engineering is undergoing an architectural transition from manual line-by-line syntax writing to agentic orchestration. As artificial intelligence models evolve from basic autocomplete utilities into autonomous systems, the primary role of the engineer shifts toward defining scope, designing systems, and orchestrating agents.

This comprehensive technical guide synthesizes the frameworks, live demonstrations, and architectural principles presented by practitioner Dileep Karri during Day 11 of the AI Engineering Accelerator. It details how to configure **Claude Code CLI**, connect external environments through the **Model Context Protocol (MCP)**, enforce deterministic workflows with **Skills**, and manage context handoffs using **Codex (Cursor)**.

---

## Executive Summary

Contemporary AI coding workflows frequently suffer from two common failure modes: "vibe coding," where developers issue vague prompts and receive unstable codebases, and token exhaustion, where conversational context limits stall long-running projects. Addressing these issues requires treating AI development not as an informal chat session, but as a disciplined orchestration pipeline.

The orchestration layer is structured around three foundational pillars:

1. **The Orchestrator (Claude Code CLI):** A specialized command-line interface that executes commands, manipulates the file tree, and coordinates subagents autonomously.
2. **The Connectors (Model Context Protocol / MCP):** An open standard establishing a universal client-server interface that connects large language models (LLMs) to external APIs, databases, web scrapers, and developer tools.
3. **The Playbooks (Skills):** Standardized, spec-driven behavioral templates that enforce rigorous software development life cycle (SDLC) methodologies—such as structured planning, design-system checks, and test-driven development—before executing code.

By chaining these components together, engineers can implement a clean separation of concerns: **Claude Code** is leveraged for system architecture, specification, and design, while platforms like **Codex (Cursor)** take over when execution or session limits require a seamless handoff.

```
┌───────────────────────────────────────────────────────────┐
│                      ORCHESTRATOR                         │
│                    (Claude Code CLI)                      │
└─────────────┬───────────────────────────────┬─────────────┘
              │                               │
              ▼                               ▼
┌───────────────────────────┐   ┌───────────────────────────┐
│        CONNECTORS         │   │         PLAYBOOKS         │
│           (MCP)           │   │         (Skills)          │
├───────────────────────────┤   ├───────────────────────────┤
│ • Firecrawl (Scraping)    │   │ • Superpowers (Design)    │
│ • FastMCP Custom Servers  │   │ • GSD Build (Execution)   │
│ • Local Databases / APIs  │   │ • Claude Mem (Persistence)│
└───────────────────────────┘   └───────────────────────────┘

```

This guide walks step-by-step through installing connectors and skills, designing custom hybrid playbooks, creating proprietary MCP servers from scratch, and building a multi-LLM debate engine called the **Model Council**.

---

## Tools & System Prerequisites

The following software, accounts, and credentials are required to reproduce the environment and build pipelines described in this guide:

| Tool / Platform                  | Category                | Purpose & Description                                                                                                      | Source / Reference                           |
| -------------------------------- | ----------------------- | -------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------- |
| **Claude Code CLI**              | Agentic IDE / CLI       | Command-line agent executing shell tasks, git actions, and automated builds. Paid tier (Anthropic Claude Max recommended). | Anthropic CLI                                |
| **Model Context Protocol (MCP)** | Protocol / Middleware   | Open standard protocol connecting LLMs (clients) to local and remote tools (servers).                                      | [mcpmarket.com](https://mcpmarket.com)       |
| **FastMCP**                      | Python Framework        | High-level library for creating custom MCP server wrappers around REST APIs.                                               | Python Package Index (`pip install fastmcp`) |
| **GSD Build (Get Shit Done)**    | Skill / Framework       | Spec-driven meta-prompting framework enforcing a six-step development cycle.                                               | GitHub Repository (`gsd-build`)              |
| **Superpowers**                  | Skill / Framework       | Brainstorming, architectural spec writing, and structured debugging playbook.                                              | GitHub Repository (`superpowers`)            |
| **Claude Mem**                   | Skill / Memory Engine   | Local context-persistence observer backed by an SQLite database to minimize token consumption.                             | Local SQLite Agent Plugin                    |
| **OpenRouter**                   | Multi-Model API Gateway | Unified API endpoint for routing queries across models (Claude Sonnet/Opus, GPT-5, Gemini Pro, Grok, Kimi).                | [openrouter.ai](https://openrouter.ai)       |
| **Superdesign**                  | Design Token Generator  | Web tool providing ready-made, accessible design system prompts and UI tokens.                                             | [superdesign.dev](https://superdesign.dev)   |
| **Codex (Cursor IDE)**           | Secondary IDE / Agent   | Implementation agent leveraged for deep code writing, diff analysis, and additive builds.                                  | Cursor / Codex Workspace                     |
| **Git**                          | Version Control         | Required for tracking atomic commits, managing state trees, and running phase gates.                                       | Local CLI Installation                       |

---

## Architectural Foundations: Claude Code, MCP, and Skills

To understand how an autonomous development environment operates, consider the functional analogies introduced during the session.

### The Conceptual Analogies

```
Carpenter Analogy:
  LLM (Claude Code)   ===>  The Master Carpenter
  MCP (Connectors)    ===>  Tools & Equipment (Saws, Drills, Adhesives)
  Skills (Playbooks)  ===>  Blueprints & Processes (How to join, assemble, finish)

Chef Analogy:
  LLM (Claude Code)   ===>  The Executive Chef
  MCP (Connectors)    ===>  Kitchen Hardware (Knives, Woks, Ovens)
  Skills (Playbooks)  ===>  The Recipe & Plating Standard

```

An LLM alone resembles a master craftsman locked in an empty room. It possesses deep domain knowledge but lacks physical hands (tools) and institutional procedures (recipes).

* **Model Context Protocol (MCP)** acts as the power tools. It supplies the agent with the external capability to read a web page, query an internal database, or trigger a deployment script.

* **Skills** serve as the strict standard operating procedures. They dictate how the agent approaches a problem: requiring it to formulate requirements, write a specification document, establish a test harness, and obtain human sign-off before modifying files.

### The MCP Client-Server Architecture

The Model Context Protocol mimics standard client-server communication architectures, such as HTTP:

```
┌─────────────────────────┐                     ┌─────────────────────────┐
│       MCP CLIENT        │                     │       MCP SERVER        │
│                         │  JSON-RPC Protocol  │                         │
│  • Claude Code CLI      ├────────────────────►│  • Local SQLite Store   │
│  • Claude Desktop       │◄────────────────────┤  • Firecrawl Scraper    │
│  • Codex Environment    │                     │  • Custom REST Wrapper  │
└─────────────────────────┘                     └─────────────────────────┘

```

* **The MCP Client:** The LLM client (such as Claude Code or Claude Desktop) initiates requests, discovers exposed tools, and consumes returned data.

* **The MCP Server:** A lightweight service exposing explicit capabilities via tool decorators (e.g., `@mcp.tool`) along with schema definitions and rich docstrings.

* **Universal Standard:** Just as USB-C unified peripheral connectivity for personal computing hardware, MCP establishes a universal, model-agnostic contract for AI agents communicating with disparate software APIs.

---

## Engineering Life Cycle: Dileep's 8-Stage SDLC

Rather than writing code immediately upon receiving a feature request, professional agentic workflows adhere to a multi-stage software development life cycle (SDLC):

```
[ 1. Idea ] ──► [ 2. Plan ] ──► [ 3. User Stories ] ──► [ 4. Agents ]
                                                              │
[ 8. Deploy ] ◄── [ 7. Review ] ◄── [ 6. Test Cases ] ◄── [ 5. Execution ]

```

1. **Idea Formulation:** Defining the core thesis, value proposition, and user experience targets.
2. **System Planning:** Specifying technical stack constraints, business rules, API boundaries, database schemas, and explicit design system primitives.
3. **User Stories:** Deconstructing architectural designs into atomic, testable, and dependency-mapped functional units.
4. **Agent Orchestration:** Selecting which agents, subagents, and skills will govern respective phases of the development pipeline.
5. **Execution:** Writing the actual business logic, configuration files, and components sequentially in atomic Git commits.
6. **Test Harness & Verification:** Executing automated validation suites and inspecting vertical code slices.
7. **Architectural Review:** Performing deep static analysis, security checks, and code reviews across touched files.
8. **Deployment:** Packaging and pushing the application to hosting providers (such as Vercel, Netlify, or local containers).

---

## Hands-on Tutorial: Environment Setup & Skill Installation

### 1. Initializing Claude Code CLI

Claude Code is most effective when executed directly within a pure terminal interface. Terminal execution is lightweight, supports multiple parallel instances across sessions, and integrates seamlessly with local developer toolchains.

Launch a terminal session inside an empty workspace directory containing only your environment configuration file (`.env`):

```bash
# Navigate to the target project directory
cd ~/Projects/ai-accelerator-council

# Launch Claude Code
claude

```

> **Practitioner Tip:** For automated demonstration environments or non-interactive build runs, developers can pass `--dangerously-skip-permissions`. This bypasses interactive human-approval prompts for terminal commands and file modifications. **Caution:** Avoid this flag in production codebases or when working with unverified scripts.

```bash
claude --dangerously-skip-permissions

```

### 2. Monitoring Context Health & Usage

Large-scale agentic builds quickly saturate LLM context windows. You can monitor your current session consumption using the `/usage` command directly within the Claude Code prompt:

```text
> /usage
Current Session: 42,300 tokens (21% of window)
Weekly Allocation: 2% of overall quota consumed
Model: Claude 3.7 Sonnet

```

* **The 60% Rule:** Aim to keep the working context under 60% capacity. Exceeding 60% significantly increases the probability of hallucination, overlooked edge cases, and degraded instruction-following.

* **In-Flight Visibility (`/btw`):** If Claude Code is executing a long-running subagent loop in the background, use the `/btw` ("by the way") command to probe current progress without interrupting execution:

```text
> /btw what is the current build status?
Phase 1 Foundation: Verified and committed.
Wave 2 Scaffolding: Creating API router models.
Wall time elapsed: 18 minutes.

```

### 3. Installing MCP Servers via Claude Code

Installing an MCP connector does not require manual configuration editing. Claude Code can parse documentation repositories and register the connector automatically.

```text
# Example: Installing Firecrawl for web data extraction
> Install the Firecrawl MCP server using https://github.com/mendableai/firecrawl-mcp-server

```

Claude Code triggers an internal configuration tool, analyzes the remote documentation, prompts for the necessary API key (or links it from `.env`), registers the server under its global or project scope, and updates the local configuration.

### 4. Evaluating and Installing Community Skills

Before adding community skills to your workspace, evaluate their stability and ecosystem health using three key criteria:

* **Fork Count:** High fork counts (e.g., 5,000+) indicate widespread practical use and community extensions.

* **Stars:** Strong validation signals (e.g., 60,000+ stars) reflect active adoption across diverse development teams.

* **Maintenance Velocity:** Confirm the repository has received updates within recent days or hours to prevent compatibility breaks.

To install core community playbooks, provide the repository URL to Claude Code:

```text
# Installing GSD (Get Shit Done) Build
> Install the skill from https://github.com/gsd-build/gsd-build

# Installing Superpowers
> Install the skill from https://github.com/superpowers-dev/superpowers

```

Claude registers these files inside the global configuration directory: `~/.claude/skills/<skill-name>/skill.md`.

---

## Creating a Custom Meta-Skill: The "Blueprint" Playbook

A key technique demonstrated by Dileep Karri is synthesizing complementary skills into a single custom meta-skill.

While **Superpowers** provides rigorous brainstorming, user-intent validation, and architectural design capabilities, **GSD Build** excels at spec-driven, deterministic execution loops. Karri directed Claude Code to create a hybrid meta-skill named **Blueprint** that combines the strengths of both frameworks.

```
┌────────────────────────────────────────────────────────────────────────┐
│                        BLUEPRINT HYBRID SKILL                          │
│                                                                        │
│  [ Stage 1: Superpowers Engine ]                                       │
│    • Interactive Brainstorming & Requirements Extraction               │
│    • Visual & System Design Specification                              │
│    • Design System Quality Gate (Hard Block)                           │
│                     │                                                  │
│                     ▼ (Handoff via `specs/design-spec.md`)             │
│                                                                        │
│  [ Stage 2: GSD Build Engine ]                                         │
│    • Phase Breakdown (`roadmap.md`)                                    │
│    • Sequential Wave Execution with Fresh Subagents                    │
│    • Automated Verification & Atomic Git Commits                       │
└────────────────────────────────────────────────────────────────────────┘

```

### Tutorial: Orchestrating the Hybrid Skill Synthesis

1. **Invoke Skill Synthesis:** Prompt Claude Code to design the meta-orchestrator:

   ```text
   > I want to build a completely new skill from scratch called "Blueprint"
   that combines Superpowers and GSD Build. Superpowers must handle
   brainstorming, technical specs, and design systems. GSD Build must
   execute the implementation phase-by-phase. Create a design system
   gate that prevents any code execution until visual tokens and
   architecture specs are fully approved.
   ```

1. **Select the Integration Architecture:** Claude Code presents three structural approaches:

   * *Option 1:* Superpowers generates specifications, followed by an interactive `gsd new-project` invocation.
   * *Option 2:* Superpowers handles design and planning; GSD strictly executes code.
   * *Option 3 (Hybrid Spec Pipeline):* Superpowers conducts brainstorming, generates a comprehensive specification document, and drops it into GSD's native planning path with automated gate validation.
   * *Selection:* Choose Option 3 for end-to-end automation.
1. **Enforce the Design System Gate:** Configure a strict rule requiring that before any functional code is scaffolded, UI tokens (colors, typography, spacing, border radii) must be committed to the project repository.
1. **Verification:** Confirm the skill is registered locally:

   ```text
   > Check if blueprint is active.
   ✓ Blueprint is registered and discoverable under ~/.claude/skills/blueprint/skill.md
   ```

---

## Building Custom MCP Servers with Python and FastMCP

When third-party MCP connectors are unavailable, proprietary APIs or legacy systems can be integrated into Claude Code using the open-source **FastMCP** Python library. FastMCP wraps standard REST endpoints in an MCP-compliant interface.

```
┌─────────────────┐       ┌─────────────────┐       ┌─────────────────┐
│ Claude Code CLI │◄─────►│ FastMCP Server  │◄─────►│ External REST   │
│   (MCP Client)  │       │   (Python App)  │       │ API (e.g.Adzuna)│
└─────────────────┘       └────────┬────────┘       └─────────────────┘
                                   │
                                   ▼
                          ┌─────────────────┐
                          │  Local SQLite   │
                          │     Cache       │
                          └─────────────────┘

```

### Complete Implementation Example: Job Search MCP Server

The following script, based on Karri's Adzuna job search implementation, demonstrates how to structure tools, manage local SQLite caching for network resilience, and provide documentation for the LLM context window:

```python
"""
Job Search MCP Server
Built using FastMCP for the AI Engineering Accelerator.
Exposes job query tools to Claude Code via an Adzuna API wrapper with local caching.
"""

import os
import sqlite3
import requests
from dotenv import load_dotenv
from fastmcp import FastMCP

# Load secrets from local .env
load_dotenv()

ADZUNA_APP_ID = os.getenv("ADZUNA_APP_ID")
ADZUNA_APP_KEY = os.getenv("ADZUNA_APP_KEY")
ADZUNA_BASE_URL = "https://api.adzuna.com/v1/api/jobs/us/search/1"

# Initialize the FastMCP Server instance
mcp = FastMCP("Job Search Server")

def init_db():
    """Initializes local SQLite database for query caching and resilience."""
    conn = sqlite3.connect("jobs_cache.db")
    cursor = conn.cursor()
    cursor.execute("""
        CREATE TABLE IF NOT EXISTS jobs (
            id TEXT PRIMARY KEY,
            title TEXT,
            company TEXT,
            location TEXT,
            salary_min REAL,
            description TEXT
        )
    """)
    conn.commit()
    conn.close()

init_db()

@mcp.tool()
def search_jobs(query: str, location: str = "remote") -> str:
    """
    Search for active software engineering jobs based on query keywords and location.
    NOTE FOR LLM: Use this tool whenever the user requests market salary data,
    hiring trends, or open developer roles.
    """
    params = {
        "app_id": ADZUNA_APP_ID,
        "app_key": ADZUNA_APP_KEY,
        "what": query,
        "where": location,
        "content-type": "application/json"
    }

    try:
        response = requests.get(ADZUNA_BASE_URL, params=params, timeout=10)
        response.raise_for_status()
        data = response.json()

        results = []
        conn = sqlite3.connect("jobs_cache.db")
        cursor = conn.cursor()

        for item in data.get("results", [])[:5]:
            job_id = str(item.get("id"))
            title = item.get("title", "N/A")
            company = item.get("company", {}).get("display_name", "N/A")
            loc = item.get("location", {}).get("display_name", "N/A")
            salary = item.get("salary_min", 0.0)
            desc = item.get("description", "")[:200]

            # Upsert into local SQLite cache
            cursor.execute("""
                INSERT OR REPLACE INTO jobs (id, title, company, location, salary_min, description)
                VALUES (?, ?, ?, ?, ?, ?)
            """, (job_id, title, company, loc, salary, desc))

            results.append(f"• ID: {job_id} | {title} at {company} ({loc}) ~ ${salary:,.2f}")

        conn.commit()
        conn.close()

        return "\n".join(results) if results else "No jobs found matching criteria."

    except Exception as exc:
        return f"Error querying job API: {str(exc)}. Falling back to cached local records."

@mcp.tool()
def get_job_by_id(job_id: str) -> str:
    """Retrieve complete description and requirements for a cached job by ID."""
    conn = sqlite3.connect("jobs_cache.db")
    cursor = conn.cursor()
    cursor.execute("SELECT title, company, description FROM jobs WHERE id = ?", (job_id,))
    row = cursor.fetchone()
    conn.close()

    if row:
        return f"Title: {row[0]}\nCompany: {row[1]}\nDescription: {row[2]}"
    return "Job ID not found in local cache."

if __name__ == "__main__":
    # Automatically registers server with local Claude Desktop / Claude Code configurations
    mcp.run()

```

### Critical Architectural Nuance: Docstrings as Prompts

Notice the explicit documentation provided directly below `@mcp.tool()`:

```python
"""
Search for active software engineering jobs based on query keywords and location.
NOTE FOR LLM: Use this tool whenever the user requests market salary data...
"""

```

In FastMCP, **the docstring is not merely documentation for human engineers; it is compiled into context schema loaded by the LLM**. When the agent plans tool execution, it parses these docstrings to decide when and how to invoke each function. Vague or omitted docstrings frequently cause tools to be skipped or misapplied.

---

## Project Walkthrough: Building the "Model Council" Application

To demonstrate the full power of this setup, Karri orchestrated the creation of a **Model Council** web application—recreating the multi-agent consensus feature offered by Perplexity Pro ($200/month tier).

```
┌────────────────────────────────────────────────────────────────────────┐
│                       MODEL COUNCIL PIPELINE                           │
│                                                                        │
│  [ Stage 1: Independent Attack (Parallel Fan-Out) ]                    │
│    • Claude 3.7 Sonnet ──► Independent Position                        │
│    • GPT-5              ──► Independent Position                       │
│    • Gemini 3.1 Pro     ──► Independent Position                       │
│    • Grok 4.2           ──► Independent Position                       │
│                                                                        │
│  [ Stage 2: Peer Critique & Debate Pass ]                              │
│    • Models ingest all peer answers                                    │
│    • Each model highlights points of agreement and disagreement        │
│                                                                        │
│  [ Stage 3: Synthesis & Disagreement Mapping ]                         │
│    • Synthesizer (Claude Opus) analyzes full debate matrix             │
│    • Generates: Multi-format Output (Tables, Prose, Bullets)           │
│    • Generates: Explicit "Disagreement Map"                            │
└────────────────────────────────────────────────────────────────────────┘

```

### Step 1: Specifying the Problem and Architecture

The user invokes the newly synthesized Blueprint meta-skill to initiate project planning:

```text
> /blueprint I want to build a local Model Council web application inspired by
Perplexity's multi-model consensus feature.

Architecture specifications:
1. Stage 1 (Independent Attack): Four distinct LLMs (Claude Sonnet, GPT-5,
   Gemini 3.1 Pro, Grok 4.2) must attack a query in parallel without seeing
   peer responses.
2. Stage 2 (Peer Critique): All responses are distributed across the models.
   Each model critiques peer arguments, highlighting agreements and flaws.
3. Stage 3 (Synthesis): Claude Opus acts as a synthesizer to produce an
   organized output featuring a comparison table, executive prose, bullet summaries,
   and a disagreement map.
4. Model Access: Route all model calls via OpenRouter API using OPENROUTER_API_KEY
   stored in .env.

```

### Step 2: Injecting the Visual Design System

Before allowing the agent to proceed to code generation, Dileep Karri integrated design specifications from **superdesign.dev**. Providing an explicit UI token schema ensures clean, consistent frontend interfaces without requiring manual CSS revisions.

```text
> Incorporate this design system prompt into the project specifications:
Style: Minimalist, clinical-white layout.
Tokens: High-contrast typography, slate borders (#E2E8F0), crisp whitespace,
subtle drop shadows, semantic badge components for model identity cards.
Accessibility: WCAG AA compliant contrast ratios throughout.

```

Claude Code ingested the styling rules, generated a design system specification document under `specs/design-tokens.json`, and passed the gate check into GSD Build's execution pipeline.

### Step 3: Automated Execution and Wave Scaffolding

With the specification and design tokens approved, GSD Build orchestrated execution across sequential waves:

* **Wave 1:** Repository scaffolding, environment variable parsing, and design token integration.

* **Wave 2:** OpenRouter API client implementation with streaming support for multi-model fan-out.

* **Wave 3:** Peer critique matrix and conversational exchange logic.

* **Wave 4:** Synthesis engine, layout primitives, and export formatters.

Throughout this phase, Claude Code generated code, executed shell commands, resolved package dependencies, and committed code to Git atomically without requiring manual code entry.

---

## Production Realities: Token Economics, Handoffs, and Codex Integration

While agentic frameworks streamline development, production workflows frequently encounter token and context exhaustion limits.

### Understanding the Token Wall

During the live demonstration, extensive subagent planning and multi-stage code execution consumed significant context, prompting an alert:

```text
> /usage
Warning: 96% of active session limit reached. Session resets at 10:20 PM.

```

Subagent loops consume context rapidly:

* **65%** of overall token consumption stemmed from nested subagent research tasks.

* **32%** occurred while running with broad context windows (>150k tokens).

* Running parallel sessions compound usage quotas quickly.

### The Codex Handoff Strategy

Rather than halting work until token limits reset, Karri demonstrated a cross-platform handoff strategy: **Claude Code for System Architecture and Design; Codex (Cursor) for Deep Implementation**.

```
┌─────────────────────────────────────────────────────────────┐
│                    CLAUDE CODE CLI                          │
│  • High-level system architecture & specification           │
│  • Design system token generation                           │
│  • Phase 1 Foundation build                                 │
└──────────────────────────────┬──────────────────────────────┘
                               │
                               ▼ (Session Limit: 96% Hit)
┌─────────────────────────────────────────────────────────────┐
│                    THE HANDOFF SEAM                         │
│  • Shared Local Git Repository                              │
│  • Shared `.env` & Configuration Files                      │
│  • Structured Spec Files (`specs/`, `roadmap.md`)           │
└──────────────────────────────┬──────────────────────────────┘
                               │
                               ▼
┌─────────────────────────────────────────────────────────────┐
│                     CODEX (CURSOR)                          │
│  • Ingests existing codebase and git history                │
│  • Validates Phase 1 clean state                            │
│  • Executes Phase 2 additively without breaking changes     │
└─────────────────────────────────────────────────────────────┘

```

#### Step-by-Step Handoff Execution

1. **Preserve Current State:** Instruct Claude Code to commit all working changes and write a comprehensive handoff document (`handoff.md` or `status.md`) detailing completed tasks and pending phases.
2. **Open Workspace in Codex:** Launch Codex (Cursor) and point it directly to the shared local project folder.
3. **Execute Non-Destructive Ingestion:** Prompt Codex to review the existing environment without altering working files:

   ```text
   > Inspect this project directory and review git commits along with roadmap.md.
   Claude Code completed the Phase 1 Foundation cleanly. Determine what remains
   to be built in Phase 2. Confirm that you can proceed additively without
   overwriting or breaking Claude Code's foundation.
   ```

4. **Codex Analysis and Verification:** Codex verifies that the core models, token schemas, and OpenRouter API wrappers are functioning correctly. It notes any minor discrepancies between planning docs and actual code, confirms it will build additively, and continues scaffolding subsequent phases without disruption.

### Mitigating Token Consumption: Claude Mem

To minimize token churn across recurring sessions, Karri recommends installing **Claude Mem**.

* **How It Works:** Claude Mem operates as a background observer that records interactions, architectural decisions, and error resolutions into a lightweight, local SQLite database.

* **Session Persistence:** When a new session begins, Claude Mem automatically injects a condensed log of past decisions into the context window. Rather than re-reading the entire file tree to discover project state, the agent queries this local history, cutting token usage and preventing context loss.

---

## The Engineering Mindset: Surfing the AI Disruption Wave

Beyond technical mechanics, Dileep Karri addressed the broader implications of AI adoption for engineering organizations, highlighting ongoing debates around tech layoffs and organizational restructuring.

```
                     AI ADOPTION IN ENGINEERING
                                  │
         ┌────────────────────────┴────────────────────────┐
         ▼                                                 ▼
[ The Contraction Trap ]                         [ The Expansion Mindset ]
  • "Reduce 10 engineers to 5"                     • "Equip 10 engineers to achieve 5x"
  • Constrained vision                             • Multiplied organizational ambition
  • Treating code as the ultimate output           • Treating customer outcomes as the goal

```

### Ambition Multipliers vs. Headcount Reduction

Corporate leadership often views AI tools narrowly as cost-reduction mechanisms—attempting to cut a 10-engineer team down to 5 while maintaining identical output targets. Karri argues this perspective misinterprets the competitive landscape:

* **First Principles of Hiring:** Engineering teams are hired to achieve business scope within budget constraints.

* **The Ambition Shift:** When engineering throughput increases by 5x or 10x, competitive organizations do not reduce headcount; **they scale their technical ambition**. They tackle complex backlogs, build advanced features, and target challenging market opportunities that were previously cost-prohibitive.

* **Outcomes Over Inputs:** Writing code is an input; building features is an output; **delivering customer value and business impact is the true objective**.

### Experience as an Asset vs. Experience as a Tax

Karri highlighted an insightful perspective on industry experience: **Domain experience without AI upskilling becomes an anchor; combined with AI leverage, it becomes an unbeatable competitive advantage**.

```
High Experience  +  Zero AI Adoption  ===>  Organizational Tax (Fragility)
High Experience  +  Active AI Fluency ===>  High-Leverage Engineering Leadership

```

Senior practitioners who master agentic orchestration can apply years of architectural judgment, security intuition, and system design expertise at unprecedented speed. Conversely, developers who avoid modern tooling risk turning their hard-won experience into an operational handicap.

### The Surfer Metaphor

Karri closed with an evocative metaphor comparing AI adoption to ocean surfing:

* **The Shore-Waiters:** Individuals who wait at the shoreline, hesitating until corporate IT departments mandate change, company guidelines are finalized, or toolchains become risk-free.

* **The Surfers:** Practitioners who venture into open water, actively experiment with new tooling, ride the wave of disruption, and develop the taste and judgment required to lead modern teams.

---

## Resources, References & Community Links

All core references, tools, repositories, and materials from the Day 11 session are compiled below with operational context:

### Session Materials

* **[Post-Read Session Summary (Google Docs)](https://docs.google.com/document/d/1d7CrO4wuOeK-KiUBZUGrAuJsh0bjhxIlJnuY-03U-Uo/edit?usp=sharing):** Official post-session companion notes, tool links, and follow-up guidance compiled by Dileep Karri and Sham Kulkarni for the AI Engineering Accelerator\[cite: 1].
* **[Session Whiteboard & Diagrams (Google Drive)](https://www.google.com/search?q=https://drive.google.com/drive/folders/1Qkt_-slsMX5dqnbIAHr0sKYEtEDK0ntA%3Fusp%3Dsharing):** Image exports of the session whiteboards, illustrating MCP client-server relationships, skills composition layers, and the Model Council pipeline.

### Tools, Ecosystems & Registries

* **[MCP Market (mcpmarket.com)](https://mcpmarket.com):** Central registry and discovery hub for Model Context Protocol servers across database connectors, productivity apps, and web utilities.

* **[Firecrawl (firecrawl.dev)](https://firecrawl.dev):** High-performance web crawling and scraping engine designed specifically for LLMs and agentic pipelines.

* **[Superdesign (superdesign.dev)](https://superdesign.dev):** Prompt-driven design system generator used to produce structured, accessible UI tokens for agent-built web applications.

* **[FastMCP Documentation](https://github.com/jlowin/fastmcp):** The standard Python framework for building custom Model Context Protocol servers via high-level decorators.

* **[OpenRouter (openrouter.ai)](https://openrouter.ai):** Unified model aggregation API providing flexible, cost-effective access to state-of-the-art LLMs (Claude Sonnet/Opus, GPT-5, Gemini Pro, Grok, Kimi).

* **[GSD Build Framework](https://www.google.com/url?sa=E\&source=gmail\&q=https://github.com/gsd-build/gsd-build):** Spec-driven development framework enforcing a deterministic six-step execution loop for Claude Code.

* **[Superpowers Repository](https://www.google.com/url?sa=E\&source=gmail\&q=https://github.com/superpowers-dev/superpowers):** Agentic playbook providing structured brainstorming, architectural spec generation, and debugging capabilities.

---

## Troubleshooting Guide & FAQ

### Frequently Asked Questions

**Q: Can I run this entire orchestration pipeline using local open-weight models?**

While high-parameter local coding models (such as Qwen 2.5 Coder 32B) can execute basic coding tasks, they generally lack the complex reasoning, instruction adherence, and tool-calling reliability needed for long-running, multi-phase orchestration pipelines. SOTA frontier models remain essential for reliable spec generation and multi-agent synthesis.

**Q: Is the Claude Max subscription strictly necessary, or is the Pro tier sufficient?**

While the Pro tier supports basic coding queries, building multi-phase applications end-to-end quickly exhausts Pro context quotas. For continuous agentic builds, an enterprise plan or the Anthropic Claude Max tier ($100/month) is strongly recommended.

**Q: How do I prevent an agent from making breaking changes to existing dependencies?**

Avoid directing the agent to refactor entire modules at once. Instead, enforce strict phase-based execution gates: require your playbook to build additively, verify individual code slices, run automated test suites, and commit changes atomically to Git after each step.

### Common Debugging Scenarios

```
Scenario: Claude Code encounters a runtime or browser rendering error.
Resolution:
  1. Capture a clean screenshot of the terminal stack trace or browser error[cite: 1, 2].
  2. Drop the image directly into the Claude Code prompt[cite: 1, 2].
  3. Claude Code uses visual reasoning to inspect layout rendering, parse stack traces,
     and automatically apply targeted cache or logic fixes[cite: 1, 2].

Scenario: Subagents get stuck in circular reasoning or infinite tool loops.
Resolution:
  1. Run `/btw` to inspect current execution status and identify stuck processes[cite: 2].
  2. Exit the active process gracefully (`Ctrl+C`)[cite: 2].
  3. Review `status.md` or git diffs, refine requirements in the spec doc,
     and resume execution with clearer guardrails[cite: 2].

Scenario: The project hits an unexpected session context limit mid-build.
Resolution:
  1. Do not panic or delete your workspace[cite: 2].
  2. Open the existing workspace folder in Codex (Cursor)[cite: 1, 2].
  3. Ask Codex to inspect the repository history and roadmap to continue building additively[cite: 1, 2].

```

By pairing systematic planning with robust orchestration tools—combining Claude Code CLI, the Model Context Protocol, modular skills, and cross-IDE handoffs—engineers can evolve from manual coders into high-leverage software architects, directing autonomous agent fleets with precision and scale.
