# From Vibe Coding to Agentic Coding: Orchestrating Autonomous AI Agents with Windsurf, Devin, and Claude Code

## Executive Summary

Software development with generative AI is undergoing a fundamental paradigm shift: the transition from conversational **vibe coding** to specification-driven **agentic coding**<sup></sup>.

In the initial wave of AI development, non-technical creators and developers embraced vibe coding platforms like Bolt, Lovable, v0, and Emergent<sup></sup>. In this modality, building is an informal dialogue: a user describes a feature, an AI generates code, and the user responds with iterative conversational tweaks<sup></sup>. While effective for rapid prototyping and weekend hackathons, vibe-coded applications notoriously collapse when released to real-world users<sup></sup>. Fragile state management, overlooked edge cases, unhandled rate limits, and missing error-handling hierarchies cause apps that "worked on my machine" to fail in the wild<sup></sup>.

Agentic coding replaces informal conversation with formal engineering specifications<sup></sup>. Instead of chatting loosely with a model, developers write comprehensive functional specifications, architectural definitions, and multi-phase execution plans<sup></sup>. Autonomous agent swarms inside local AI-native Integrated Development Environments (IDEs) like **Windsurf** and cloud-native software engineering agents like **Devin** then ingest these plans, execute multi-file changes, write tests, manage Git branches, and submit pull requests<sup></sup>.

```
┌────────────────────────────────────────────────────────┐
│                      VIBE CODING                       │
│  "Grandma's Recipe" • Informal Chat • Rapid Prototype  │
│  (Bolt, Lovable, Emergent)                             │
└───────────────────────────┬────────────────────────────┘
                            │  Evolution via
                            │  Engineering Discipline
                            ▼
┌────────────────────────────────────────────────────────┐
│                     AGENTIC CODING                     │
│  "MasterChef Spec" • Git-Backed • Multi-Agent Swarms   │
│  (Windsurf, Devin, Claude Code, Cursor)                │
└────────────────────────────────────────────────────────┘
```

This guide details the complete mental framework, engineering principles, and hands-on tooling required to operate as an agentic engineer<sup></sup>. It covers version control fundamentals using intuitive mental models, outlines the four mandatory engineering pillars of resilient specifications, contrasts local IDEs against cloud-based autonomous agents, and walks through two real-world tutorials: building **Superplexity** (an elevated, high-signal search engine) from scratch and performing autonomous repository refactoring with **Devin**<sup></sup>.

## Tools Required

The agentic software development lifecycle combines specialized environments for planning, local authoring, and background execution<sup></sup>.

| **Tool / Platform** | **Category** | **Primary Function in Agentic Workflow** | **Access / Pricing Tier Notes** |
| ------------------- | ------------ | ---------------------------------------- | ------------------------------- |
| **Windsurf 2.0 (Devin Desktop)** | AI-Native IDE | Local development environment; houses the **Cascade** agent for file-level edits, terminal automation, and sub-agent swarms<sup></sup>. | Free tier includes Kimi K2.6; paid tiers unlock Adaptive Routing and frontier models<sup></sup>. |
| **Devin (Cognition)** | Autonomous Software Engineer | Cloud-based software agent executing in an isolated sandbox; ideal for legacy code migrations, audits, and async PRs<sup></sup>. | Enterprise / usage-based paid tier; runs independently even when your laptop is closed<sup></sup>. |
| **Claude Code (Anthropic)** | CLI & Agent Companion | Used within Windsurf as a planning engine; excels at structured interviews, architecture breakdown, and spec generation<sup></sup>. | Requires Anthropic API Key; token-intensive for end-to-end builds<sup></sup>. |
| **Obra Superpowers** | Claude Code Plugin Framework | Extends Claude Code with structured skills like `brainstorming`, `writing-plans`, and sub-agent reviews<sup></sup>. | Open-source GitHub repository (`obra/superpowers`)<sup></sup>. |
| **OpenRouter** | LLM Gateway & API Proxy | Unified access point to bypass single-provider rate limits; routes to models like Perplexity Sonar and Claude Sonnet<sup></sup>. | Pay-per-token API key managed via `.env` files<sup></sup>. |
| **Apify** | Web Scraping Platform | Cloud scraping actor used for on-demand Twitter/X timeline extraction and high-signal blog parsing<sup></sup>. | Freemium / usage-based API key<sup></sup>. |
| **GitHub** | Version Control & CI/CD | Manages branches, commits, PR reviews, and automated verification checks for both human and agent code<sup></sup>. | Free / Pro accounts; integrates natively with Cascade and Devin<sup></sup>. |
| **Styles Referrer (`styles.referrer.design`)** | UI Design System Source | Repository of structured UI design tokens and component specs downloadable as a `design.md` contract<sup></sup>. | Free public web utility<sup></sup>. |
| **Wispr Flow** | Voice Dictation Tool | High-speed, natural language voice-to-text dictation used to write comprehensive prompts without typing fatigue<sup></sup>. | Desktop utility for prompt input<sup></sup>. |

