# Agentic AI for Developers
### Concepts & Applications for Enterprises — LinkedIn Learning

> 📚 **Course:** [Agentic AI for Developers](https://www.linkedin.com/learning/agentic-ai-for-developers-concepts-and-application-for-enterprises/building-your-own-ai-agent?u=42751868)
> 🐙 **GitHub:** [Course Repository](https://github.com/LinkedInLearning/agentic-ai-for-developers-concepts-and-applications-for-enterprises-3913172)

---

## Table of Contents
- [Chapter 1: The Agentic AI Ecosystem](#chapter-1-the-agentic-ai-ecosystem)
- [Chapter 2: Reference Architecture](#chapter-2-agentic-ai-system--reference-architecture)
- [Chapter 3: Building Your First Agentic Application](#chapter-3-building-your-first-agentic-application--the-routing-pattern)
- [Chapter 4: Design Patterns for Agentic AI](#chapter-4-design-patterns-for-agentic-ai)

---

## Chapter 1: The Agentic AI Ecosystem

An agentic system is not a single tool — it is a **multi-service orchestration** designed to solve complex, multi-step problems that a simple LLM prompt cannot handle.

### 1.1 Orchestration & Frameworks

These are the **Orchestrators** and **Planners** of agentic systems — they provide the structure and control flow for the AI's logic.

| Framework | Description | Best For |
|---|---|---|
| **LangChain** | Most widely adopted framework for LLM apps. Chains components together: prompts, LLMs, memory, databases. | Building step-by-step reasoning pipelines |
| **LangGraph** | Extension of LangChain built for **cyclic, stateful flows**. Agents can loop back, retry steps, and maintain complex state. | True agentic behavior where decisions affect future actions |
| **LlamaIndex** | Connects LLMs to private/external data (RAG). The Agent layer adds reasoning — it decides *which* data source to query. | Large-scale data retrieval with intelligent query routing |

### 1.2 Multi-Agent Orchestration

When one agent is not enough — these frameworks coordinate multiple specialized agents using the **Agent-as-a-Tool** pattern.

- **AutoGen** (by Microsoft) — Enables multiple agents to converse with each other to solve a task. Example: one agent writes code, another reviews and debugs it autonomously.
- **CrewAI** — Orchestrates "crews" of AI agents, each with a distinct role (e.g., Researcher, Writer, Reviewer), working in a structured process toward a shared goal.

### 1.3 Infrastructure & Storage

- **Vector Databases** — Store data as mathematical vectors (embeddings), enabling **semantic search**: finding information by *meaning*, not just exact keywords.
  - Examples: Pinecone, Milvus, Weaviate

### 1.4 Cloud AI Platforms (The Models)

| Platform | Provider | Description |
|---|---|---|
| **Amazon Bedrock** | AWS | Managed access to top LLMs (Claude, Llama, Mistral) via API — no server management needed |
| **Google Vertex AI** | Google Cloud | Unified platform for accessing Gemini models with full training, deployment, and monitoring tooling |

### 1.5 Ecosystem Summary

| Category | Technology | Best Used For |
|---|---|---|
| Data Connectivity | LlamaIndex | Connecting private/external data to an LLM (RAG) |
| Logic & Workflow | LangChain / LangGraph | Step-by-step reasoning and cyclic agent flows |
| Multi-Agent Teamwork | AutoGen / CrewAI | Multiple agents collaborating on a shared goal |
| Long-Term Memory | Vector Database | Semantic search and persistent knowledge storage |
| Compute & Models | Bedrock / Vertex AI | Accessing LLMs and cloud-scale infrastructure |

---

## Chapter 2: Agentic AI System — Reference Architecture

An Agentic AI system is a **distributed architecture of specialized services**. The goal is to move beyond simple prompts to autonomous goal completion.

### 2.1 Goals vs. Prompts

> 💡 A **Prompt** is a direct instruction. A **Goal** is a mission — it requires multi-step reasoning, strategy, and tool use.

| | Example |
|---|---|
| **Prompt** | *"Write a professional email to the client."* |
| **Goal** | *"Research the competitor's pricing, compare it with ours, identify the gaps, and then draft a strategy email."* |

### 2.2 The Planner — Strategy Engine

- Breaks a complex goal into a sequence of actionable steps using an LLM
- Looks at current progress and determines the **next best action**
- **Adaptive:** if a step fails, the Planner re-routes and creates a new plan based on feedback

### 2.3 The Orchestrator & Executor

- **Orchestrator** *(Project Manager)* — Central hub that manages the full task lifecycle; moves information between the Planner, Executor, and Memory
- **Executor** *(The Muscle)* — Receives instructions from the Planner and triggers the necessary tools to perform each task

### 2.4 Tools — Interface to the Real World

| Tool Type | Description |
|---|---|
| **RAG** | Fetches current, specific data from documents or databases — extends the agent beyond its training knowledge |
| **Functions / APIs** | Code that calls microservices, cloud APIs, or databases — gives the agent the ability to take real-world actions |
| **Sub-Agents** | A complex agent delegates niche tasks to smaller, specialized agents *(Agent-as-a-Tool pattern)* |

### 2.5 GenAI Models — Heterogeneous by Design

> 💡 You don't have to use the same LLM for every component. Use a **powerful model** for the Planner (high reasoning quality) and a **smaller, faster model** for the Executor or summarization tasks (lower cost and latency).

### 2.6 Memory — Short-Term & Long-Term

- **Short-Term Memory** — Stores the context of the current session (what has been tried in the last few minutes)
- **Long-Term Memory** — Stores historical interactions, learned user preferences, and past results — typically in a Vector Database
- **State Management** — Tracks exactly where the system is in a multi-step process, enabling task resumption and improvement over time. This is what makes a system truly *agentic*.

### 2.7 Architectural Flow

```
User provides a Goal
        ↓
Orchestrator requests a strategy from the Planner
        ↓
Planner + LLM reasons through steps → lists required Tools
        ↓
Executor triggers each tool (APIs, RAG, Sub-Agents)
        ↓
Memory tracks state and results throughout
        ↓
Orchestrator delivers the final output
```

---

## Chapter 3: Building Your First Agentic Application — The Routing Pattern

This chapter covers the practical implementation of the **Routing Pattern** using LlamaIndex — moving from a single flat RAG system to an intelligent Router Agent.

### 3.1 The Problem: Why Routing Matters

- In a traditional single-index system, all data is mixed together causing **"data bleeding"** — queries for one product return results contaminated by another product's data
- **Solution:** partition data into separate, specialized indexes and use a Router Agent to dispatch each query to the correct destination

> 💡 Think of the Router Agent as a receptionist who reads your question and immediately forwards you to the right department specialist — rather than shouting your question into a crowded room.

### 3.2 Intent vs. Keyword Routing

| Approach | Logic | Example |
|---|---|---|
| Traditional | Hard-coded `if/else` on keywords | Must match exact product name |
| **Agentic Router** | LLM understands **semantic intent** | Correctly routes *"What shades come with the trail bike?"* without needing the exact product name |

### 3.3 Implementation Stack

| Component | Technology | Role |
|---|---|---|
| Environment | Python 3.10 + GitHub Codespaces | Development runtime |
| LLM | Azure OpenAI GPT-3.5 Turbo | Decision-making and query routing |
| Embeddings | AzureOpenAIEmbedding | Converts text to vectors for semantic search |
| Framework | LlamaIndex | Orchestration, indexing, and agent tooling |
| Async Handling | `asyncio` | Handles non-blocking LLM calls |

### 3.4 Step-by-Step Build Process

**Step 1: Data Partitioning & Indexing**
- Use `SimpleDirectoryReader` to ingest product documents
- Chunk documents into smaller text nodes for granular retrieval
- Create separate **In-Memory Vector Store Indexes** per product (e.g., one for AeroFlow, one for EcoSprint)
- Expose each index as a `QueryEngine` — the interface for searching that data silo

**Step 2: Wrap Engines as Agentic Tools**
- Wrap each `QueryEngine` using `QueryEngineTool`
- Assign each tool a **Description** — this is the most critical configuration step

> ⚠️ **The Tool Description is what the LLM reads to decide which tool to use.** Write it clearly and specifically. A vague description leads to wrong routing.

**Step 3: Initialize the Router Agent**
- Use `RouterQueryEngine` as the orchestrator
- Provide the **Selector** (the LLM) and the **Toolset** (list of query engine tools)
- Enable `verbose=True` during development to observe the agent's internal reasoning

### 3.5 Execution Flow Example

```
Query: "What colors are available for AeroFlow?"
        ↓
Agent identifies "AeroFlow" as the key subject
        ↓
Matches subject to AeroFlow Tool via its description
        ↓
Triggers AeroFlow QueryEngine — ignores EcoSprint engine
        ↓
Returns: "Available colors: Coastal Blue, Sunset Orange"
```

### 3.6 Key Takeaways

| # | Takeaway |
|---|---|
| 1 | **Semantic routing understands meaning** — not just keywords. This separates an agentic router from hard-coded `if/else` logic. |
| 2 | **Tool Descriptions are your agent's configuration.** Routing quality depends entirely on how clearly each tool is described to the LLM. |
| 3 | **Always log the agent's reasoning during development.** Non-deterministic AI logic requires observability to debug and improve. |

---

---

## Chapter 4: Design Patterns for Agentic AI

Most agentic AI applications are built from one or more of these five core patterns. They are composable — a real system typically combines several of them to achieve a goal.

---

### 4.1 Reflection Pattern

The LLM acts as a **reviewer or judge** — critiquing output from itself, another LLM, or a human. Think of it as peer review, automated.

**How it works:**
- The LLM is given a set of evaluation **criteria** that guide what aspects to focus on
- It can call external tools (e.g., a RAG system) to verify facts during review
- Output can be **text feedback**, a **numeric score**, or a **pass/fail result**
- Feedback is sent back to the original generator LLM to improve its response
- The loop continues **iteratively** until the reviewer is satisfied

**Flow:**
```
User / client app sends Goal + Context
        ↓
Generator LLM produces initial response
        ↓
Reviewer LLM evaluates response against Goal + criteria
        ↓
Feedback sent back to Generator LLM → improved response
        ↓
(Loop repeats until reviewer is satisfied)
        ↓
Final response returned to user
```

> 💡 The reviewer LLM can be the same model critiquing its own output, or a completely separate model acting as an independent judge.

---

### 4.2 Router Pattern

The LLM acts as a **decision-maker**, choosing between alternate routes or actions. This is an intelligent, semantic `if/else`.

> *(We implemented this pattern hands-on in Chapter 3.)*

**How it works:**
- The agent holds a set of available routes/tools, each with a **capability profile/description**
- Given a goal or prompt, the LLM reads the profiles and selects the best-fit route
- The Orchestrator then executes the chosen route
- The quality of routing depends entirely on the clarity of the tool descriptions

**Routes can be:** different data sources, retrieval techniques, procedures, system integrations, or combinations of these.

**Flow:**
```
Client sends Goal to Routing Agent
        ↓
Agent sends Goal + all action profiles to LLM
        ↓
LLM selects the best-fit action
        ↓
Orchestrator executes the chosen route
```

> ⚠️ **Tool/route descriptions are the configuration.** Vague descriptions lead to wrong routing decisions.

---

### 4.3 Tool Use Pattern

The most popular pattern in agentic AI. The LLM decides **if**, **when**, and **how** to use a tool — including identifying the exact input parameters to pass.

**How it differs from Routing:**

| | Router Pattern | Tool Use Pattern |
|---|---|---|
| LLM decides | *Which* route to take | *Which* tool + *what parameters* to pass |
| Inputs | Goal only | Goal + parameter extraction from goal/context |
| Complexity | Simpler | More flexible and powerful |

**How it works:**
- Tools have capability profiles AND defined input parameters
- The LLM data-mines the goal and context history to extract the right parameter values
- The LLM may chain multiple tools in a specific sequence to achieve the goal
- The agent executes each tool call with the extracted inputs

**Flow:**
```
Agent receives Goal
        ↓
LLM decides which tool(s) to use and in what order
        ↓
LLM extracts the required input parameters from Goal / context
        ↓
Agent executes tools with the identified inputs
        ↓
Results returned and synthesized
```

---

### 4.4 Planning Pattern

For complex goals that require a **multi-step workflow**. The LLM breaks a high-level goal into subgoals, tasks, and subtasks — then maps each task to an available tool or action.

**Why planning is needed:**
- A complex goal cannot be achieved in a single step
- Each task may produce output that becomes the input to the next task
- Each task needs its own execution logic (routes, tools, or sub-agents)

**How it works:**
- The LLM analyzes the goal AND the available tools/actions
- It determines a workflow — the sequence of steps through which the goal can be achieved
- Planning **leverages the other patterns**: reflection, routing, tool use, and multi-agent interactions
- The Planner is adaptive — if a step fails, it can re-plan

**Structure:**
```
Goal
 └── Subgoal 1
      ├── Task 1.1 → Tool A
      └── Task 1.2 → Tool B
 └── Subgoal 2
      ├── Task 2.1 → RAG lookup
      └── Task 2.2 → API call
```

> 💡 Planning is the pattern that ties everything together — it is the "meta-pattern" that orchestrates all other patterns into a coherent workflow.

---

### 4.5 Multi-Agent Pattern

An **ensemble pattern** where multiple specialized agents collaborate to achieve a single complex goal. Mirrors how a human project team works.

**Why multi-agent systems are needed:**
- A single agent specializes in one domain — it cannot do everything
- Complex enterprise workflows require multiple skill sets
- Agents are reusable building blocks — one agent can participate in many multi-agent systems
- Mirrors **microservices architecture**: each agent has a well-defined responsibility, enabling separation of concerns and distributed design

**Agents can be:** built in-house, open source, or acquired from third parties.

**Example — E-commerce multi-agent system:**

```
Customer
  ├── Virtual Sales Agent
  │     ├── uses → Product Expert Agent (RAG on product catalog)
  │     └── uses → Sales Fulfillment Agent (orders, shipping, tracking)
  │
  └── Customer Support Agent
        ├── uses → Product Expert Agent (product feature questions)
        └── uses → Sales Fulfillment Agent (order status questions)
```

> 💡 The **Product Expert Agent** and **Fulfillment Agent** are independent, reusable agents. They are consumed by multiple other agents — just like reusable microservices.

---

### 4.6 Pattern Summary

| Pattern | LLM's Role | Key Characteristic |
|---|---|---|
| **Reflection** | Reviewer / judge | Iterative feedback loop until quality threshold is met |
| **Router** | Decision-maker | Semantic `if/else` — picks a route based on intent |
| **Tool Use** | Tool selector + param extractor | Most popular; LLM identifies inputs, not just which tool |
| **Planning** | Workflow designer | Breaks goal into subgoals → tasks → tools; meta-pattern |
| **Multi-Agent** | Coordinator | Ensemble of specialized agents; microservices analogy |

> 💡 A real agentic system typically combines **multiple patterns**. The choice of patterns depends on the specific use case and complexity of the goal.

---

*Personal learning notes — updated chapter by chapter as the course progresses.*
