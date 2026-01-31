# Knowledge Management System (KMS)

<!-- @META: System-wide Knowledge Strategy & Map -->
<!--
    File: .opencode/specs/doctrine/KNOWLEDGE_MANAGEMENT.md
    Version: 2.1.0
    Created: 2026-01-07
    Updated: 2026-01-07
    Scope: Enterprise Knowledge Management System
    Status: Active
    Maintainer: Leonidas Sub-Orchestrator
-->

## 1. Knowledge Strategy

<!-- @NOTE(strategy-001): The "Why" behind our documentation -->
Documentation is not an afterthought—it is the **primary interface for Artificial Intelligence**. We invest in a rigorous Knowledge Management System (KMS) to achieve three strategic goals:

1.  **Context Maximization**: Reduce "hallucinations" by providing structured, semantic context to agents.
2.  **Autonomous Evolution**: Enable agents to learn new capabilities by reading the wiki, not just the code.
3.  **Token Efficiency**: Distilled knowledge consumes fewer tokens than raw code analysis.
4.  **Resilience**: Knowledge persists even if the team changes or agents are swapped.

### 1.1 The "Diamond Mining" Methodology
We treat information like ore. We must mine, refine, and polish it before it becomes valuable. This is our core value chain:

1.  **Prospecting (Discovery)**: Identifying high-value vendors, papers, and patterns.
2.  **Excavation (Ingestion)**: Dumping raw documentation into `vendors/`.
3.  **Refining (Mapping)**: Creating `index-*.md` files to structure the raw data.
4.  **Polishing (Distillation)**: Writing "Deep Dive" articles and "Best Practices" that agents can consume.
5.  **Jewelry (Implementation)**: Converting knowledge into executable code in `src/`.

---

## 2. Research Methodology (SOP-RES-001)

<!-- @RULE: Research Protocol -->
All research activities must follow this standardized protocol to ensure consistent quality.

### Phase 1: Ingestion
*Objective: Capture raw data without bias.*
- **Step 1**: Clone/Download vendor documentation to `vendors/[vendor-name]/`.
- **Step 2**: Create a `README.md` in that folder citing the version and date.
- **Step 3**: (Optional) Use an LLM to summarize the "Key Capabilities" immediately.

### Phase 2: Analysis
*Objective: Understand the mechanics.*
- **Step 1**: Identify "Core Primitives" (e.g., Chains in LangChain, Nodes in LlamaIndex).
- **Step 2**: Map these primitives to project concepts (e.g., does "Chain" = "Task"?).
- **Step 3**: Run "Hello World" examples to verify claims.
- **Step 4**: Document "Gotchas" and "limitations" explicitly.

### Phase 3: Synthesis
*Objective: Create teachable knowledge.*
- **Step 1**: Write a `wiki/index-[topic]/STRATEGY.md` file.
- **Step 2**: Define "Best Practices" for our specific use case.
- **Step 3**: Create decision trees (e.g., "When to use X vs Y").

---

## 3. Learning Paths & Expertise

<!-- @NOTE(levels-001): Standardized competency framework -->

### 3.1 Expertise Levels (L1-L5)
We measure knowledge maturity using the Dreyfus model adaptation. This applies to both Humans and Agents.

| Level | Designation | Definition | Capability | Evaluation |
|-------|-------------|------------|------------|------------|
| **L1** | **Awareness** | Knows the concept exists. | Can identify relevant keywords. | Can explain "What is it?" |
| **L2** | **Competence** | Understands basics & API. | Can implement "Hello World". | Can run basic examples. |
| **L3** | **Proficiency** | Grasps internals & patterns. | Can debug & optimize. | Can fix non-trivial bugs. |
| **L4** | **Expertise** | Deep architectural insight. | Can refactor & teach. | Can design new systems. |
| **L5** | **Mastery** | Industry-leading knowledge. | Can invent new patterns. | Cited by external peers. |

### 3.2 Learning Paths
Targeted tracks for different agent roles. Agents should "read" these paths to upgrade themselves.

#### 🟢 Path A: Agent Architect
*Focus: Orchestration, State, Cognitive Modeling*
1.  **Foundations**: Prompt Engineering (L3)
2.  **Core**: Context Engineering (L3)
3.  **Advanced**: Agent Frameworks (L4)
4.  **Mastery**: Cognitive Architectures (L3)