## The Core Thesis: Vibe Coding vs. Agentic Coding

The distinction between vibe coding and agentic coding is not merely aesthetic; it is an architectural divide between exploratory conversation and rigid specification<sup></sup>.

```
VIBE CODING:    [Human] ──(Informal Chat)──► [Single AI] ──► [Patchy Codebase]
                                                                    │
                                                             (Fails in Prod)

AGENTIC CODING: [Human] ──► [Structured Spec] ──► [Agent Swarm] ──► [Production Build]
                                   │                    │
                             (Edge Cases,          (Git Branches,
                             Rate Limits,           Sandboxed PRs,
                             Error Trees)           Automated Tests)
```

### The Culinary Spectrum: Grandma’s Recipe vs. MasterChef Precision

To grasp this conceptual leap, consider how food is prepared:

> **Grandma's Recipe (Vibe Coding):** Your grandmother never tells you to measure 1.5 teaspoons of salt or preheat an oven to 165°C for exactly 11 minutes<sup></sup>. She says: *"Take this much flour, add a pinch of this, and take it out when it smells ready"*<sup></sup>. Her technique relies on decades of tacit knowledge and intuition<sup></sup>. It is forgiving: minor measurement variations still yield a delicious meal<sup></sup>. However, it is **non-replicable**—someone else following her written notes will rarely produce the same dish<sup></sup>.
>
> **MasterChef Specification (Agentic Coding):** A pastry chef specifies: *"Add 0.25 tsp baking soda, 1 drop agar-agar, preheat convection oven to 150°C for 12 minutes, bake for 7 minutes until golden, then drizzle 5 ml honey"*<sup></sup>. Every variable is measured, bounded, and reproducible<sup></sup>. The tradeoff is **brittleness**: if you alter one parameter or miscalibrate the oven temperature, the entire dish collapses<sup></sup>.

When working with autonomous AI agents, you must operate like a MasterChef<sup></sup>. AI agents do not possess human intuition; if you provide loose, conversational prompts, the agent fills in the ambiguities with arbitrary assumptions<sup></sup>. When multiple agents work in a swarm across front-end, back-end, and database layers, conflicting assumptions create catastrophic runtime errors<sup></sup>.

### The Comparative Matrix

| **Dimension** | **Vibe Coding** | **Agentic Coding** |
| ------------- | --------------- | ------------------ |
| **Primary Interaction** | Conversational chat ("Make the header blue and add a login button")<sup></sup>. | Formal technical specifications (`spec.md`, `plan.md`, `design.md`)<sup></sup>. |
| **Execution Layer** | Single AI output stream directly modifying a live preview<sup></sup>. | Autonomous agent swarms operating across local files, terminals, and sandboxes<sup></sup>. |
| **Version Control** | Casual "checkpoints" or basic undo history<sup></sup>. | Strict Git version control: feature branches, atomic commits, pull requests<sup></sup>. |
| **Failure Tolerance** | High tolerance during prototyping; extreme fragility in production<sup></sup>. | Rigorous upfront failure planning (edge cases, rate limits, error fallbacks)<sup></sup>. |
| **Tool Ecosystem** | Bolt.new, Lovable.dev, Emergent, v0<sup></sup>. | Windsurf, Devin, Cursor, Claude Code, OpenRouter, GitHub<sup></sup>. |

## Git Fundamentals for Agentic Builders: The Pizza Kitchen Analogy

Autonomous agents regularly manipulate Git repositories—creating branches, executing commits, running tests, and opening pull requests<sup></sup>. You do not need to memorize complex CLI syntax, but you must understand the underlying mechanics to supervise your agents effectively<sup></sup>.

```
                     [MAIN BRANCH] (Customer-Facing Menu)
                           │
             ┌─────────────┼─────────────┐
             │ git branch  │ git branch  │ git branch
             ▼             ▼             ▼
        [BRANCH A1]   [BRANCH A2]   [BRANCH A3]
        (Dough/Crust) (Sauce Mix)   (Toppings)
             │             │             │
        (git commit)  (git commit)  (git commit)
             │             │             │
             │             ▼             │
             │      (Soggy Crust!        │
             │       Chef Rejects)       │
             ▼                           ▼
      [PULL REQUEST]              [PULL REQUEST]
             │                           │
             └─────────────┬─────────────┘
                           │ git merge
                           ▼
                     [MAIN UPDATED]
```

### The Scenario: A Kitchen Under Renovation

Imagine a restaurant famous for its curated menu of three signature pizzas<sup></sup>. The head chef wants to modernize the recipes with three sous chefs without disrupting daily service to paying customers<sup></sup>.

