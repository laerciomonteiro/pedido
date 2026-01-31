---
name: manage-task-boundaries
description: "Manage mode transitions and progress communication in complex tasks using the task_boundary protocol. Ensures structured progression through PLANNING, EXECUTION, and VERIFICATION phases with clear user communication. Use when: (1) Starting complex tasks with more than 5 tool calls, (2) Transitioning between planning/execution/verification phases, (3) Need to communicate progress to user during long operations, (4) Managing multi-phase work with clear milestones."
license: MIT
---

# Skill: Manage Task Boundaries

> Manage mode transitions and progress communication in complex tasks using the task_boundary protocol.

---

## Purpose

Guide the correct use of the `task_boundary` protocol to keep users informed of progress, organize work in clear phases, and ensure effective communication in complex tasks.

**Use when**:
- Starting complex tasks (>5 tool calls)
- Transitioning between planning/execution/verification
- Need to communicate progress to user
- Managing multi-phase work

---

## Approaches

### Quick
For simple tasks (<5 tool calls):
- Skip task_boundary protocol
- Use direct communication

### Complete
For complex tasks:
1. Initialize with PLANNING mode
2. Transition through phases
3. Update every ~5 tool calls
4. Complete with notify_user

### Incremental
For very large tasks:
- Multiple task boundaries
- Progress tracking in task.md
- Regular user checkpoints

---

## Main Prompt

```
Implement **structured task management** using the task_boundary protocol for complex tasks.

## Task Details
- **Task Name**: [Descriptive name for the task]
- **Estimated Complexity**: [Number of tool calls expected]

---

## Phase 1: Task Initialization (PLANNING Mode)

1.  **Call task_boundary** with:
    -   TaskName: Descriptive, properly capitalized
    -   Mode: PLANNING
    -   TaskSummary: Initial goal description
    -   TaskStatus: "Analyzing requirements and creating plan"
    -   PredictedTaskSize: Estimated tool calls

2.  **Create/Update task.md**:
    -   [ ] uncompleted tasks
    -   [/] in progress tasks
    -   [x] completed tasks

3.  **Create implementation_plan.md** if needed

4.  **Request User Approval** via notify_user if plan is non-trivial

---

## Phase 2: Execution (EXECUTION Mode)

1.  **Transition Mode**: Call task_boundary with Mode: EXECUTION

2.  **Update Periodically** (every ~5 tools):
    -   TaskSummary: What has been accomplished (PAST TENSE)
    -   TaskStatus: What you're about to do (FUTURE TENSE)

3.  **Mark Progress** in task.md:
    -   Change [ ] to [/] when starting
    -   Change [/] to [x] when complete

4.  **Handle Backtracking**:
    -   If discovery requires replanning, switch back to PLANNING
    -   Keep same TaskName, update Summary to explain

---

## Phase 3: Verification (VERIFICATION Mode)

1.  **Transition Mode**: Call task_boundary with Mode: VERIFICATION

2.  **Run Validations**:
    -   Tests: `pnpm test`
    -   Build: `pnpm build`
    -   Manual checks as needed

3.  **Create walkthrough.md**:
    -   What was accomplished
    -   What was tested
    -   Results

---

## Phase 4: Completion

1.  **Final task_boundary Update**:
    -   Complete TaskSummary with all accomplishments
    -   TaskStatus: "Finalizing and notifying user"

2.  **Call notify_user**:
    -   Concise summary (not redundant with artifacts)
    -   PathsToReview for artifacts requiring review
    -   BlockedOnUser: true only if approval needed

---

## Constraints
-   **Never skip task_boundary** for complex tasks
-   **Update every ~5 tools** (not every single tool)
-   **TaskStatus != TaskSummary**: Status is future, Summary is past
-   **Use %SAME%** when values don't change
```

---

## Key Rules

### TaskName
- Properly capitalized, human-readable
- Describes a reasonable chunk of work
- Changes when switching to fundamentally different activity

### TaskSummary (PAST TENSE)
- "Analyzed X, implemented Y, fixed Z"
- Cumulative: adds to previous summary
- Specific: mentions files/functions edited

### TaskStatus (FUTURE TENSE)
- "Creating tests for module"
- Describes NEXT steps, not previous
- Broad enough for ~5 tool calls

---

## Mode Transitions

```
PLANNING -> EXECUTION: After plan approved or simple enough
EXECUTION -> VERIFICATION: After implementation complete
EXECUTION -> PLANNING: If unexpected complexity discovered
VERIFICATION -> EXECUTION: If bugs found during testing
```

### State Diagram

```
     ┌──────────────┐
     │   PLANNING   │
     └──────┬───────┘
            │ plan approved
            ▼
     ┌──────────────┐     unexpected
     │  EXECUTION   │◄────complexity
     └──────┬───────┘         │
            │ complete        │
            ▼                 │
     ┌──────────────┐         │
     │ VERIFICATION │─────────┘
     └──────┬───────┘  bugs found
            │ passed
            ▼
        [COMPLETE]
```

---

## Expected Deliverables

1. Consistent task_boundary calls throughout task
2. Updated task.md with progress markers
3. Clear mode transitions
4. Final notify_user with summary

---

## Skill Chaining

### Can Be Called By
- Any skill that involves multi-phase work

### Chains To
- None (this is a meta-process skill)

---

## Evolution

### v2.0.0 (2026-01-07)
- Adapted to Anthropic standard frontmatter
- Removed version/category/complexity/estimated_time/chains_to from frontmatter
- Description enriched with "Use when" triggers

### v1.0.0 (2026-01-06)
- Migrated from .skills/ to .opencode/skill/
- Added approaches section
- Added state diagram
- Enhanced with clear examples
