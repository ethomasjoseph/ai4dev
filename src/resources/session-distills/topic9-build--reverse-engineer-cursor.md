# Building an AI-Powered Code Editor: Reverse Engineering Cursor with LangGraph

## Executive Summary

Modern AI coding assistants like Cursor, GitHub Copilot, and Claude Code give the impression of being monolithic, highly intelligent engines<sup></sup>. In reality, production-grade coding assistants are modular architectures composed of smaller, specialized components working in concert<sup></sup>. At their foundation, an AI agent is a harness that connects a Large Language Model (LLM) to external tools—such as file readers, file writers, terminal runners, and search indexes<sup></sup>.

This guide deconstructs and reverse-engineers the core architecture of Cursor into three evolutionary stages, modeled after an end-to-end curriculum developed by machine learning engineer Ishan Dutta<sup></sup>:

* **Notebook 1: Hands (Tool Calling & Interface):** Equipping an LLM with deterministic tools via schema registration (`@tool`), dynamic tool binding (`bind_tools`), and token streaming (`astream_events`)<sup></sup>.
* **Notebook 2: Self-Awareness (Self-Correction & Reflection):** Enforcing typed responses with Pydantic (`with_structured_output`), validating execution via shell subprocesses, and orchestrating self-healing retry loops and code-quality reflection before touching user disk<sup></sup>.
* **Notebook 3: The Brain (Orchestration, RAG, & Multi-Agent Parallelism):** Implementing repository-wide semantic search (Codebase RAG via vector indexing), human-in-the-loop review gates (`interrupt` and `Command`), state checkpointing (`MemorySaver`), and parallel multi-file code generation using LangGraph's `Send` API<sup></sup>.

```
┌────────────────────────────────────────────────────────────────────────┐
│                        Cursor Architecture Stack                       │
├────────────────────────┬───────────────────────────────────────────────┤
│ The Brain (Notebook 3) │ Codebase RAG, Planning, Parallel Fan-Out,     │
│                        │ Human-in-the-Loop Gates (interrupt/Command)   │
├────────────────────────┼───────────────────────────────────────────────┤
│ Self-Awareness (NB 2)  │ Subprocess Execution, Structured Output,      │
│                        │ Self-Correction Loops, Reflection Pattern    │
├────────────────────────┼───────────────────────────────────────────────┤
│ The Hands (Notebook 1) │ @tool Schemas, bind_tools, ToolNode,          │
│                        │ StateGraph, Token & Event Streaming           │
└────────────────────────┴───────────────────────────────────────────────┘
```

## Tools Required

To build and run this architecture locally, configure the following environment and dependencies<sup></sup>:

### 1. Core Frameworks & Python Libraries

* **Python 3.10+**
* **`langgraph`:** State machine orchestration, checkpointing, and cyclic agent graphs<sup></sup>.
* **`langchain` & `langchain-core`:** Tool abstractions, schema generation, and standard messaging types<sup></sup>.
* **`langchain-openai`:** Interface for OpenAI models or OpenRouter endpoints<sup></sup>.
* **`pydantic`:** Data validation and structured LLM extraction schemas<sup></sup>.
* **`faiss-cpu`:** In-memory vector database for local codebase semantic search<sup></sup>.
* **`python-dotenv`:** Secure environment variable management<sup></sup>.

### 2. Environment & API Setup

The system supports direct OpenAI endpoints as well as OpenRouter<sup></sup>. Using OpenRouter allows switching between Claude, GPT, and open-source models dynamically<sup></sup>:

Bash

```
pip install langgraph langchain langchain-core langchain-openai pydantic faiss-cpu python-dotenv
```

Python

```
import os
from dotenv import load_dotenv
from langchain_openai import ChatOpenAI

load_dotenv()

# Configuration via OpenRouter (supports multi-model routing)
llm = ChatOpenAI(
    model="openai/gpt-4o-mini",  # or anthropic/claude-3.5-sonnet, openai/gpt-4.5
    api_key=os.getenv("OPENROUTER_API_KEY"),
    base_url="https://openrouter.ai/api/v1",
)
```

## Architecture Overview: The Three-Stage Agent Progression

Every major feature in a production AI code editor corresponds to a specific LangGraph API primitive and an underlying agentic design pattern<sup></sup>:

| **Cursor Feature** | **LangGraph / Python API** | **Underlying Design Pattern** | **Core Functionality** |
| :--- | :--- | :--- | :--- |
| **Chat & File Tools** | `@tool`, `bind_tools`, `ToolNode` | Tool Calling Harness | Inspecting directories, reading, and creating files. |
| **Streaming Output** | `app.astream_events` | Reactive Event Streaming | Emitting token-by-token text and tool execution logs in real time. |
| **Code vs. Text Separation** | `with_structured_output` | Schema Enforcement | Isolating runnable code from commentary without regex parsing. |
| **BugBot (Auto-Fixing)** | `execute` node + conditional edges | Exception Handling & Retry Loops | Executing generated code in a subprocess and retrying on tracebacks. |
| **Code Review Suggestions** | Dedicated `review` node | Reflection Pattern | Evaluating code quality, modularity, and PEP 8 compliance before disk writes. |
| **`.cursorrules`** | Dynamic system prompt injection | Context Augmentation | Injecting project-specific architectural rules into agent prompts. |
| **Cmd+K Inline Edit** | Scoped context capture & replacement | Localized In-Place Transformation | Passing targeted code selections to an LLM without altering outer files. |
| **`@codebase` Search** | FAISS vector store + retriever tool | Knowledge Retrieval (RAG) | Semantically indexing source trees to ground plans in relevant context. |
| **Multi-File Edits** | `Send` API + custom reducers | Map-Reduce Parallelization | Fanning out file edits across concurrent worker nodes. |
| **Review Before Applying** | `interrupt()` + `Command(resume=...)` | Human-in-the-Loop (HITL) | Halting graph execution for user confirmation before committing changes. |
| **Session Persistence** | `MemorySaver` + thread checkpoints | Stateful Memory Management | Pausing, resuming, and rolling back multi-turn agent runs. |

## Stage 1: Giving the Model Hands (Tools & Execution Flow)

An LLM on its own is a sequence predictor; it cannot read files, write disk buffers, or execute bash scripts<sup></sup>. An AI agent is simply a harness that pairs the model with deterministic functions<sup></sup>.

### Why the `@tool` Decorator Matters

When equipping an agent with dozens or hundreds of tools, sending complete function definitions consumes hundreds of thousands of context tokens<sup></sup>. LangChain's `@tool` decorator extracts only the function name, the type-hinted parameters, and the docstring into a JSON schema<sup></sup>. The LLM reasons over this compact metadata rather than parsing raw Python code<sup></sup>.

Python

```
from langchain_core.tools import tool

@tool
def read_file(file_path: str) -> str:
    """Read the contents of a file and return it as a string."""
    try:
        with open(file_path, "r", encoding="utf-8") as f:
            return f.read()
    except Exception as e:
        return f"Error reading {file_path}: {str(e)}"

@tool
def write_file(file_path: str, content: str) -> str:
    """Write content to a file. Creates directories and file if they do not exist."""
    try:
        os.makedirs(os.path.dirname(file_path), exist_ok=True)
        with open(file_path, "w", encoding="utf-8") as f:
            f.write(content)
        return f"Successfully wrote {len(content)} characters to {file_path}"
    except Exception as e:
        return f"Error writing {file_path}: {str(e)}"

@tool
def list_directory(path: str = ".") -> str:
    """List all files and folders in a given directory path."""
    try:
        entries = os.listdir(path)
        return "\n".join(entries) if entries else "Directory is empty."
    except Exception as e:
        return f"Error listing directory: {str(e)}"

tools = [read_file, write_file, list_directory]
```

### The Two-Turn Mechanics of `bind_tools`

Calling `llm.bind_tools(tools)` does not automatically execute code<sup></sup>:

1. **Turn 1 (Identification):** The LLM reviews the prompt and emits a tool call request containing the tool name and extracted arguments (e.g., `list_directory(path=".")`)<sup></sup>.
2. **Turn 2 (Execution & Formatting):** The orchestrator runs the Python function, returns the raw output to the LLM, and the LLM synthesizes the result into natural language<sup></sup>.

### Constructing the Base LangGraph Agent

A minimal agent consists of two nodes: an `agent` node and a `tools` node, connected by a conditional edge<sup></sup>.

Python

