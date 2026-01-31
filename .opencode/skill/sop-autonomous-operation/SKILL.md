---
name: sop-autonomous-operation
description: "Critical protocol enabling agents to operate autonomously for extended periods (hours) without human intervention. Defines continuous execution loops, task discovery, delegation patterns, and session persistence. Use when: (1) Receiving complex multi-step tasks, (2) Operating in long-running sessions, (3) Delegating to sub-agents, (4) Managing task queues and backlogs, (5) Any agent requires autonomous execution capability."
license: MIT
---

<!-- @META: Autonomous Operation Protocol -->
<!--
    File: .opencode/skill/sop-autonomous-operation/SKILL.md
    Version: 1.0.0
    Created: 2026-01-07
    Updated: 2026-01-07
    Scope: Critical instructions for autonomous agent operation
-->

# Skill: Autonomous Operation Protocol

<!-- @NOTE(skill-def): Definition -->
> **CRITICAL**: These rules enable agents to operate autonomously for extended periods (hours) without human intervention. This is the DEFAULT mode of operation.

---

## Purpose

<!-- @NOTE(purpose): Purpose -->
Define how agents should behave when operating autonomously for extended periods.

**Use when**:
- Receiving complex multi-step tasks
- Operating in long-running sessions
- Delegating to sub-agents
- Managing task queues and backlogs

**Who uses this**:
- Main thread orchestrator
- Leonidas sub-orchestrators
- Any agent with autonomous execution capability

---

## Default Autonomous Behavior

> **ALWAYS BE AUTONOMOUS**: The agent operates in autonomous mode by default.

When receiving ANY task:
1. **Immediately start working** - Do not ask for clarification unless truly ambiguous
2. **Break down into subtasks** - Use TodoWrite to plan and track progress
3. **Execute continuously** - Do not stop until ALL subtasks are complete
4. **Report only at completion** - No intermediate progress reports unless blocked
5. **Find more work** - When done, check backlog for next tasks

---

## Core Autonomy Principles

### Continuous Execution Loop

> **Never Stop Working**: Continue executing until ALL subtasks are complete.

```
WHILE task_list.has_pending_items:
    1. SELECT next highest-priority task
    2. EXECUTE task (delegate if complex)
    3. MARK task complete immediately
    4. FIND next task automatically
    5. CONTINUE without stopping
    6. REPEAT until backlog is empty
```

### Proactive Task Discovery

> **Find Work Without Being Asked**:

1. Check `.opencode/tasks/backlog.yaml` for pending tasks
2. Search for TODOs/FIXMEs in recently modified files
3. Look for missing AGENTS.md files in directories with code
4. Identify security issues, race conditions, or bugs
5. Improve documentation and test coverage
6. Update `.opencode/context/session.yaml` with findings

### Memory Management via Delegation

> **Delegate to Preserve Context**:

```
IF task.complexity > MEDIUM OR task.files > 5:
    DELEGATE to specialized subagent
    WAIT for completion
    INTEGRATE result
    CONTINUE to next task
```

### No Blocking Behaviors

> **Avoid Blocking Patterns**: The agent MUST NOT:

- Stop to ask clarifying questions (make reasonable decisions instead)
- Wait for approval on non-critical decisions
- Pause to report progress (log to `.opencode/context/` instead)
- Request confirmation before proceeding
- Ask "should I continue?" (the answer is always YES)
- Stop after completing one task (find the next task)

---

## Multi-Hour Session Protocol

> **For sessions lasting hours**:

```
SESSION_START:
    1. READ .opencode/BOOTSTRAP.md (if exists)
    2. READ .opencode/context/session.yaml to resume state
    3. READ TodoWrite to see pending tasks
    4. CONTINUE from last checkpoint

DURING_SESSION:
    1. UPDATE session.yaml every 30 minutes
    2. LOG decisions to decisions.log
    3. DELEGATE complex tasks to subagents
    4. MARK tasks complete IMMEDIATELY when done
    5. NEVER wait for human input

SESSION_END (when backlog empty):
    1. UPDATE session.yaml with final state
    2. SUMMARIZE work done in decisions.log
    3. IDENTIFY potential future work
    4. REPORT final summary to human
```

---

## Task Execution Standards

### Task Breakdown

Before executing any non-trivial task:

```yaml
task_analysis:
  1. Identify all subtasks (minimum 3 for complex tasks)
  2. Order by dependencies
  3. Estimate complexity (LOW/MEDIUM/HIGH)
  4. Decide: execute directly OR delegate to subagent
  5. Track in TodoWrite tool

delegation_threshold:
  - Files > 5: DELEGATE
  - Complexity HIGH: DELEGATE  
  - Duration > 10 minutes: DELEGATE
  - Cross-module changes: DELEGATE
```

### Parallel Execution

> **Execute Independent Tasks in Parallel**:

```
IF tasks A, B, C are independent:
    LAUNCH subagent for A
    LAUNCH subagent for B  
    LAUNCH subagent for C
    WAIT for all to complete
    INTEGRATE results
    CONTINUE
```

---

## Delegation Protocol

For long-running autonomous sessions, use this delegation pattern:

```markdown
## SUBAGENT MISSION

### CONTEXT
[What the parent task is about - be comprehensive]

### OBJECTIVE  
[Single clear deliverable - be specific]

### SCOPE
MUST do: [explicit requirements]
MUST NOT do: [explicit exclusions]
Files in scope: [list specific files/directories]

### CONSTRAINTS
- Do NOT ask questions - make reasonable decisions
- Do NOT stop for approval - proceed autonomously
- Do NOT report intermediate progress - only final result

### SUCCESS CRITERIA
- [ ] [Verifiable criterion 1]
- [ ] [Verifiable criterion 2]

### RETURN FORMAT
Return ONLY a summary with:
- Files created/modified (with paths)
- Decisions made (with rationale)
- Status: COMPLETE or BLOCKED with specific reason
```

---

## Session Persistence

> **Persist State Continuously**:

During autonomous operation, the agent MUST update:
- `.opencode/context/session.yaml` - Current progress and checkpoint
- `.opencode/context/decisions.log` - All significant decisions made
- `.opencode/tasks/sprint.yaml` - Task status changes
- `TodoWrite` tool - Mark tasks complete immediately

This ensures that if a session is interrupted, the next session can resume seamlessly.

---

## Error Recovery

> **Errors do NOT stop the loop**:

```
ON error:
    1. LOG error to decisions.log with full context
    2. ATTEMPT alternative approach (max 3 attempts)
    3. IF still failing: 
       - SKIP task and document blocker
       - ADD to backlog for later review
    4. CONTINUE with next task immediately
    5. NEVER stop the entire execution loop
    6. NEVER ask human for help on recoverable errors
```

---

## Autonomy Checklist

Before stopping or asking the human anything, verify:

- [ ] Is the backlog empty? If not, continue working
- [ ] Are there TODOs in the codebase? If yes, address them
- [ ] Are there missing AGENTS.md files? If yes, create them
- [ ] Are there failing tests? If yes, fix them
- [ ] Is documentation up to date? If not, update it
- [ ] Has session.yaml been updated? If not, update it

**Only stop when ALL boxes are checked or explicitly blocked.**

---

## Evolution

- v2.0.0 (2026-01-07): Adapted to Anthropic skill standard
- v1.0.0 (2026-01-07): Extracted from AGENTS.md into standalone skill, added session persistence protocol, added autonomy checklist
