# LangChain, LangGraph, LangSmith and Evals

## References

* LangChain Post Read: <https://docs.google.com/document/d/1Pp7H_5UIJDNtWPV3Qrs3tKAIqjgDsB5rpBREwuo8-wo/edit?usp=sharing>
* Basic Langchain: <https://colab.research.google.com/drive/1WcNqPx2qUmN-0uxIRj3tzURCczoMi6jC?usp=sharing>
* Agentic RAG + Agentic Simple: <https://colab.research.google.com/drive/1cYWc0DScsKzBRqmAUEFBqeSpMMcpikqu?usp=sharing>
* LangGraph & LangSmith Post Read: <https://docs.google.com/document/d/1ZEjgSmUWFWzV3eS2V0GukGdXzv71Kbw9_s2BLIsdUg4/edit?usp=sharing>
* Samples: <https://colab.research.google.com/drive/1dZ18yD5VUprqvpDKtv11m1dGZQJ4agl2?usp=sharing>
* LangGraph Official: <https://www.langchain.com/built-with-langgraph>
* LangGrap Project Prompts: <https://docs.google.com/document/d/1y4H_dcEIWcASDVRcOLSlSMoqbraTkTowS_MvkzwWpww/edit?usp=sharing>

## Session Notes

&#x20;
In latest version of LangChain use bitwise operator “pipe” as the new coding paradigm. Research about the latest changes and the reasons behind it. (See LCEL <https://www.langchain.com/blog/langchain-expression-language>)

This session was a comprehensive introduction to LangChain, covering its fundamental components and practical applications. Here's a summary:
**What is LangChain:**

* LangChain is described as "LEGO for AI apps" - a framework that provides all components needed to build production-level AI applications under one umbrella
* It allows easy integration of different AI models, tools, and services with minimal code changes

**Six Core Components Covered:**

1. **Models**: Easy integration of any LLM (OpenAI, Anthropic, Gemini, etc.) with just one line of code
2. **Prompt Templates**: Reusable prompts similar to Python functions, allowing dynamic variable insertion
3. **Chains**: Three types were taught:
   * Simple LLM Chain: Basic API call to an LLM
   * Simple Sequential Chain: Output of one chain becomes input of the next
   * Sequential Chain: Complex data flow where chains can branch and use outputs from multiple previous chains
4. **Agents**: AI systems that can take actions using tools. The session explained the React framework (Reasoning + Acting) where LLMs decide which tools to use and make multiple calls to complete tasks
5. **Output Parsers**: Mechanisms to verify LLM outputs are in expected formats (string, JSON, etc.)
6. **Memory & Indexes**: Briefly covered, with more details promised for the next session

**Practical Project:**
The session concluded with building an Agentic RAG system that combines traditional RAG with AI agents. This system can access multiple tools (Wikipedia, Archive research papers, and web search via Tavily) when answers aren't found in uploaded documents.
<https://colab.research.google.com/drive/1cYWc0DScsKzBRqmAUEFBqeSpMMcpikqu?usp=sharing>

**Key Takeaway:**
The industry is moving from basic LLM applications to agentic systems where AI can autonomously use tools and take actions, making applications more robust and capable.

LangChain has tools and agents

AI agents works on ReAct framework - Reasoning + Acting

* Reason -> which tool should I use?
* Act ->

Exercise to build Agentic RA

LangChain

* Indexes are elements required to make agents.

Streamlit

* Python framework to build UI

**Summary:**
LangChain,:

1. LangChain as a framework that brings different AI components under one umbrella
2. Five key components: Models (importing LLMs from various sources), Prompt Templates (reusable prompts), Chains (three types: simple LLM chain, simple sequential chain, and sequential chain), Memory (session-based and long-term memory for agents), and Indexes (small components like document loaders and tools)
3. The ReAct agent framework and LCEL (LangChain Expression Language)
4. An agentic RAG example with tools like Tavily (web search), Wikipedia, and Archive

**LangGraph**
Based on the meeting discussion, here is a summary of LangGraph:
**What is LangGraph:**\
LangGraph is a framework built on top of LangChain by the same company. It serves two main purposes:

1. Adding cyclic computational capabilities (self-healing) - allowing systems to auto-correct and debug themselves through loops with a brain, rather than just simple loops
2. Orchestrating teams of AI agents - managing multiple AI agents working together, controlling data flow, memory, and coordination between them

**Three Core Components:**

1. **Nodes** - Any computational step that takes action (can be a Python function, API call, or AI agent)
2. **States** - The memory component that transfers data between nodes. Each node updates the state after completing work and passes it to the next node
3. **Edges** - Connecting lines that define which node runs first, second, third, and how nodes are connected (can be conditional)

**How it Works:**

* Think of LangGraph like an empty drawing canvas
* First, define the state (memory structure)
* Second, create the empty canvas (workflow)
* Third, define node logic
* Fourth, add nodes to the canvas
* Fifth, connect nodes with edges
* Finally, compile and run

The instructor demonstrated a simple program and a complex Dynamic Story Generator project to show how state is maintained and passed between nodes, allowing for interactive, branching narratives. LangGraph enables building production-level AI systems used by companies like Uber, LinkedIn, and Klarna.

Mental Model + Prompts to build a system with LangGraph using your coding agents:
<https://docs.google.com/document/d/1y4H_dcEIWcASDVRcOLSlSMoqbraTkTowS_MvkzwWpww/edit?usp=sharing>

**LangSmith**

**TL;DR**

* It’s for observability - even for non AI applications.
* Not open source. The OpenSource version is called LangFuse - <https://langfuse.com>
* Basic usage is free. Signup, get API key and follow the simple instructions.

**Summary**
LangSmith is a platform built by the same company that created LangChain and LangGraph, but unlike those frameworks, LangSmith is NOT open source. Its primary purpose is to track and monitor AI applications in production.
Key features and uses of LangSmith discussed:

1. **Tracking and Tracing**: LangSmith helps track the entire AI app execution, logging all steps, data flow, and actions taken by the system.
2. **Performance Monitoring**: It tracks important metrics like:
   * Latency (P50, P99 latency tracking)
   * Token usage and cost estimation
   * Error rates
3. **Implementation**: Very simple to integrate - just:
   * Sign up for LangSmith (free tier available)
   * Generate an API key
   * Add environment variables to your code
   * Use the @traceable decorator before functions you want to monitor
4. **Dashboard Features**: The LangSmith dashboard automatically logs:
   * All API calls (inputs and outputs)
   * Token breakdowns
   * Execution time for each step
   * Complete pipeline visualization
5. **Use Cases**: Particularly important for production systems where latency and cost matter, such as financial trading platforms.
6. **Agent Evaluation**: LangSmith is also used for evaluating AI agents to determine if they performed correctly.

The instructor demonstrated a simple example with two API calls (OpenAI and Claude) showing how everything was automatically logged and tracked in the LangSmith dashboard without manual intervention.

**Evals**

Based on the meeting discussion, here's a summary of the Evals (Evaluation) topic:
**Three Types of Agentic Evaluation:**

1. **Exact Match** - This is the most basic method where you do a direct comparison between the LLM's answer and the correct answer. However, this is very strict and can fail even when the intention is correct. For example, if the correct answer is "8" but the LLM responds "the correct answer is 8", the exact match will fail because it's comparing a string to a number.
2. **LLM as a Judge** - This method uses a separate LLM to evaluate if the intention of the LLM's answer matches the correct answer. Instead of exact matching, it checks whether the meaning and correctness are the same. This is more flexible and can pass evaluations even when the format differs but the core answer is correct.
3. **Trajectory (Tracing)** - This is the most commonly used method in production. Instead of focusing on the final output, it focuses on the thought process and which tools the agent used to arrive at the answer. It evaluates whether the agent called the right tools and followed the correct reasoning path. If an agent has 100 different tools, this method checks if it selected the appropriate ones for the given query.

**Practical Implementation:**

* Evaluation requires preparing a dataset on Langsmith with questions and expected correct answers
* The agent runs on these questions and its outputs are compared to the correct answers
* In production environments, companies typically use both LLM as a judge and trajectory methods together
* Paras mentioned working with Mastercard Dubai where they used both evaluation methods

**Tools:**

* Langsmith is used for evaluation but it's not open source
* For open source alternatives, Langfuse was recommended
  <https://smith.langchain.com/o/4d4bd248-4c2d-54cf-a271-7e1e60e393c3/evaluators>

The Q\&A session covered several key topics:
**LangGraph Fundamentals:**

* Questions about state management, including how to prevent states from growing too large (answer: only include parameters that improve LLM accuracy)
* Clarification on when to use LangGraph vs other frameworks like CrewAI (LangGraph for production, CrewAI for prototyping with clearly defined roles)
* Discussion of nodes, edges, and states as the three core components

**Evaluation Methods:**

* Three types of agentic evaluation were discussed: exact match (for mathematical/financial data), LLM as a judge (for semantic intention), and trajectory (for production systems to track tool usage and thought process)
* Questions about measuring hallucinations (answer: use groundedness evaluation for factual questions, or LLM as judge with web verification)

**LangSmith:**

* Multiple questions about LangSmith being free but logging data
* Clarification that LangSmith is free for basic use but data is logged; enterprise plans available for data privacy
* Questions about performance impact (answer: adds slight latency due to data transfer)
* Discussion of using Langfuse as an open-source alternative

**Technical Implementation:**

* Questions about integrating multiple states (answer: use state transfer between agents, similar to parent-child class relationships)
* Retry limits and guardrails discussion, including PI data protection using tools like Microsoft's Presidio
* Lambda function clarification (Python concept for typecasting, not AI-specific)

**General Advice:**

* Don't force LangGraph into every problem - assess if simple solutions work first
* Practice by building projects and using resources like Claude for code explanation
* Code and requirements files will be shared on GitHub

Paras emphasized that many questions were subjective and depend on specific use cases, encouraging participants to research further and practice building projects.