```
from langgraph.graph import StateGraph, START, END, MessagesState
from langgraph.prebuilt import ToolNode

# Bind tools to the model
llm_with_tools = llm.bind_tools(tools)

def agent_node(state: MessagesState):
    """Invokes the model with the current conversation history."""
    return {"messages": [llm_with_tools.invoke(state["messages"])]}

def should_continue(state: MessagesState):
    """Routes to 'tools' if the last message requested a tool call, else END."""
    last_message = state["messages"][-1]
    if hasattr(last_message, "tool_calls") and last_message.tool_calls:
        return "tools"
    return END

# Graph Construction
builder = StateGraph(MessagesState)
builder.add_node("agent", agent_node)
builder.add_node("tools", ToolNode(tools))

builder.add_edge(START, "agent")
builder.add_conditional_edges("agent", should_continue, ["tools", END])
builder.add_edge("tools", "agent")  # Return tool output back to agent for synthesis

agent_app = builder.compile()
```

### Real-Time Event Streaming

Cursor delivers instantaneous feedback via streaming<sup></sup>. By replacing `app.invoke` with `app.astream_events`, clients capture token generation and tool life cycles as they happen<sup></sup>:

Python

```
import asyncio

async def stream_agent_execution(user_query: str):
    inputs = {"messages": [("user", user_query)]}
    async for event in agent_app.astream_events(inputs, version="v2"):
        kind = event["event"]
      
        # Stream text token-by-token
        if kind == "on_chat_model_stream":
            content = event["data"]["chunk"].content
            if content:
                print(content, end="", flush=True)
              
        # Surface tool call lifecycle
        elif kind == "on_tool_start":
            print(f"\n[Executing Tool: {event['name']} with args: {event['data'].get('input')}]")
        elif kind == "on_tool_end":
            print(f"\n[Tool Finished: {event['name']}]")
```

## Stage 2: Self-Awareness (Self-Correction, Reflection, & Inline Edits)

Production assistants cannot rely on raw text outputs<sup></sup>. If an LLM returns a markdown code block surrounded by explanations, automated file-writing pipelines require brittle regex to extract the executable logic<sup></sup>. Furthermore, broken code must be detected and corrected before committing changes<sup></sup>.

### 1. Structured Output with Pydantic

Using `LLM.with_structured_output`, the model is constrained to return a typed Pydantic object, completely separating executable code from developer explanations<sup></sup>.

Python

```
from pydantic import BaseModel, Field

class CodeOutput(BaseModel):
    code: str = Field(description="The complete, runnable Python code without markdown tags.")
    explanation: str = Field(description="A concise summary of how the code works.")

structured_llm = llm.with_structured_output(CodeOutput)
# Access attributes cleanly:
# response = structured_llm.invoke("Write a binary search function")
# run_code(response.code), display(response.explanation)
```

### 2. The Local Sandbox: Subprocess Execution

To verify code viability, the agent executes Python scripts inside a local subshell via `python -c`<sup></sup>:

Python

```
import subprocess

def execute_python(code: str) -> dict:
    """Executes a snippet in an isolated Python subprocess and captures std streams."""
    try:
        result = subprocess.run(
            ["python", "-c", code],
            capture_output=True,
            text=True,
            timeout=10
        )
        return {
            "stdout": result.stdout.strip(),
            "stderr": result.stderr.strip(),
            "exit_code": result.returncode,
            "success": result.returncode == 0
        }
    except subprocess.TimeoutExpired:
        return {"stdout": "", "stderr": "Execution timed out after 10s.", "exit_code": -1, "success": False}
```

### 3. The Self-Correction & Reflection State Machine

A resilient coding agent uses a multi-tier verification pipeline<sup></sup>:

1. **Generate:** Produces code based on user specifications and previous error traces<sup></sup>.
2. **Execute:** Validates that the code actually runs without runtime tracebacks<sup></sup>.
3. **Review (Reflection):** Inspects the working code for style, type hints, security flaws, and modularity<sup></sup>.

```
  ┌─────────┐
  │  START  │
  └────┬────┘
       ▼
┌──────────────┐◄──────────────────────────────┐
│   GENERATE   │                               │
└──────┬───────┘                               │
       ▼                                       │
┌──────────────┐  Failed (Retries < 3)          │
│   EXECUTE    ├───────────────────────────────┤
└──────┬───────┘                               │
       │ Success                               │
       ▼                                       │
┌──────────────┐  Rejected (Retries < 3)       │
│    REVIEW    ├───────────────────────────────┘
└──────┬───────┘
       │ Approved
       ▼
  ┌─────────┐
  │   END   │
  └─────────┘
```