#### 🔵 Path B: System Engineer
*Focus: Protocols, Performance, Security*
1.  **Foundations**: MCP Protocol (L3)
2.  **Core**: Backend/Hub Architecture (L4)
3.  **Advanced**: WebRTC & Streaming (L3)
4.  **Mastery**: Distributed Systems (L3)

#### 🟣 Path C: Frontend Specialist
*Focus: UI, UX, Client State*
1.  **Foundations**: React & Hooks (L4)
2.  **Core**: Shell Architecture (L3)
3.  **Advanced**: Client-side AI integration (L2)
4.  **Mastery**: WASM & Performance (L3)

---

## 4. Standards & Processes

### 4.1 Quality Standards (SOP-QUA-001)
All documentation in the `wiki/` MUST meet these criteria (QS 1-10).
*Score < 7 requires immediate revision.*

- **[ ] Structured Metadata**: Includes `@META`, `@REF`, `@VERSION`.
- **[ ] Source Citations**: Every claim links to `vendors/` or external papers.
- **[ ] Semantic Notes**: Uses `<!-- @NOTE -->` and `<!-- @RULE -->` blocks.
- **[ ] Actionable**: Contains code examples or decision trees.
- **[ ] Up-to-date**: Reviewed within the last 3 months.
- **[ ] No Dead Links**: All internal links are valid.
- **[ ] Concise**: No fluff; high information density.
- **[ ] Visuals**: Uses Mermaid diagrams for complex flows.

### 4.2 Distillation Process (SOP-DIS-001)
How to turn `vendors/` into `wiki/`:

1.  **Scan**: Read vendor docs and TOC.
2.  **Index**: Create `wiki/index-[topic]/` with links to key vendor files.
3.  **Extract**: Copy critical patterns to a local `patterns.md`.
4.  **Synthesize**: Write a `deep-dive.md` analyzing pros/cons.
5.  **Codify**: Create `AGENTS.md` rules based on the findings.
6.  **Archive**: Move outdated vendor docs to `archive/` if no longer needed.

### 4.3 Vendor Evaluation Criteria
When selecting external tools/frameworks:
1.  **Type Safety**: First-class TypeScript/Python typing?
2.  **Observability**: Can we see what the agent is thinking?
3.  **Modularity**: Can we use just one part (e.g., memory) without the rest?
4.  **Prompt Control**: Do they hide the prompts or expose them? (We prefer exposed).
5.  **Community**: Is the discord active? Are issues closed?
6.  **Lock-in**: How hard is it to rip out?

---

## 5. Knowledge Map (Status & Metrics)

<!-- @REF(wiki/TODO_MASTER.md): Master backlog -->
<!-- @REF(wiki/TODO_SUPER_PLAN.md): Strategic roadmap -->

### 5.1 Prompt Engineering
<!-- @REF(wiki/index-prompt-engineering/) -->
> **Scope**: Optimizing communication with LLMs.

| Metric | Value | Notes |
|--------|-------|-------|
| **Maturity** | **L4 (Expert)** | We have mastered Chain-of-Thought and ReAct. |
| **Coverage** | 90% | Most major techniques documented. |
| **Quality** | 9/10 | High-quality examples and patterns. |
| **Status** | ✅ **Done** | Implemented in production. |
| **Value** | 🔴 **Critical** | Fundamental to all agents. |
| **Updated** | 2025-12 | |

**Key Files:**
- `01-zero-shot.md` to `03-chain-of-thought.md` (Basics)
- `06-react-framework.md`, `07-reflexion.md` (Advanced)
- `09-security-adversarial.md` (Security)
- `TECHNIQUES.md` (Cheatsheet)

### 5.2 Context Engineering
<!-- @REF(wiki/index-context-engineering/) -->
> **Scope**: Managing the "Context Window" resource.

| Metric | Value | Notes |
|--------|-------|-------|
| **Maturity** | **L3 (Proficient)** | Good grasp of retrieval and window management. |
| **Coverage** | 75% | Memory systems need more work. |
| **Quality** | 8/10 | Solid architectural diagrams. |
| **Status** | 🔄 **Implementing** | Designing Vector/Graph hybrid memory. |
| **Value** | 🔴 **Critical** | Enables long-running sessions. |
| **Updated** | 2026-01 | |

**Key Files:**
- `01-atoms.md`, `02-molecules-context.md` (Theory)
- `03-cells-memory.md` (Memory Structures)
- `STRATEGIES.md` (Optimization patterns)

### 5.3 Agent Frameworks
<!-- @REF(wiki/index-agents/) -->
> **Scope**: Runtimes and orchestration for agents.

