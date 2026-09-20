# Claude Code + MCP

## Resources

- [Post-Read Session Summary](https://docs.google.com/document/d/1d7CrO4wuOeK-KiUBZUGrAuJsh0bjhxIlJnuY-03U-Uo/edit?usp=sharing)
- [Session Whiteboard (captured as images)](https://drive.google.com/drive/folders/1Qkt_-slsMX5dqnbIAHr0sKYEtEDK0ntA?usp=sharing)

---

## Notes

> Q: How to you compare Codex & Claude Code in your SDLC?
>
> Claude Code for Design\
> Codex for implementation

### Meeting Catch-up

Here's a catch-up on this meeting:\
This is Day 11 of the AI Engineering Accelerator. The session is led by Dileep Karri and focuses on Claude Code + MCP (Model Context Protocol) + Skills for building applications.

**Key points covered so far:**

1. **Session Goal:** Dileep is teaching how to set up a great orchestration layer using Claude Code to write good applications. The session is intentionally lighter than previous days.
2. **Coding Process:** Dileep outlined his workflow for writing code from scratch:
   - Idea → Plan (tech stack, business logic, scope, DB schema, design) → User Stories → Agents → Execution → Test Cases & Review → Deploy
3. **Three Main Components:**
   - Claude Code (command line interface version)
   - MCP/Connectors (equipment/tools)
   - Skills/Playbooks (processes/recipes)
4. **Key Analogy:** Dileep compared this to a carpenter building furniture - MCPs are like the tools (saw, drill, adhesive), Skills are like the processes (how to build a chair vs table), and Claude Code is the carpenter bringing them together.
5. **Demo Project:** They'll be building a "Model Council" feature - a $200/month Perplexity feature where multiple LLMs debate a problem statement and critique each other's responses.
6. **Current Status:** Dileep has just started with an empty folder containing only a .env file (for storing API keys and secrets) and is about to dive into the practical demonstration.

The session will be somewhat nonlinear, switching between building and explaining theory as the agent works.

---

### Summary of the Session Before the Break

**Main Topic:** Setting up Claude Code with MCP servers and skills to build a Model Council application\
**Key Concepts Covered:**

1. **Session Overview:** Dileep introduced a lighter, practical session focused on setting up an orchestration layer using Claude Code, MCP servers, and skills to write good code.
2. **Coding Process:** Dileep outlined his workflow: Idea → Plan (tech stack, business logic, DB schema, design) → User Stories → Agents → Execution → Test Cases → Review → Deploy
3. **MCP vs Skills Analogy:**
   - MCP servers = Equipment/tools (like a carpenter's power saw or chef's knives)
   - Skills = Processes/playbooks (like recipes or furniture-building procedures)
   - Claude Code = The agent/LLM bringing both together
4. **Installing MCP Servers:** Demonstrated installing Firecrawl MCP server by:
   - Finding the MCP on mcpmarket.com
   - Copying the GitHub documentation URL
   - Pasting into Claude Code and asking it to install
   - Providing API keys when prompted
5. **Installing Skills:** Showed how to install skills like GSD Build and Superpowers by:
   - Copying the GitHub repository URL
   - Pasting into Claude Code with "install the skill" command
   - Claude Code handles the installation automatically
6. **Building a Custom Skill:** Created a new "Blueprint" skill that combines:
   - Superpowers (for planning and design)
   - GSD (for execution)
   - This hybrid skill orchestrates the entire development lifecycle
7. **Model Council App:** Started building a $200 Perplexity feature where:
   - Stage 1: Four LLMs independently attack the same problem
   - Stage 2: Models debate and critique each other
   - Stage 3: A fifth synthesizer model produces organized output
8. **Design System:** Integrated a minimalist design from superdesign.dev into the project specifications
9. **Current Status:** The system generated complete specs and began building the application phase by phase using the Blueprint skill

---

### Additional Notes: Summary Before Break

Here's a summary of the session before the break:

**Session Overview:**\
Dileep conducted a hands-on session on using Claude Code with MCP (Model Context Protocol) and Skills to build applications. The focus was on creating an orchestration layer for writing good code.

**Key Concepts Covered:**

1. **MCP vs Skills Analogy:**
   - MCP servers are like equipment/tools (carpenter's saw, drill, chef's knives)
   - Skills are like processes/recipes (how to build a chair, cooking recipe)
   - Claude Code is the agent that uses both to build applications
2. **Workflow Process:**\
   The coding process follows: Idea → Plan (tech stack, business logic, DB schema, design) → User Stories → Agents → Execution → Test Cases → Review → Deploy
3. **Installation Demonstrations:**
   - Showed how to install MCP servers (demonstrated with Firecrawl) by copying GitHub URLs into Claude Code
   - Showed how to install Skills (demonstrated with GSD Build and Superpowers)
   - Used command line interface (CLI) for Claude Code
4. **Building a Custom Skill:**\
   Created a hybrid skill called "Blueprint" that combines:
   - Superpowers (for planning and brainstorming)
   - GSD (for execution and building)
   - This orchestrates the complete development lifecycle
5. **Project Demo:**\
   Started building a "Model Council" app (a $200 Perplexity feature) that:
   - Uses 4 LLMs to independently attack a problem (Stage 1)
   - Has models debate and share information (Stage 2)
   - Uses a 5th synthesizer model to create final output in multiple formats (Stage 3)
   - Applied a minimalist design system from superdesign.dev
6. **Key Technical Points:**
   - MCP is a client-server architecture (like HTTP) where LLMs are clients and applications are servers
   - Can build custom MCP servers using Fast MCP library when needed
   - Claude Max plan ($100/month) recommended for end-to-end builds
   - Uses Git for version control throughout the process

The session was building the Model Council

---

### Session After Break

After the break, Dileep demonstrated several key concepts and processes:

1. **Model Council App Demo**: He showed a previously built Model Council application running on port. The app allows multiple AI models to debate topics independently, then critique each other's responses, before a synthesizer model produces final output in multiple formats (table, prose, bullets).
2. **Debugging Process**: When the Model Council app encountered errors, he showed how to debug by taking screenshots, dropping them into Claude Code, and letting it identify and fix cache issues.
3. **Session Limits & Codex Integration**: Claude Code hit its 96% session limit during the build. Dileep demonstrated how to continue work by:
   - Opening the same project folder in Codex (Cursor)
   - Asking Codex to analyze what Claude Code had completed
   - Having Codex verify it could continue building without breaking existing work
   - Codex identified Phase 1 was complete and recommended building Phase 2 additively
4. **Claude Mem Skill**: He explained this skill acts as an observer that logs all actions to a local SQLite database, allowing context to persist across sessions and reducing token usage by checking against stored context first.
5. **Verification Process**: Codex verified the OpenRouter models and API keys were working correctly before proceeding with the build.
6. **AI Adoption Discussion**: Dileep shared his perspective on AI in engineering, emphasizing that AI should increase ambition and scope rather than just reduce headcount. He encouraged participants to be "surfers riding the wave" rather than waiting at the shore, stressing the importance of learning AI tools regardless of organizational restrictions.

The session ended with Q\&A where he recommended Claude Code for design and Codex for implementation as the ideal workflow.