* **The Naive Approach (Direct Changes on `main`):** If Sous Chef 1 alters Step 3 (dough proofing), Sous Chef 2 alters Step 5 (sauce temperature), and Sous Chef 3 alters Step 7 (bake duration) directly in the master recipe binder without telling anyone, the kitchen collapses into chaos<sup></sup>. The resulting pizza is ruined, and customers leave<sup></sup>.
* **The Branch (`git branch`):** To prevent chaos, the head chef photocopies the master recipe binder and hands one copy to each sous chef: Copy A1, Copy A2, and Copy A3<sup></sup>. Each sous chef experiments exclusively on their copy<sup></sup>. The master recipe remains untouched, and customers continue receiving high-quality pizzas<sup></sup>.
* **The Pull Request (`git request-pull`):** Sous Chef 1 discovers that cold-fermenting the dough elevates the crust<sup></sup>. They cannot simply paste their notes into the master binder<sup></sup>. Instead, they submit a formal request to the head chef: *"I have modified Step 3. Please bake a test pie, evaluate the crust, and decide if this belongs on the permanent menu"*<sup></sup>.
* **The Review and Merge (`git merge`):** The head chef tastes the pizza<sup></sup>. If the crust is superior, the chef approves the change and merges the notes into the master binder<sup></sup>. If Sous Chef 2’s sauce variation makes the crust soggy, the chef rejects the pull request with actionable feedback: *"Good flavor profile, but excessive moisture softens the crust. Reduce liquid content and re-submit"*<sup></sup>.
* **The Commit (`git commit`):** A commit is a secure milestone checkpoint<sup></sup>. Think of saving your game at Level 4<sup></sup>. When you die on Level 5, you restart at Level 4 rather than Level 1<sup></sup>. Whenever an agent achieves a working feature, it locks in a commit<sup></sup>. If an experiment corrupts the codebase later, the agent rolls back to that clean snapshot<sup></sup>.
* **The Fork (`git fork`):** A fork occurs when a sous chef leaves the restaurant entirely to open their own pizzeria across town<sup></sup>. They take a copy of the original recipe binder, but changes made in their new restaurant do not flow back to the original kitchen<sup></sup>.

## Engineering 101: The Four Pillars of Resilient Specifications

When moving from beginner to practitioner, your specification must account for four structural concerns before a single line of code is generated<sup></sup>.

```
                     ┌───────────────────────────────┐
                     │     ROBUST SPECIFICATION      │
                     └───────────────┬───────────────┘
         ┌───────────────────┬───────┴───────────┬───────────────────┐
         ▼                   ▼                   ▼                   ▼
  1. EDGE CASES       2. AUTH & STATE      3. RATE LIMITS     4. ERROR TREES
  • Boundary checks   • Cinema ticket      • Dinner guest     • "Out of syllabus"
  • Device variances    analogy              rationing          fallbacks
  • Empty databases   • Layer last in build • Agent-friendly   • Graceful UX
                                             Markdown docs      degradation
```

### Pillar 1: Preemptive Edge Case Mapping

An edge case is any boundary condition where user behavior, network instability, or device variation causes software to fail<sup></sup>.

* **The Fine Line:** In baking, the boundary between a crispy charred crust and a burnt, bitter pie is 60 seconds<sup></sup>. In software, an edge case is the boundary where an otherwise valid input crashes an unhandled calculation<sup></sup>.
* **Hardware & Data Variations:** An app tested exclusively on an iPhone 15 Pro may crash on an iPhone SE due to viewport clipping or memory pressure<sup></sup>. Similarly, a search application designed to summarize articles will crash if the user queries a topic with zero indexed documents—unless an empty state handler is explicitly specified<sup></sup>.
* **Engineering Mandate:** Map failure modes in reverse<sup></sup>. Ask your planning agent: *"What are the six most likely ways this pipeline will fail at runtime?"* and mandate fallback behaviors in the spec<sup></sup>.

### Pillar 2: Authentication vs. Authorization

Many non-technical builders confuse these two identity concepts<sup></sup>:

> **The Cinema Analogy:**
>
> * **Authentication (AuthN):** You purchase a cinema ticket<sup></sup>. The usher at the entrance checks your ticket and verifies you are permitted inside the building<sup></sup>. In an application, authentication proves *who you are* (via email, OAuth, or session tokens)<sup></sup>.
> * **Authorization (AuthZ):** Once inside the cinema, your ticket indicates whether you sit in the general stalls, the premium balcony, or luxury sleeping pods<sup></sup>. An usher will stop a general ticket holder from entering the VIP lounge<sup></sup>. In software, authorization dictates *what you are allowed to access* (e.g., standard user vs. workspace admin)<sup></sup>.

