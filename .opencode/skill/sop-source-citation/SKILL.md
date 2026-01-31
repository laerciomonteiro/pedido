---
name: sop-source-citation
description: "Mandatory source citation protocol (SOP-001) for all claims and references ensuring traceability and auditability of information. Outputs without proper citations are INVALID. Use when: (1) Making claims about code behavior, (2) Referencing documentation or specs, (3) Citing external sources, (4) Creating or updating YAML task files, (5) Writing documentation or reports, (6) Any statement that needs verification trail."
license: MIT
---

<!-- @META: SOP-001 Source Citation Protocol -->
<!--
    File: .opencode/skill/sop-source-citation/SKILL.md
    Version: 1.0.0
    Created: 2026-01-07
    Updated: 2026-01-07
    Scope: Mandatory traceability standard for all agent outputs
-->

# Skill: SOP-001 Source Citation Protocol

<!-- @NOTE(skill-def): Definition -->
> **MANDATORY PROTOCOL**: Every piece of information MUST cite its source. Outputs without proper citations are INVALID and will be REJECTED.

---

## Purpose

<!-- @NOTE(purpose): Purpose -->
Enable agents to navigate the codebase by maintaining a complete audit trail of information sources.
Every fact, task, decision, or reference MUST link back to its origin.

**Use when**:
- Creating or updating YAML task files
- Writing documentation or reports
- Making decisions that need justification
- Extracting information from source files
- Creating any deliverable that references other content

---

## Required Citation Formats

### 1. Markdown Documents (AGENTS.md, reports, docs)

```markdown
<!-- @REF(path/to/file.ts:42): Description of what this references -->
<!-- @CITE(AGENTS.md#rule-5): Why this decision was made -->
<!-- @SOURCE(wiki/TODO_MASTER.md:15-120): Origin of this information -->
```

### 2. YAML Files (backlog.yaml, sprint.yaml, configs)

```yaml
- id: "TASK-XXX"
  title: "Task Title"
  source: "path/to/origin/file.md"      # REQUIRED: Where this task came from
  source_line: 45                        # Recommended: Specific line number
  related_files:                         # REQUIRED: Files involved in this task
    - "src/module/file.ts"
    - "src/module/types.ts"
  spec: "docs/RFC-XXX.md"               # Optional: Specification document
```

### 3. Code Comments

```typescript
// @REF(AGENTS.md#sop-001): Following mandatory citation protocol
// @CITE(backend/TODO.md:23): Implementing billing fix from backlog
// @SOURCE(wiki/patterns/circuit-breaker.md): Pattern reference
```

### 4. Inline References (for navigation)

Use `file_path:line_number` format for clickable navigation:
```markdown
The billing service is defined in `backend/hub/src/billing/service.ts:45`.
See the circuit breaker pattern at `backend/packages/core/src/circuit-breaker.ts:12-89`.
```

---

## What MUST Be Cited

| Content Type | Required Citation | Example |
|--------------|-------------------|---------|
| **Tasks** | `source:` + `related_files:` | `source: "backend/TODO.md"` |
| **Decisions** | `@CITE` or `@WHY` | `<!-- @CITE(RFC-001): Chosen for X -->` |
| **Code changes** | `// @REF` comment | `// @REF(TASK-012)` |
| **Documentation** | `@REF` to source | `<!-- @REF(src/module.ts:42) -->` |
| **Audit findings** | `@SOURCE` with line | `<!-- @SOURCE(file:23-45) -->` |
| **Extracted TODOs** | Original file:line | `source_line: 23` |

---

## Valid vs Invalid Examples

```yaml
# INVALID (No source - agent cannot verify or navigate):
- id: "TASK-012"
  title: "Fix billing race condition"
  description: "There's a race condition in billing"

# VALID (Full traceability - agent can navigate):
- id: "TASK-012"
  title: "Fix billing race condition"
  source: "backend/TODO.md"
  source_line: 23
  related_files:
    - "backend/hub/src/billing/service.ts"
    - "backend/hub/src/billing/transaction.ts"
  description: |
    <!-- @REF(backend/TODO.md:23): Original TODO item -->
    Race condition identified in billing transaction atomicity.
```

---

## Compliance Checklist

Before completing ANY task, verify:

- [ ] All created/modified YAML files have `source:` fields
- [ ] All created/modified YAML files have `related_files:` arrays
- [ ] All documentation has `@REF` or `@CITE` annotations
- [ ] All extracted information links to origin `file:line`
- [ ] All decisions cite the rule or requirement that drove them
- [ ] All code changes reference the task/requirement in comments

---

## Enforcement

> **Outputs without source citations are INVALID.**
> - Agents MUST reject or fix any content lacking proper traceability
> - The orchestrator will reject deliverables that violate this SOP
> - Sub-agents must self-verify compliance before returning results

---

## Evolution

- v2.0.0 (2026-01-07): Adapted to Anthropic skill standard
- v1.0.0 (2026-01-07): Extracted from AGENTS.md into standalone skill, can be loaded by any agent that needs citation compliance