> **Design Choice: Why Execute Runs Before Review**Executing the code before running the LLM reviewer is significantly more token-efficient<sup></sup>. If generated code fails with a syntax error or a broken import, passing it to an LLM reviewer wastes expensive reasoning tokens on broken logic<sup></sup>. By filtering for mechanical execution first, the LLM reviewer only analyzes functional code<sup></sup>.

Python

```
from typing import TypedDict, Optional
from pydantic import BaseModel, Field

class ReviewResult(BaseModel):
    approved: bool = Field(description="True if the code satisfies style and engineering standards.")
    feedback: str = Field(description="Actionable critique if rejected; empty string if approved.")

class AgentState(TypedDict):
    task: str
    code: str
    explanation: str
    error: Optional[str]
    review_feedback: Optional[str]
    attempts: int
    max_attempts: int

# Nodes
def generate_node(state: AgentState):
    prompt = f"Task: {state['task']}\n"
    if state.get("error"):
        prompt += f"Previous execution error to fix: {state['error']}\n"
    if state.get("review_feedback"):
        prompt += f"Reviewer feedback to incorporate: {state['review_feedback']}\n"
      
    result = structured_llm.invoke(prompt)
    return {
        "code": result.code,
        "explanation": result.explanation,
        "attempts": state["attempts"] + 1
    }

def execute_node(state: AgentState):
    res = execute_python(state["code"])
    if res["success"]:
        return {"error": None}
    return {"error": res["stderr"]}

def review_node(state: AgentState):
    reviewer = llm.with_structured_output(ReviewResult)
    prompt = f"Review this Python code against PEP 8, typing, and modularity:\n\n{state['code']}"
    review = reviewer.invoke(prompt)
    return {"review_feedback": "" if review.approved else review.feedback}

# Routing logic
def route_after_execution(state: AgentState):
    if state["error"] is None:
        return "review"
    if state["attempts"] >= state["max_attempts"]:
        return END  # Give up state
    return "generate"

def route_after_review(state: AgentState):
    if not state["review_feedback"]:
        return END  # Code runs and passed review
    if state["attempts"] >= state["max_attempts"]:
        return END  # Give up state
    return "generate"

# Build Graph
builder = StateGraph(AgentState)
builder.add_node("generate", generate_node)
builder.add_node("execute", execute_node)
builder.add_node("review", review_node)

builder.add_edge(START, "generate")
builder.add_edge("generate", "execute")
builder.add_conditional_edges("execute", route_after_execution, ["review", "generate", END])
builder.add_conditional_edges("review", route_after_review, ["generate", END])

bugbot_app = builder.compile()
```

### 4. Reverse-Engineering Cursor Features

#### Cmd+K (Inline Edits)

When a user highlights code and hits `Cmd+K`, Cursor does not send the entire workspace to the model<sup></sup>. It isolates the highlighted snippet, injects nearby file boundaries as context, prompts the LLM to rewrite only that segment, and performs an in-memory buffer replacement<sup></sup>:

Python

```
def inline_edit(selected_code: str, instruction: str) -> str:
    prompt = f"""You are an inline code editor.
Instruction: {instruction}
Selection to replace:
```python
{selected_code}
```

Output ONLY the replacement code, maintaining indentation. No markdown, no commentary."""

return llm.invoke(prompt).content.strip()

```

#### `.cursorrules` (Dynamic Rule Injection)
Cursor rules are simply specialized system prompts appended to every inference turn. Adding an explicit system prompt turns generic, un-typed code into production-grade Python:

```python
EXPERT_SYSTEM_PROMPT = """You are an expert software engineer.
When generating code, you MUST adhere to:
1. Complete PEP 484 type annotations on every function signature.
2. Standardized, descriptive docstrings detailing arguments, returns, and raises.
3. Explicit error handling; never use bare except blocks.
4. Clean, modular functions adhering strictly to single-responsibility principles."""
```

## Stage 3: The Production Brain (RAG, Planning, Parallelism, & HITL)

Building a full-scale AI assistant requires understanding repository context, coordinating multiple files simultaneously, and ensuring safety via human approval<sup></sup>.