* **Why Authentication Drives Personalization:** Authentication is not just a security barrier; it is the prerequisite for cross-device state<sup></sup>. If you read an e-book to page 27 on your phone, close it, and open it on your laptop, only a server-backed user identity can sync your position<sup></sup>. Relying on browser `localStorage` is fragile: if the cache clears or the browser crashes, user progress vanishes<sup></sup>.
* **The Builder's Golden Rule:** **Build authentication last**<sup></sup>. Implementing authentication early introduces login friction into every test iteration<sup></sup>. Build your core application mechanics unauthenticated, verify their stability, and layer Supabase or Replit Auth directly before user deployment<sup></sup>.

### Pillar 3: API Rate Limits and Agent-Friendly Docs

Applications fail when developers treat external APIs as infinite pipes<sup></sup>.

* **The Dinner Guest Analogy:** Imagine hosting fifteen unexpected dinner guests with only two pizzas available<sup></sup>. Your mother whispers a strict constraint: *"You get two slices maximum. Leave the rest for the guests"*<sup></sup>. You are rate-limited<sup></sup>.
* **Throttling in Production:** If your application integrates a voice API like ElevenLabs on a tier permitting 20 requests per second, and 100 users trigger voice generation at the same moment, 80 users will receive server 429 errors<sup></sup>. If your spec lacks backoff logic, retry queues, or graceful UI alerts, users assume your entire app is broken<sup></sup>.
* **The Rise of Agent-Friendly Documentation:** Modern API providers format their documentation for AI consumption rather than humans<sup></sup>. For example, ElevenLabs includes a "Copy Page for LLMs" feature, outputting structured Markdown that agents can parse directly to learn endpoints and rate limits without manual human translation<sup></sup>.

### Pillar 4: Error Handling Hierarchies

Error handling defines what your system does when an unexpected condition arises<sup></sup>.

* **The "Out-of-Syllabus" Exam:** When a student encounters a question on an exam covering a topic never taught in class, they freeze in panic<sup></sup>. An application without explicit error handling does the same: it freezes, yielding a blank screen or a silent crash<sup></sup>.
* **Layered Social Etiquette:** Consider how children are taught social rules:
  1. *Rule 1:* If a guest offers a treat, politely decline<sup></sup>.
  2. *Rule 2:* If the guest insists three times, accept with gratitude<sup></sup>.
  3. *Catch-All:* If a situation occurs that was not covered by a rule, immediately look at your parent<sup></sup>.
* **Application Error Trees:** In your specification, map explicit responses for known failure modes: invalid input format, external API timeouts, and empty database returns<sup></sup>. For all unidentified errors, implement a global error boundary that catches exceptions and renders a helpful, transparent fallback message rather than a broken UI<sup></sup>.

## Tooling Landscape: The Genealogy of AI-Native IDEs

Understanding how modern developer tools evolved helps you choose the right tool for each phase of a project<sup></sup>.

```
                         [Visual Studio Code] (Open Source Base)
                                    │
             ┌──────────────────────┼──────────────────────┐
             │                      │                      │
             ▼                      ▼                      ▼
         [Cursor]               [Windsurf]           [Trae / Kiro]
         (Anysphere)         (Codeium / Exafunction) (ByteDance / AWS)
                                    │
                     ┌──────────────┴──────────────┐
                     │ Google Acquires             │ Cognition Acquires
                     │ Core Team (8-10)            │ Remaining Team (70-80)
                     ▼                             ▼
             [Google Antigravity]          [Windsurf 2.0 / Devin Desktop]
                                                   │
                                           (Merges with Devin Cloud)
```

### The Lineage of IDEs

Microsoft’s open-source VS Code forms the architectural base for modern AI editors<sup></sup>.

1. **Cursor** forked VS Code to embed deep agentic chat and inline code editing (Composer)<sup></sup>.
2. **Windsurf** was created by Codeium (Exafunction) as a competing agentic IDE<sup></sup>.
3. **The Google & Cognition Split:** In mid-2025, Google executed a major licensing deal, acquiring Windsurf's core leadership team (8–10 people) to develop Google's internal **Antigravity** IDE<sup></sup>. The remaining 70–80 engineers continued building the standalone editor, which was subsequently acquired by **Cognition**—the creators of **Devin**<sup></sup>. Windsurf was integrated with Devin's technology, evolving into **Windsurf 2.0 (Devin Desktop)**<sup></sup>.

### Local AI IDE vs. Cloud Autonomous Agent

Practitioners must not confuse an AI-enhanced IDE with an autonomous software agent<sup></sup>:

* **Windsurf (Local AI IDE):** Operates on your physical workstation<sup></sup>. The code lives in your local directories, and the **Cascade** agent assists you interactively<sup></sup>. If you close your laptop lid, Windsurf halts immediately<sup></sup>.
* **Devin (Cloud Autonomous Engineer):** Operates inside a secure, ephemeral cloud sandbox container<sup></sup>. Devin clones your GitHub repository, sets up runtime dependencies, reproduces bugs, writes tests, runs linters, and issues pull requests completely out-of-band<sup></sup>. If you close your laptop and walk away, Devin continues working on the cloud server<sup></sup>.

