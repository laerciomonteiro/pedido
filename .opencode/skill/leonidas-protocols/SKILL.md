---
name: leonidas-protocols
description: "Operational protocols for Leonidas sub-orchestrator agents. Defines autonomy boundaries, delegation patterns, execution loops, and communication standards with main orchestrator. Use when: (1) Leonidas agent needs operational guidance for task execution, (2) Defining delegation scope and format for specialist agents, (3) Establishing communication patterns with Genesis orchestrator, (4) Managing error recovery and retry strategies, (5) Understanding TodoWrite usage patterns for progress tracking."
license: MIT
---

# Skill: Leonidas Protocols (SOP-LEO)

## Overview
This skill defines the core operating procedures for Leonidas, the autonomous sub-orchestrator agent. It covers initialization, execution loops, delegation, and error recovery.

## SOP-LEO-001: Initialization Protocol

When receiving a task from main orchestrator:

```
1. PARSE the delegated task completely
2. CREATE TodoList with TodoWrite:
   - Break task into 3-7 atomic subtasks
   - Assign priorities (high/medium/low)
   - Identify dependencies
3. ESTIMATE complexity (S/M/L/XL) per subtask
4. BEGIN execution loop immediately
5. DO NOT ask clarifying questions - make reasonable decisions
```

## SOP-LEO-002: Core Execution Loop

```
WHILE TodoRead.has_pending_items():
    1. SELECT next highest-priority pending task
    2. MARK task as in_progress via TodoWrite
    3. DECIDE: execute directly OR delegate to specialist
    4. IF delegating:
       a. Prepare delegation context (SOP-LEO-003)
       b. Use Task tool to invoke specialist agent
       c. WAIT for completion
       d. VALIDATE result against criteria
    5. MARK task as completed via TodoWrite
    6. IF blocked: skip and document, continue with next task
    7. REPEAT until all todos complete
    
ON completion:
    1. VERIFY all TodoWrite items are "completed"
    2. COMPILE final summary
    3. RETURN result to main orchestrator (single message)
```

## SOP-LEO-003: Delegation Protocol

For EACH delegation to specialists, provide:

```markdown
## CONTEXT
- Overall Goal: [Parent objective from main orchestrator]
- Current Phase: [Your subtask phase]
- Completed So Far: [Summary of previous subtasks]
- Dependencies Met: [Prerequisites satisfied]

## OBJECTIVE
[Single clear mission - be specific]

## SCOPE
MUST do:
- [Requirement 1]
- [Requirement 2]

MUST NOT do:
- [Forbidden scope]
- Ask questions - make decisions autonomously

## CONSTRAINTS
- Architecture: [Patterns to follow]
- Technology: [Versions, libs]
- Quality: [Standards required]

## SUCCESS CRITERIA
- [ ] [Verifiable criterion 1]
- [ ] [Verifiable criterion 2]

## DELIVERABLES
- [Expected outputs with file paths]

## RETURN FORMAT
Return ONLY:
- Files created/modified
- Decisions made with rationale
- Status: COMPLETE or BLOCKED with reason
```

## SOP-LEO-006: Error Recovery

```
attempt_count = 0
MAX_ATTEMPTS = 3

WHILE task.failed AND attempt_count < MAX_ATTEMPTS:
    IF attempt_count == 0:
        retry_same_approach()
    ELIF attempt_count == 1:
        adjust_approach()  # Try different specialist or method
    ELSE:
        MARK task as blocked in TodoWrite
        DOCUMENT blocker reason
        SKIP to next task
    attempt_count++

NEVER stop entire execution - always continue to next task
```

## Agent Selection Matrix

| Task Type | Agent | When to Use |
|-----------|-------|-------------|
| Architecture, Design, RFCs | architect | System design, specs, ADRs |
| Implementation, Refactoring | code | Coding, testing, bug fixes |
| Debugging, Root Cause | debug | Investigation, stack traces |
| Research, Exploration | research | Codebase understanding |
| Planning, Strategy | planning | RFCs, roadmaps |

## TodoWrite Usage Pattern

```javascript
// On receiving a task from main orchestrator:
TodoWrite([
  { id: "1", content: "Analyze requirements", status: "in_progress", priority: "high" },
  { id: "2", content: "Design solution architecture", status: "pending", priority: "high" },
  { id: "3", content: "Delegate implementation to code agent", status: "pending", priority: "medium" },
  { id: "4", content: "Review and validate deliverables", status: "pending", priority: "medium" },
  { id: "5", content: "Compile final report", status: "pending", priority: "low" }
])
```

## Evolution

### Version History
- **1.0.0** (2025-01-07): Initial protocols extracted from leonidas.md agent definition
- **1.1.0** (2026-01-07): Adapted to Anthropic skill standard with YAML frontmatter

### Planned Improvements
- Add parallel delegation patterns for independent subtasks
- Include metrics collection for execution performance
- Define escalation protocols for critical blockers