```
                                  ┌─────────────┐
                                  │    START    │
                                  └──────┬──────┘
                                         ▼
                                  ┌─────────────┐
                                  │ PLAN (RAG)  │
                                  └──────┬──────┘
                                         ▼
                         ┌───────────────────────────────┐
                         │   FAN-OUT (LangGraph Send)    │
                         └───────┬───────────────┬───────┘
                                 │               │
                                 ▼               ▼
                          ┌─────────────┐ ┌─────────────┐
                          │ CODER (App) │ │CODER(Config)│
                          └──────┬──────┘ └──────┬──────┘
                                 │               │
                                 └───────┬───────┘
                                         ▼
                                  ┌─────────────┐
                                  │  AI REVIEW  │
                                  └──────┬──────┘
                                         ▼
                                  ┌─────────────┐
                                  │HUMAN REVIEW │
                                  │ (interrupt) │
                                  └──────┬──────┘
                                         │ Approved
                                         ▼
                                  ┌─────────────┐
                                  │ APPLY FILES │
                                  └──────┬──────┘
                                         ▼
                                  ┌─────────────┐
                                  │    TEST     │
                                  └──────┬──────┘
                                         ▼
                                  ┌─────────────┐
                                  │     END     │
                                  └─────────────┘
```

### 1. Codebase RAG (Semantic Search via FAISS)

Cursor maintains a background vector index over repository files<sup></sup>. This allows questions like *"Where is authentication handled?"* to automatically resolve to `auth.py` without explicit path mentions<sup></sup>.

Python

```
from langchain_community.vectorstores import FAISS
from langchain_openai import OpenAIEmbeddings
from langchain_text_splitters import RecursiveCharacterTextSplitter
from langchain_core.documents import Document

def index_repository(repo_path: str):
    documents = []
    for root, _, files in os.walk(repo_path):
        for file in files:
            if file.endswith((".py", ".ts", ".js", ".md")):
                full_path = os.path.join(root, file)
                with open(full_path, "r", encoding="utf-8") as f:
                    documents.append(Document(page_content=f.read(), metadata={"source": full_path}))
                  
    splitter = RecursiveCharacterTextSplitter(chunk_size=800, chunk_overlap=100)
    chunks = splitter.split_documents(documents)
  
    vectorstore = FAISS.from_documents(chunks, OpenAIEmbeddings())
    return vectorstore.as_retriever(search_kwargs={"k": 4})
```

### 2. The Planner Agent

Complex features touch multiple files<sup></sup>. A dedicated Planner agent breaks a feature request down into targeted, file-by-file action plans<sup></sup>:

Python

```
class FileTask(BaseModel):
    file_path: str = Field(description="The path of the file to modify or create.")
    action: str = Field(description="'CREATE' or 'MODIFY'")
    instructions: str = Field(description="Precise technical changes required in this file.")

class SystemPlan(BaseModel):
    summary: str = Field(description="High-level engineering overview of the change.")
    file_tasks: list[FileTask] = Field(description="Individual tasks partitioned per file.")
```

### 3. Human-in-the-Loop (HITL) with `interrupt` and `Command`

Allowing an agent to write directly to a codebase without developer oversight is hazardous<sup></sup>. LangGraph introduces native breakpoints via `interrupt()`<sup></sup>. When invoked, the graph serializes its state into a checkpointer (`MemorySaver`) and yields execution<sup></sup>. Once approved, it is resumed using `Command(resume=...)`<sup></sup>:

Python

```
from langgraph.types import interrupt, Command
from langgraph.checkpoint.memory import MemorySaver

def human_review_node(state: dict):
    """Halts graph execution and surfaces the plan/diff to the developer."""
    decision = interrupt({
        "message": "Please review proposed file modifications.",
        "plan": state.get("plan"),
        "files_affected": [t["file_path"] for t in state.get("file_tasks", [])]
    })
  
    # Execution pauses here until Command(resume=...) is passed to graph
    return {"human_decision": decision.get("action"), "feedback": decision.get("feedback")}

def route_after_human(state: dict):
    if state["human_decision"] == "approve":
        return "apply"
    return "code"  # Loop back if rejected with feedback
```

### 4. Parallel File Generation via the `Send` API

When changes span three independent files, generating them sequentially is slow<sup></sup>. LangGraph's `Send` primitive fans tasks out across multiple instances of a worker node simultaneously<sup></sup>:

Python