```
┌──────────────────────────────────────┐  ┌──────────────────────────────────────┐
│       WINDSURF (Local AI IDE)        │  │     DEVIN (Cloud-Native Agent)       │
├──────────────────────────────────────┤  ├──────────────────────────────────────┤
│ • Runs locally on your machine       │  │ • Runs in an isolated cloud container│
│ • Edits active, open project files   │  │ • Clones Git repo into remote sandbox│
│ • Shuts down when laptop closes      │  │ • Runs 24/7 background tasks         │
│ • Best for: New features, rapid      │  │ • Best for: Legacy migrations,       │
│   greenfield development             │  │   code reviews, autonomous bug fixes │
└──────────────────────────────────────┘  └──────────────────────────────────────┘
```

> **The Founder Pedigree:** Devin’s specialized software engineering performance stems from its architectural design by Cognition founder **Scott Wu**<sup></sup>. Wu is a competitive programming prodigy who earned three gold medals at the International Olympiad in Informatics (IOI), won the ICPC World Championship at Harvard, and attained the Legendary Grandmaster rank on Codeforces<sup></sup>. Devin's workflow mirrors the rigorous problem-decomposition, test-driven validation, and terminal-level troubleshooting practiced by elite competitive programmers<sup></sup>.

## Step-by-Step Tutorial 1: Building "Superplexity" with Claude Code and Windsurf

This tutorial walks through building **Superplexity**—an elevated AI research workspace designed to bypass SEO-heavy web content by targeting high-signal sources like Substack newsletters, Beehiiv blogs, and curated Twitter/X accounts<sup></sup>.

```
                          SUPERPLEXITY SYSTEM PIPELINE

    User Query ──► [Next.js Web Interface]
                         │
                         ▼
             [Thin Routing Orchestrator]
                         │
       ┌─────────────────┼─────────────────┐
       ▼                 ▼                 ▼
[SQLite FTS5]     [Apify Actors]    [Perplexity Sonar]
Indexed Newsletters On-Demand Tweets Fallback Search
(Weight: 1.0)     (Weight: 0.7)     (Weight: 0.4)
       │                 │                 │
       └─────────────────┼─────────────────┘
                         │
                         ▼
        [Weighted Blend Scoring Engine]
                         │
                         ▼
      [OpenRouter: Claude 3.5/3.7 Sonnet]
           (Synthesizes with Citations)
```

### Step 1: Initialize Workspace and Git Repository

1. Launch Windsurf and select **Create New Project**<sup></sup>.
2. Create an empty directory on your machine named `superplexity`<sup></sup>.
3. Open Cascade (`Cmd + L` on macOS or `Ctrl + L` on Windows/Linux) to initiate an agentic session<sup></sup>.
4. Instruct Cascade to initialize version control:
   Cascade executes the necessary Git and GitHub CLI commands to configure your remote repository automatically<sup></sup>.

### Step 2: Configure the Model Stack

Windsurf provides several model options directly in the Cascade interface<sup></sup>:

* **Adaptive Routing:** Windsurf's automated routing mode that switches between models based on query complexity to balance performance and cost (roughly \$0.50 per 1M tokens)<sup></sup>.
* **SWE 1.6 Fast:** Windsurf's proprietary, low-latency in-house coding engine<sup></sup>.
* **Kimi K2.6:** A cost-effective, high-context model available on the free tier<sup></sup>.
* **Claude 3.5 / 3.7 Sonnet & Opus:** High-capability reasoning models for complex architectural tasks<sup></sup>.

*Recommendation:* Use **Adaptive** for routine code modifications and reserve **Claude Sonnet** or **Opus** for architecture planning and complex refactoring<sup></sup>.

```
Cascade Model Selection:
┌─────────────────────────────────────────────────────────┐
│ (*) Adaptive (Auto-balances quality & cost)             │
│ ( ) SWE 1.6 Fast (Proprietary low-latency coding model) │
│ ( ) Kimi K2.6 (Free tier coding & reasoning engine)     │
│ ( ) Claude 3.7 Sonnet (High-complexity tasks via API)   │
└─────────────────────────────────────────────────────────┘
```

### Step 3: Run the Architecture Interview via Claude Code & Superpowers

Instead of writing an ad-hoc prompt, use Claude Code equipped with the **Obra Superpowers** plugin to conduct a structured system interview<sup></sup>.