| Metric | Value | Notes |
|--------|-------|-------|
| **Maturity** | **L2 (Competent)** | Broad survey, deep dive on Aider. |
| **Coverage** | 40% | Many frameworks, rapid changes. |
| **Quality** | 6/10 | Needs more comparative benchmarks. |
| **Status** | 🔬 **Researching** | Evaluating LangGraph vs Agno vs Custom. |
| **Value** | 🟠 **High** | Determines our OS runtime. |
| **Updated** | 2026-01 | |

**Analysis Roadmap:**
- **Aider**: L3 Deep Dive ✅ (Adopted patterns)
- **LangChain**: L2 Analysis 🔄 (Too heavy?)
- **LlamaIndex**: L2 Analysis 🔄 (Good for RAG)
- **Agno**: L2 Analysis 🔄 (Promising lightweight)
- **MCP**: L3 Implementation 🔄 (Standardizing)
- **Jarvis**: L1 Analysis ✅ (Reference)

### 5.4 Protocols & Standards
<!-- @REF(wiki/index-protocols/) -->
> **Scope**: Inter-agent communication (MCP, WebRTC).

| Metric | Value | Notes |
|--------|-------|-------|
| **Maturity** | **L3 (Proficient)** | Strong focus on MCP. |
| **Coverage** | 60% | WebRTC needs better docs. |
| **Quality** | 7/10 | MCP specs are good, custom protocols vague. |
| **Status** | 🔄 **Implementing** | Rolling out MCP servers. |
| **Value** | 🔴 **Critical** | The nervous system of the platform. |
| **Updated** | 2025-11 | |

---

## 6. Integration Roadmap

<!-- @NOTE(roadmap-001): From Wiki to Code -->

### Phase 1: Foundation (Q4 2025) ✅
- [x] Establish Prompt Engineering patterns (L4)
- [x] Select primary coding agent (Aider/OpenCode)
- [x] Define `AGENTS.md` standard

### Phase 2: Context & Memory (Q1 2026) 🔄
- [ ] Finalize Context Engineering specs (Reach L4)
- [ ] Implement Graph Memory in production
- [ ] Standardize MCP integration
- [ ] Build "Memory Visualizer" tool

### Phase 3: Autonomous Agency (Q2 2026) 🔮
- [ ] Select/Build Master Orchestrator Framework (Reach L3)
- [ ] Implement Multi-Agent collaboration patterns
- [ ] Enable "Agent-Reads-Docs" loop
- [ ] Self-healing code pipelines

### Phase 4: Cognitive Evolution (Q3 2026) 🔮
- [ ] Self-improving prompts
- [ ] Automatic documentation maintenance
- [ ] L5 Mastery in Agentic Systems
- [ ] "DayDream" mode for agents

---

## 7. Contribution Guide

### 7.1 How to Contribute
1.  **Identify a Gap**: Check `Knowledge Gaps` below.
2.  **Research**: Dig into `vendors/` or external papers.
3.  **Draft**: Create a markdown file in `wiki/drafts/`.
4.  **Review**: Ask an L3+ Architect to review.
5.  **Publish**: Move to `wiki/[topic]/` and link in `wiki-map.md`.

### 7.2 Knowledge Gaps (Wanted!)
- **GraphRAG**: How to combine Knowledge Graphs with Vector DBs?
- **Swarm Intelligence**: Patterns for 10+ agents?
- **Evaluation**: How to unit test an agent's personality?
- **Tool Use**: Best practices for fault-tolerant tool calling.
- **Voice Mode**: Handling audio streams in real-time.

---

## 8. Metrics & KPIs

To ensure our KMS remains healthy, we track:

1.  **Freshness Score**: % of docs updated in last 90 days (Target: >80%).
2.  **Coverage Score**: % of critical codebase covered by `AGENTS.md` (Target: 100%).
3.  **Distillation Ratio**: Ratio of `vendors/` (raw) to `wiki/` (refined). Target is 10:1 (High compression).
4.  **Agent Success Rate**: % of tasks completed without human help (proxy for doc quality).
5.  **Audit Compliance**: % of files meeting SOP-QUA-001 (Target: >95%).

---

## 9. Glossary

- **KMS**: Knowledge Management System.
- **SOP**: Standard Operating Procedure.
- **MCP**: Model Context Protocol.
- **RAG**: Retrieval Augmented Generation.
- **Distillation**: Process of refining raw info into knowledge.
- **Context Window**: The limited working memory of an LLM.

<!-- @END_OF_FILE -->