```
import operator
from typing import Annotated
from langgraph.types import Send

class ProductionState(TypedDict):
    feature_request: str
    file_tasks: list[FileTask]
    # Annotated with operator.add to merge parallel returns cleanly into one list
    generated_files: Annotated[list[dict], operator.add]
    human_decision: str

def fan_out_to_coders(state: ProductionState):
    """Spawns an independent code generation node for every planned file task."""
    return [
        Send("code_worker", {"task": task, "feature_request": state["feature_request"]})
        for task in state["file_tasks"]
    ]

def code_worker_node(payload: dict):
    task = payload["task"]
    prompt = f"Implement changes for {task.file_path}: {task.instructions}"
    code_result = structured_llm.invoke(prompt)
    return {"generated_files": [{"file_path": task.file_path, "code": code_result.code}]}
```

### 5. Compiling the Production Graph

Connecting the planner, fan-out workers, reflection reviewer, human gate, and disk applicator<sup></sup>:

Python

```
builder = StateGraph(ProductionState)

builder.add_node("plan", plan_node)
builder.add_node("code_worker", code_worker_node)
builder.add_node("ai_review", ai_review_node)
builder.add_node("human_review", human_review_node)
builder.add_node("apply", apply_changes_to_disk_node)
builder.add_node("test", run_test_suite_node)

builder.add_edge(START, "plan")
builder.add_conditional_edges("plan", fan_out_to_coders, ["code_worker"])
builder.add_edge("code_worker", "ai_review")

def route_after_ai_review(state: ProductionState):
    return "human_review" if state.get("ai_approved") else "plan"

builder.add_conditional_edges("ai_review", route_after_ai_review, ["human_review", "plan"])
builder.add_conditional_edges("human_review", route_after_human, {
    "apply": "apply",
    "code": "plan"
})
builder.add_edge("apply", "test")
builder.add_edge("test", END)

# Checkpointing enables pausing and resuming threads
memory = MemorySaver()
production_agent = builder.compile(checkpointer=memory)
```

To resume the paused agent after a human inspects the diff<sup></sup>:

Python

```
# 1. Initial invocation (runs until interrupt is hit)
thread_config = {"configurable": {"thread_id": "session-101"}}
production_agent.invoke({"feature_request": "Add dark mode toggle"}, config=thread_config)

# 2. Resume execution using Command
production_agent.invoke(
    Command(resume={"action": "approve"}),
    config=thread_config
)
```

## Model Selection & Engineering Heuristics

Different frontier models exhibit distinct behavioral "personalities"<sup></sup>. Optimizing a production assistant requires assigning tasks to the model best suited for that specific role<sup></sup>:

* **Claude Series (e.g., Sonnet, Opus):** Exceptionally skilled at planning, complex system reasoning, and front-end interface design<sup></sup>. Highly effective for generating architecture plans, refactoring frontend code, and executing UI tasks<sup></sup>.
* **GPT Series (e.g., GPT-4.5, Codex, GPT-4o-mini):** Exceptionally strong at raw backend engineering, API contracts, deterministic tool calling, and systems architecture<sup></sup>. GPT-4o-mini serves as a cost-effective choice for localized generation and test loops, while GPT-4.5 excels at complex system design<sup></sup>.
* **Gemini Models:** Highly competent in visual-to-code tasks, multimodal parsing, and generating initial UI design blueprints<sup></sup>.

> **Production Cost Optimization Strategy:** Never use heavyweight models (e.g., Opus or GPT-4.5) inside tight validation loops<sup></sup>. Use frontier reasoning models once at the `plan` stage<sup></sup>. Delegate localized file generation and syntax-fix retries to lightweight, fast models (e.g., GPT-4o-mini or Sonnet) to balance speed and operating cost<sup></sup>.

## Resources and References

* **Tutorial Code & Curriculum:** [Orion Tutorial Curriculum & Interactive Notebooks][https://www.google.com/search?q=https://orion-tutorial.vercel.app/curriculum](cite: 1)
* **LangGraph Core Documentation:** StateGraph primitives, checkpointing, and human-in-the-loop workflows (`interrupt`, `Command`)<sup></sup>.
* **LangChain Tool Calling:** Schema generation, tool binding, and event streaming APIs[cite: 1, 2].
* **Agentic Design Patterns:** Reference implementation of reflection, prompt chaining, parallel worker fan-out, and bounded retry exception handling[cite: 1, 2].