1. Install the Superpowers plugin inside Claude Code:
2. Launch the `brainstorming` skill and initiate the Superplexity design:
3. Answer the resulting interview questions to establish core architectural boundaries:
   * **Target Sources:** Focus on Substack, Beehiiv, curated domain blogs, and technical Twitter/X threads<sup></sup>.
   * **Twitter Ingestion:** Integrate Apify Twitter search actors rather than the costly enterprise X API (\$200+/month)<sup></sup>.
   * **Scraping Rule:** To prevent budget exhaustion, enforce **free background fetching for RSS feeds**, but restrict **paid Apify scraping to on-demand user queries only**<sup></sup>.
   * **Search Fallback:** Substitute fragile Google Custom Search APIs with Perplexity's native **Sonar** model via OpenRouter<sup></sup>.
   * **Source Weighting:** Implement a weighted blend scoring engine: Newsletters = `1.0`, Tweets = `0.7`, Sonar Web Fallback = `0.4`<sup></sup>.

> **The Non-Tech Circuit Breaker:** If your planning agent uses overly dense architectural jargon, paste this reset prompt:
>
> Plaintext
>
> ```
> I am a non-technical product builder. Pause the deep jargon, summarize our system architecture in plain English using real-world analogies, confirm whether we are on the right track, and identify where you need my direct decisions.
> ```
>
> The agent will reframe the technical stack into plain functional components and add a memory rule to maintain accessible communication<sup></sup>.

### Step 4: Establish the Visual Contract (`design.md`)

Prevent generic AI frontend styles by providing an explicit design specification<sup></sup>:

1. Navigate to `styles.referrer.design` and select an aesthetic design token set<sup></sup>.
2. Export the design tokens and save them as `design.md` in your project root<sup></sup>.
3. Direct Cascade to use this file for all UI components:

### Step 5: Convert the Specification into an Implementation Plan

A human-readable specification is not the same as an agent execution plan<sup></sup>:

* **`spec.md` (Design Doc):** Written in clear, unambiguous English for human review, outlining feature requirements, source weighting algorithms, and edge-case behaviors<sup></sup>.
* **`plan.md` (Implementation Plan):** A granular, phased, technical instruction set designed for sub-agent execution<sup></sup>.

Instruct the planning skill to generate both files in the `/docs` directory<sup></sup>:

Bash

```
docs/
├── superplexity-design-doc.md  # The functional and architectural contract
├── design.md                   # Visual tokens and UI components
└── plans/
    └── phase1-mvp-plan.md      # Phased work packages for Cascade agents
```

### Step 6: Execute Sub-Agent Development in Windsurf

1. In Windsurf, tag the generated execution plan in Cascade using the `@` symbol<sup></sup>:
2. Cascade initializes a task list and coordinates multiple sub-agents to construct the application:
   * **Agent 1:** Generates database schemas (SQLite with FTS5 for full-text search)<sup></sup>.
   * **Agent 2:** Implements the synthesis pipeline using OpenRouter API keys stored in `.env`<sup></sup>.
   * **Agent 3:** Builds the frontend interface based on the guidelines in `design.md`<sup></sup>.

```
Cascade Sub-Agent Orchestration:
┌─────────────────────────────────────────────────────────────┐
│ [Sub-Agent: Database] ──► Creating SQLite FTS5 Tables       │
│ [Sub-Agent: Logic]    ──► Integrating OpenRouter Sonar API  │
│ [Sub-Agent: UI]       ──► Building Tailwind Search Bar      │
│ [Supervisor Agent]    ──► Running automated test suite      │
└─────────────────────────────────────────────────────────────┘
```

### Step 7: Troubleshoot Runtime Edge Cases

When launching the application for the first time, you may encounter an empty state message:

Plaintext

```
Thin coverage: No usable sources found. Consider adding sources to config/sources.yaml.
```

Rather than treating this as a crash, recognize that the error-handling fallback worked as intended<sup></sup>. The system alerted the user instead of displaying fabricated answers<sup></sup>.

**Troubleshooting Loop:**

1. Copy the error output directly from the browser or terminal<sup></sup>.
2. Paste it into Cascade:
3. Cascade will identify that the local SQLite database lacks initial seed records, create an ingestion script for your curated sources, populate the database, and re-run retrieval to complete the working MVP<sup></sup>.

## Step-by-Step Tutorial 2: Auditing and Refactoring Existing Code with Devin

While Windsurf excels at building new features locally, Cognition's **Devin** is purpose-built for auditing, migrating, and refactoring existing codebases autonomously<sup></sup>.

```
                     DEVIN CLOUD-BASED REFACTORING FLOW

 [GitHub Repo: Model-Console] ──► [Devin Cloud Sandbox]
                                           │
                                           ▼
                              [Full Context Repository Scan]
                                           │
                                           ▼
                              [Autonomous Audit Report]
                              • 4 Critical Bugs Found
                              • 7 Code Quality Bottlenecks
                              • Missing Guard Validations
                                           │
                                           ▼
                              [15-Step Automated Task List]
                              (Branch: devin/refactor-console)
                                           │
                                           ▼
                              [Sandboxed Test Verification]
                                           │
                                           ▼
                              [Automated GitHub Pull Request]
                                           │
                                           ▼
                         [Human Review & Merge to Main]
```

