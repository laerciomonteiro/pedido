# Orchestration Architecture Specs

<!-- @META: Orchestration Specs -->
<!--
    File: .opencode/specs/ORCHESTRATION_ARCHITECTURE.md
    Version: 2.0.0
    Created: 2026-01-06
    Updated: 2026-01-07
    Scope: Orchestration guidelines and model architecture
-->

<!-- @NOTE(orch-def): Definition -->
> Specification document for OpenCode agent orchestration architecture.

## 1. Context and Purpose

<!-- @NOTE(context): Context -->
This document describes the orchestration architecture for the OpenCode agent system.

**Main Objective**: Define the hierarchical orchestration model for AI agents working on complex software projects.

---

## 2. Three-Tier Architecture

<!-- @REF(.opencode/agent/orchestrator.md): Main Thread Definition -->
<!-- @REF(.opencode/BOOTSTRAP.md#agent-hierarchy): Full Hierarchy Diagram -->

### 2.1 Hierarchy Overview

```
+---------------------------------------------------------------------+
|                         TIER 1: HUMAN INTERFACE                      |
|                                                                       |
|  Human <-> Main Orchestrator (Main Thread)                           |
|  - Receives directions from user                                      |
|  - Decomposes into parallel workstreams                               |
|  - Consolidates results                                               |
|  - Reports to user                                                    |
+-----------------------------------+---------------------------------+
                                    |
             PARALLEL DECOMPOSITION (SOP-003)
                                    |
+-----------------------------------+-----------------------------------+
|                      TIER 2: SUB-ORCHESTRATION                        |
|                                                                       |
|  +--------------+  +--------------+  +--------------+                 |
|  |   LEONIDAS   |  |   LEONIDAS   |  |   LEONIDAS   |                 |
|  |  Instance 1  |  |  Instance 2  |  |  Instance N  |                 |
|  |              |  |              |  |              |                 |
|  | - TodoList   |  | - TodoList   |  | - TodoList   |                 |
|  | - Autonomy   |  | - Autonomy   |  | - Autonomy   |                 |
|  +------+-------+  +------+-------+  +------+-------+                 |
|         |                 |                 |                         |
+---------+-----------------+-----------------+-------------------------+
          |                 |                 |
+---------+-----------------+-----------------+-------------------------+
|                       TIER 3: SPECIALISTS                             |
|                                                                       |
|  +----------+ +----------+ +----------+ +----------+ +----------+     |
|  | Architect| | Planning |  |   Code   | | Research |  |  Debug   |   |
|  |   Opus   | |   Opus   |  |   Opus   | |  Flash   |  |   Opus   |   |
|  +----------+ +----------+ +----------+ +----------+ +----------+     |
|                                                                       |
|  Execute specific tasks, return results to Leonidas                   |
+-----------------------------------------------------------------------+
```

### 2.2 Model Distribution

<!-- @SCHEMA: Model Distribution -->
| Role | Agent | Model | Justification |
|------|-------|-------|---------------|
| **Main Thread** | Orchestrator | Claude Opus | Human interface, strategic decomposition |
| **Sub-Orchestrator** | Leonidas | Gemini Pro | 1M token context, autonomous execution |
| **Planning** | Planning | Claude Opus | Deep reasoning, strategic decisions |
| **Architecture** | Architect | Claude Opus | System design, specs only (no code) |
| **Programming** | Code | Claude Opus | Code quality, implementation |
| **Research** | Research | Gemini Flash | Speed, rapid exploration |
| **Debugging** | Debug | Claude Opus | Root cause analysis |

### 2.3 Layer Responsibilities

<!-- @SCHEMA: Layer Responsibilities -->
| Layer | Primary Responsibility | Should NOT Do |
|-------|------------------------|---------------|
| **Orchestrator** | Talk to user, decompose, consolidate | Execute complex tasks directly |
| **Leonidas** | Orchestrate specialists, manage TodoList | Talk to user directly |
| **Specialists** | Execute atomic tasks | Orchestrate other agents |

---

## 3. Orchestration Principles

<!-- @RULE: Orchestration Principles -->
1. **Parallel Decomposition (SOP-003)**: Complex requests MUST be decomposed into multiple parallel Leonidas instances
2. **Hierarchical Delegation**: Orchestrator -> Leonidas -> Specialists (never skip levels)
3. **Complete Context**: Each delegated agent receives complete context (has no memory)
4. **Total Autonomy**: Sub-agents don't ask questions, they execute
5. **Report Only Final**: Only final results go up to the level above
6. **Maximum Parallelization**: Independent tasks MUST run in parallel

---

## 4. Delegation Patterns

### 4.1 Orchestrator -> Leonidas

<!-- @SCHEMA: Leonidas Mission Template -->
```markdown
## LEONIDAS MISSION: [Title]

### CONTEXT
[Complete context - Leonidas has no memory of conversation]

### OBJECTIVE
[Single clear deliverable]

### TASKS
1. [Specific task]
2. [Specific task]

### SUCCESS CRITERIA
- [ ] [Verifiable criterion]

### AUTONOMY GRANT
You have TOTAL AUTONOMY:
- Create your own TodoList
- Delegate to specialists
- Report ONLY the final summary
```

### 4.2 Leonidas -> Specialists

<!-- @SCHEMA: Specialist Delegation -->
```markdown
## TASK: [Title]

### CONTEXT
[Specific context for the task]

### DELIVERABLE
[What must be delivered]

### CONSTRAINTS
[Limitations and patterns to follow]
```

---

## 5. Documentation Sources

<!-- @NOTE(sources): Sources -->
### 5.1 Key Documents
<!-- @REF(.opencode/agent/orchestrator.md): Main Thread Definition -->
<!-- @REF(.opencode/BOOTSTRAP.md): Session Initialization -->

---

## 6. Changelog

| Date | Change | Author |
|------|--------|--------|
| 2026-01-07 | **v2.0.0**: Generalized for template use | OpenCode |
| 2026-01-07 | v1.1.0: 3-tier architecture with Orchestrator | OpenCode |
| 2026-01-06 | v1.0.0: Initial document | OpenCode |
