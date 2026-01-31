---
name: sop-inline-audit
description: "Mandatory inline tagging protocol (SOP-002) for documenting bugs, technical debt, and vulnerabilities directly in code. Enables terminal-based discovery via grep/ripgrep with standardized tags (@BUG, @VULN, @SECURITY, @TECH-DEBT, @TODO, @FIXME, @PERF). Use when: (1) Performing code reviews, (2) Auditing existing code, (3) Identifying bugs during debugging, (4) Documenting technical debt, (5) Flagging security vulnerabilities."
license: MIT
---

<!-- @META: SOP-002 Inline Audit Protocol -->
<!--
    File: .opencode/skill/sop-inline-audit/SKILL.md
    Version: 1.0.0
    Created: 2026-01-07
    Updated: 2026-01-07
    Scope: Standardized inline tagging for terminal-based discovery
-->

# Skill: SOP-002 Inline Audit Protocol

<!-- @NOTE(skill-def): Definition -->
> **MANDATORY PROTOCOL**: Standardize how bugs, technical debt, and vulnerabilities are documented inline within the code. This enables both humans and agents to quickly locate issues using terminal tools (grep/rg).

---

## Purpose

<!-- @NOTE(purpose): Purpose -->
Standardize how findings are documented inline during code reviews, audits, and implementation.

**Use when**:
- Performing code reviews
- Auditing existing code
- Identifying bugs during debugging
- Documenting technical debt
- Flagging security vulnerabilities

---

## Required Tags

| Tag | Level | Purpose | Search Pattern |
|-----|-------|---------|----------------|
| `@BUG` | CRITICAL | Logic errors or broken functionality | `rg "@BUG"` |
| `@VULN` | CRITICAL | Security vulnerabilities | `rg "@VULN"` |
| `@SECURITY` | HIGH | Security concerns or hardcoded secrets | `rg "@SECURITY"` |
| `@TECH-DEBT` | MEDIUM | Suboptimal code or architectural debt | `rg "@TECH-DEBT"` |
| `@TODO` | MEDIUM | Pending features or improvements | `rg "@TODO"` |
| `@FIXME` | HIGH | Critical task that must be resolved | `rg "@FIXME"` |
| `@PERF` | MEDIUM | Performance bottlenecks | `rg "@PERF"` |

---

## Comment Format

Comments MUST follow this structure to be easily parsable:

```
// @TAG: Description [Issue ID/Link] (Found by: Agent/Human)
```

**Examples:**

```typescript
// @BUG: Race condition in user registration logic [TASK-123] (Found by: Leonidas)
// @VULN: Input is not sanitized before being passed to eval() (Found by: Architect)
// @TECH-DEBT: This class should be broken into smaller modules (Found by: CodeAgent)
// @SECURITY: API key hardcoded - move to environment variable (Found by: Code Review)
// @PERF: N+1 query in user list - should batch fetch (Found by: Debug)
// @FIXME: Temporary workaround for timezone issue - proper fix needed (Found by: Code)
// @TODO: Add comprehensive tests for edge cases (Found by: Code Review)
```

---

## Discovery via Terminal

Users and agents can find all occurrences using ripgrep or grep:

```bash
# Find all vulnerabilities
rg "@VULN"

# Find all bugs in a specific directory
rg "@BUG" vibeos-react/kernel/

# Find all critical issues
rg "@(BUG|VULN|SECURITY)"

# Summary count of all issues
rg --count-matches "@(BUG|VULN|SECURITY|TECH-DEBT|TODO)"

# Find issues with specific task ID
rg "@.*TASK-123"
```

---

## Agent Responsibilities

### During Code Review
- MUST insert these tags inline instead of just listing issues in reports
- MUST categorize by severity (CRITICAL > HIGH > MEDIUM)
- MUST include enough context for another agent to understand

### During Implementation
- MUST check for these tags in relevant files before starting work
- SHOULD address @FIXME and @BUG tags if in scope
- MUST NOT remove tags without fixing the underlying issue

### During Reporting
- MUST include a summary of tags added in session summary
- SHOULD group findings by severity level

---

## Severity Guide

| Severity | Tags | Action Required |
|----------|------|-----------------|
| **CRITICAL** | `@BUG`, `@VULN` | Fix immediately, blocks merge |
| **HIGH** | `@SECURITY`, `@FIXME` | Fix before merge if possible |
| **MEDIUM** | `@TECH-DEBT`, `@PERF`, `@TODO` | Track in backlog |

---

## Integration with Code Review Skill

When using the `code-review` skill, all findings MUST be:
1. Documented inline using the appropriate tag
2. Summarized in the review output
3. Categorized by severity

---

## Evolution

- v2.0.0 (2026-01-07): Adapted to Anthropic skill standard
- v1.0.0 (2026-01-07): Extracted from AGENTS.md into standalone skill, added agent responsibility matrix, added severity guide