### Step 1: Connect Devin to a GitHub Repository

1. Log into your Devin web workspace<sup></sup>.
2. Link your GitHub account and provide access to an existing repository (e.g., `model-console`, an application that evaluates prompts across competing LLMs)<sup></sup>.
3. Devin initializes an isolated, cloud-hosted Linux sandbox container, clones your codebase, and analyzes the project's dependencies<sup></sup>.

### Step 2: Dispatch the Autonomous Repository Audit

Issue a comprehensive audit instruction to Devin:

Plaintext

```
Analyze the entire repository for model-console. Identify performance bugs, security vulnerabilities, API token waste, and architecture anti-patterns. Generate a detailed code review report and await my confirmation before editing any files.
```

### Step 3: Evaluate Devin's Code Review Report

Devin reviews the repository and returns an itemized audit report<sup></sup>:

* **Identified Bugs:** Identifies duplicate prompt calls to the synthesis engine, which wasted input tokens and degraded evaluation accuracy<sup></sup>.
* **Configuration Drift:** Detects mismatches between environment variable schema guards and sample keys in `.env.example`<sup></sup>.
* **Code Quality Bottlenecks:** Highlights redundant database queries and missing exception boundaries<sup></sup>.

```
Devin Audit Findings Summary:
┌─────────────────────────────────────────────────────────────┐
│ CRITICAL DEFECTS (4):                                       │
│ • Duplicate synthesis prompt calls waste 50% token budget   │
│ • .env.example validation mismatch causes runtime crashes   │
│                                                             │
│ CODE QUALITY (7):                                           │
│ • Missing async exception boundaries on third-party calls   │
│ • Redundant full-table scan on session retrieval            │
└─────────────────────────────────────────────────────────────┘
```

### Step 4: Execute Autonomous Branching and Refactoring

1. Instruct Devin to proceed with fixes:
2. Devin generates a 15-step execution plan and works through each task in its cloud sandbox:
   * Creates an isolated branch (`devin/audit-fixes`)<sup></sup>.
   * Refactors the synthesis logic to eliminate duplicate LLM calls<sup></sup>.
   * Updates environment variable guards<sup></sup>.
   * Executes the test suite in the sandbox to verify zero regressions<sup></sup>.

### Step 5: Review and Merge the Pull Request

1. Once testing succeeds, Devin automatically pushes the branch and opens a GitHub Pull Request<sup></sup>.
2. Devin generates a PR summary that includes unified diffs, root-cause explanations, and sandbox test results<sup></sup>.
3. You can review the PR on GitHub—running automated CI/CD scans like Sourcery—and merge the improvements directly into `main`<sup></sup>.

## Community & Builder Ecosystem: Outskill Alumni Forge

To support builders beyond initial cohort training, the Outskill community established the **Alumni Forge**—a collaborative platform hosted on the Outskill LMS (`platform.outskill.com`)<sup></sup>. The Forge provides an environment where alumni move from structured learning to active, production-grade shipping<sup></sup>.

```
                     OUTSKILL ALUMNI FORGE ARCHITECTURE

                         [Outskill LMS Platform]
                         (platform.outskill.com)
                                    │
    ┌────────────────┬──────────────┼──────────────┬────────────────┐
    ▼                ▼              ▼              ▼                ▼
[Peer Support]  [Showcases]  [General Chat]   [AI Pulse]    [Opportunity Hub]
Debug Blockers  "Hype Room"   Water Cooler    Model News    Roles, Gigs &
& Code Errors   Ship Habits   & Networking    & API Drops   Co-Founders
```

### The Five Operational Spaces

```
┌───────────────────────────────┬──────────────────────────────────────────────────────────────┐
│ Space                         │ Core Objective & Interaction Style                           │
├───────────────────────────────┼──────────────────────────────────────────────────────────────┤
│ 1. Peer Support               │ Technical troubleshooting hub across AIAP, AI Generalist,   │
│                               │ and Catalyst tracks. Drop screenshots and error logs to      │
│                               │ debug blockers with peers and mentors.           │
├───────────────────────────────┼──────────────────────────────────────────────────────────────┤
│ 2. Wins & Showcases           │ The community "Hype Room." Post MVPs, Sunday hack projects,  │
│                               │ and shipped milestones to build momentum and inspire peers   │
│                               │. Features a pinned Google Form to apply for    │
│                               │ the bi-weekly Live Product Showcase.            │
├───────────────────────────────┼──────────────────────────────────────────────────────────────┤
│ 3. General Chat               │ Virtual water cooler for informal exchanges, tooling roasts, │
│                               │ and casual networking.                          │
├───────────────────────────────┼──────────────────────────────────────────────────────────────┤
│ 4. AI Pulse                   │ Curated real-time feed for major model releases, API changes,│
│                               │ research breakthroughs, and industry shifts.    │
├───────────────────────────────┼──────────────────────────────────────────────────────────────┤
│ 5. Opportunity Hub            │ Professional marketplace for full-time roles, freelance gigs,│
│                               │ and technical co-founder matching.              │
└───────────────────────────────┴──────────────────────────────────────────────────────────────┘
```

