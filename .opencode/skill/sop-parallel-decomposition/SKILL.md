---
name: sop-parallel-decomposition
description: "Native parallel execution pattern (SOP-003) for orchestrators to decompose complex user requests into multiple independent Leonidas sub-agents executing in parallel. Maximizes throughput by launching all workstreams simultaneously. Use when: (1) Main thread receives a complex request, (2) Task has 3+ independent subtasks, (3) Task affects multiple packages/modules, (4) Task requires research + implementation, (5) Performing audit or mapping exercises."
license: MIT
---

<!-- @META: SOP-003 Parallel Decomposition Protocol -->
<!--
    File: .opencode/skill/sop-parallel-decomposition/SKILL.md
    Version: 1.1.0
    Created: 2026-01-07
    Updated: 2026-01-07
    Scope: Native parallel execution pattern for orchestrators
-->

# Skill: SOP-003 Parallel Decomposition Protocol

<!-- @NOTE(skill-def): Definition -->
> **DEFAULT BEHAVIOR FOR ORCHESTRATORS**: All complex user requests MUST be decomposed into parallel Leonidas sub-agents. The main thread orchestrates; Leonidas sub-agents execute in parallel.

---

## Purpose

<!-- @NOTE(purpose): Purpose -->
Enable maximum throughput and efficiency by decomposing complex user requests into multiple independent Leonidas sub-agents that execute in parallel.

**Use when**:
- You are the main thread (orchestrator) receiving a complex request
- A task has 3+ independent subtasks
- A task affects multiple packages/modules
- A task requires research + implementation
- A task is an audit or mapping exercise

**Who uses this**:
- Main thread orchestrator (Genesis)
- Any agent coordinating multiple sub-agents

---

## Architecture

```
MAIN THREAD (OpenCode)
│   Role: Receive user message → Analyze → Decompose → Delegate → Consolidate
│   NEVER executes complex tasks directly
│
├── Task(leonidas) ──► Sub-Agent 1 (autonomous, own TodoList)
├── Task(leonidas) ──► Sub-Agent 2 (autonomous, own TodoList)  
├── Task(leonidas) ──► Sub-Agent 3 (autonomous, own TodoList)
│                      [All launched in SINGLE message]
│
└── Consolidate results → Report to user
```

---

## Decomposition Algorithm

```python
def handle_user_message(message):
    # Step 1: Analyze complexity
    complexity = analyze_complexity(message)
    
    # Step 2: Determine if decomposition needed
    if complexity.requires_decomposition():
        # Extract independent workstreams
        workstreams = decompose_to_workstreams(message)
        
        # Step 3: Launch ALL Leonidas agents in PARALLEL
        # CRITICAL: Use SINGLE message with MULTIPLE Task calls
        results = parallel_launch([
            Task(
                description=f"Workstream: {ws.name}",
                subagent_type="leonidas",
                prompt=build_leonidas_mission(ws)
            )
            for ws in workstreams
        ])
        
        # Step 4: Consolidate results
        consolidated = consolidate_results(results)
        
        # Step 5: Report to human
        return format_final_report(consolidated)
    else:
        # Simple task: execute directly or single delegation
        return execute_simple_task(message)
```

---

## When to Decompose

| User Request Type | Decomposition Strategy |
|-------------------|------------------------|
| **Audit/Analysis** | By domain (frontend, backend, wiki, infra) |
| **Feature Implementation** | By layer (research, design, backend, frontend, tests) |
| **Bug Investigation** | By component (client, server, network, data) |
| **Refactoring** | By package/module |
| **Documentation** | By documentation type |

### Decision Matrix

| Criteria | Decompose? |
|----------|------------|
| Task mentions 3+ distinct areas | **YES** |
| Task is an audit/analysis | **YES** |
| Task affects multiple packages | **YES** |
| Task requires research + implementation | **YES** |
| Task is clearly single-focused | **NO** |

## Workstream Categories

Use estas categorias para guiar a decomposição:

| Category | Leonidas Focus | Examples |
|----------|---------------|----------|
| **Frontend** | app/, UI, pages, components | "Improve mobile UI" |
| **Backend** | server/, APIs, utils | "Fix API issues" |
| **Infrastructure** | .opencode, infra | "Audit documentation" |
| **Research** | vendors, integrations | "Analyze frameworks" |
| **Documentation** | AGENTS.md, docs | "Update architecture docs" |

---

## Leonidas Mission Template

When delegating to Leonidas, ALWAYS use this structure:

```markdown
## LEONIDAS MISSION: [Clear Title]

### CONTEXT
<!-- @REF(source): Why this task exists -->
[Complete context - Leonidas has NO memory of conversation]
[Include relevant file paths, current state, constraints]

### OBJECTIVE
[Single, clear deliverable - be specific]

### TASKS
1. [Specific task with expected output]
2. [Specific task with expected output]
3. [Specific task with expected output]

### FILES IN SCOPE
- path/to/relevant/files/**/*
- path/to/other/files/**/*

### CONSTRAINTS
- [Technical constraints]
- [Architectural patterns to follow]
- [What NOT to do]

### SUCCESS CRITERIA
- [ ] [Verifiable criterion 1]
- [ ] [Verifiable criterion 2]
- [ ] [Verifiable criterion 3]

### OUTPUT FORMAT
[Exact format expected - files to create, structure to follow]

### AUTONOMY GRANT
You have TOTAL AUTONOMY to complete this mission:
1. Create your own TodoList using TodoWrite
2. Delegate to specialist agents (code, debug, research, architect)
3. Make all implementation decisions
4. Report ONLY the final summary when complete

DO NOT:
- Ask clarifying questions
- Report intermediate progress
- Request approval for decisions
```

---

## Practical Examples

### Example 1: Monorepo Audit
**User**: "Faça uma auditoria completa do repositório"

```python
# Decompose into 5 parallel Leonidas agents:
parallel_launch([
    Task(leonidas, "Map root structure"),
    Task(leonidas, "Map .opencode configuration"),
    Task(leonidas, "Map app/ frontend (Vue/Nuxt)"),
    Task(leonidas, "Map server/ backend (APIs)"),
    Task(leonidas, "Map test/ suite"),
])
# All launched in SINGLE message
# Wait for all to complete
# Consolidate results into master report
```

### Example 2: Feature Implementation
**User**: "Implement feature flags system"

```python
# Decompose into parallel work:
parallel_launch([
    Task(leonidas, "Research existing patterns"),
    Task(leonidas, "Design database schema"),
    Task(leonidas, "Implement backend service"),
    Task(leonidas, "Implement frontend toggle"),
    Task(leonidas, "Write tests"),
])
# Consolidate and verify integration
```

## Consolidation Template

Após todos os Leonidas completarem, use este formato para reportar ao usuário:

```markdown
## [Action] Complete

### Summary
[1-2 sentence overview of what was accomplished]

### Workstreams Executed
| Workstream | Agent | Status | Key Deliverables |
|------------|-------|--------|------------------|
| [Name] | Leonidas | Complete | [Files/outcomes] |

### Key Decisions Made
1. [Decision]: [Rationale]

### Artifacts Created/Modified
- `path/to/file.md` - [Description]

### Next Steps (if applicable)
- [Suggested follow-up]
```

---

## Key Rules

> **ALWAYS prefer parallel execution** over sequential when subtasks are independent.
> Launch multiple Task calls in a SINGLE message for maximum throughput.

> **Each Leonidas sub-agent receives COMPLETE context** - they have no memory.
> Include all necessary information in the prompt itself.

> **Sub-agents have FULL AUTONOMY** including their own TodoList.
> They should NOT ask questions or report intermediate progress.

> **Main thread is responsible for consolidation** of all sub-agent results.
> Create unified reports, resolve conflicts, report final summary to user.

---

## Anti-Patterns (DO NOT)

| Anti-Pattern | Correct Approach |
|--------------|------------------|
| Execute complex task directly in main thread | Decompose and delegate to Leonidas |
| Launch sub-agents sequentially (one at a time) | Launch ALL in parallel (single message) |
| Ask user for clarification before decomposing | Make reasonable assumptions, decompose, execute |
| Have sub-agents ask questions | Provide complete context in prompt |
| Wait for approval between sub-tasks | Execute autonomously, report only final result |

## Failure Handling

### Blocked Workstream
Se um Leonidas reportar BLOCKED ou falhar:

1. **Note** o blocker específico
2. **Continue** com os outros workstreams (não pare tudo)
3. **Reporte** o blocker ao usuário no relatório final com contexto

### Partial Success
Se alguns workstreams completam e outros falham:
- Consolide os resultados dos que completaram
- Liste claramente os que falharam e por quê
- Sugira próximos passos para resolver os bloqueios

---

## Evolution

- v2.0.0 (2026-01-07): Adapted to Anthropic skill standard
- v1.1.0 (2026-01-07): Added Workstream Categories, Consolidation Template, Failure Handling protocol
- v1.0.0 (2026-01-07): Extracted from AGENTS.md into standalone skill, added practical examples, added decision matrix