### The Nine Cardinal Community Guidelines

Participation in the Forge is governed by nine operational principles:

1. **Show Up as a Builder:** Lurking is acceptable for your first week; after that, active participation and knowledge sharing are expected<sup></sup>.
2. **Zero Unsolicited Selling:** Direct pitches and sales outreach belong exclusively in the Opportunity Hub; unsolicited DMs are strictly prohibited<sup></sup>.
3. **Search Before Asking:** With over 10,000 community members, search existing discussion threads before opening new support requests<sup></sup>.
4. **Credit Publicly:** Always publicly attribute peers, mentors, or open-source contributors who help you debug or ship features[cite: 1, 3].
5. **Confidentiality:** Work-in-progress architectures, early metrics, and shared revenues remain strictly inside the Forge[cite: 1, 3].
6. **LMS-Centric Communication:** All discussions remain on the official platform (`platform.outskill.com`); unofficial WhatsApp groups are discouraged[cite: 1, 3].
7. **Professional Respect:** Maintain constructive, respectful communication across all interactions with peers and mentors[cite: 1, 3].
8. **Live Session Priority:** Attend live masterclasses and sprints whenever possible to participate in real-time Q&A[cite: 1, 3].
9. **Single Account Identifier:** Use a single, consistent email address for session access, LMS tracking, and certificate delivery[cite: 1, 3].

### Advanced Pathways: The Catalyst Program

For practitioners seeking to move beyond technical development into commercialization, Outskill offers the **Catalyst** program<sup></sup>. Operating as a six-month applied business accelerator, Catalyst functions like a focused "AI MBA"<sup></sup>. It covers go-to-market (GTM) execution, customer acquisition channels, pricing models, and scalable deployment architectures to help technical builders launch sustainable commercial products<sup></sup>.

## Resources and References

All tools, repositories, design links, and platforms mentioned in this guide are cataloged below with direct context for easy implementation<sup></sup>.

### Core Software & Platforms

* **Windsurf IDE (Devin Desktop):** AI-native editor featuring Cascade sub-agent swarms and multi-model adaptive routing<sup></sup>.
* **Devin Cloud Agent:** Autonomous cloud-native software engineering agent developed by Cognition<sup></sup>.
* **Outskill LMS & Alumni Forge:** Official platform for community spaces, live product teardowns, and sprint materials: [platform.outskill.com](https://platform.outskill.com/)<sup></sup>.

### Repositories & Extension Frameworks

* **Obra Superpowers Claude Code Plugin:** Agent skill framework providing structured brainstorming, planning, and test-driven development workflows: [github.com/obra/superpowers](https://github.com/obra/superpowers)<sup></sup>.
* **Claude Code Documentation & Skills:** Official Anthropic developer guides for running CLI-based workflows: [docs.claude.com](https://docs.claude.com/)<sup></sup>.
* **Claude Skills Marketplace:** Community skill repository: [skill.sh](https://skill.sh/)<sup></sup>.

### Design Systems, APIs, and Services

* **Styles Referrer:** UI design token generator and design-to-markdown exporter: [styles.referrer.design](https://www.google.com/search?q=https://styles.referrer.design)<sup></sup>.
* **OpenRouter:** Unified LLM routing API providing pay-as-you-go access to Claude 3.5/3.7 Sonnet, Perplexity Sonar, and other frontier models<sup></sup>.
* **Apify Web Scraping:** Pre-built scraping actors for X/Twitter search, Substack, and curated publication feeds<sup></sup>.
* **Wispr Flow:** Voice-to-text dictation application used for rapid, high-context prompt engineering<sup></sup>.

### Community Application Forms

* **Forge Product Demo Application:** Google Form pinned in the LMS *Wins & Showcases* space to submit community MVPs for live Friday teardowns<sup></sup>.
* **Catalyst Program Consultation:** 1-on-1 Calendly reservation link shared during the masterclass for builders interested in commercialization and GTM strategy<sup></sup>.

Moving from intermediate to advanced in AI-assisted development is not about memorizing more editor shortcuts; it is about thinking critically before the code is generated[cite: 4]. By pairing clear functional specifications with the right balance of local IDEs and cloud-based autonomous agents, you can build reliable, production-ready software with confidence<sup></sup>.
