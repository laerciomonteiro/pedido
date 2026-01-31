---
name: sop-code-review-structure
description: "Standardized structure and location for all code review and audit reports (SOP-004). Ensures findings are persistent, discoverable, and machine-readable with consistent file naming, severity levels, and report templates. Use when: (1) Conducting code reviews, (2) Performing security audits, (3) Creating audit reports, (4) Documenting code quality findings, (5) Establishing review documentation standards."
license: MIT
---

<!-- @META: SOP-004 Code Review Structure Protocol -->
<!--
    File: .opencode/skill/sop-code-review-structure/SKILL.md
    Version: 1.0.0
    Created: 2026-01-07
    Updated: 2026-01-07
    Scope: Standardized structure and location for code review reports
-->

# Skill: SOP-004 Code Review Structure

<!-- @NOTE(skill-def): Definition -->
> **MANDATORY PROTOCOL**: Standardizes the location, structure, and naming conventions for all code review and audit reports. Ensures findings are persistent, discoverable, and machine-readable.

---

## 1. File Locations & Naming

<!-- @RULE: Storage Locations -->
All review reports MUST be saved in `.opencode/reviews/`.

| Review Type | Path Pattern | Example |
|-------------|--------------|---------|
| **Session Review** | `.opencode/reviews/YYYYMMDD-{scope}-review.md` | `.opencode/reviews/20260107-auth-module-review.md` |
| **Full Audit** | `.opencode/reviews/audit/{package}-audit.md` | `.opencode/reviews/audit/vibeos-kernel-audit.md` |

**Path Rules:**
1. Use `YYYYMMDD` prefix for dated reviews.
2. Use kebab-case for scopes (e.g., `auth-module`, `api-gateway`).
3. Package audits go in `audit/` subdirectory and are updated over time (not dated).

---

## 2. Inline Tags Alignment

<!-- @REF(sop-inline-audit): Tag Definitions -->
This skill extends **SOP-002 (Inline Audit)**. Agents MUST use these tags inline during the review process.

| Tag | Severity | Meaning |
|-----|----------|---------|
| `@BUG` | CRITICAL | Logic error, crash, or broken functionality |
| `@VULN` | CRITICAL | Security vulnerability |
| `@SECURITY` | HIGH | Security weakness (not yet exploitable) |
| `@FIXME` | HIGH | Critical workaround that needs proper fix |
| `@REFACTOR` | MEDIUM | Code structure improvement needed |
| `@PERF` | MEDIUM | Performance optimization needed |
| `@TEST-NEEDED`| MEDIUM | Missing test coverage |
| `@TODO` | LOW | Future feature or non-urgent task |

---

## 3. Severity Levels

<!-- @RULE: Severity Hierarchy -->
Classify findings in reports using this hierarchy:

*   **CRITICAL**: Breaks production, security breach, data loss. *Must fix immediately.*
*   **HIGH**: Major functionality issue, significant tech debt, security risk. *Fix before release.*
*   **MEDIUM**: Minor bug, optimization, code style, missing tests. *Schedule in backlog.*
*   **LOW**: Nitpick, typos, suggestion. *Fix when convenient.*

---

## 4. Review Report Template

<!-- @TEMPLATE: Review Report -->
Use the following Markdown structure for all reports.

```markdown
# Code Review: [Scope/Component Name]

<!-- @META: Review Metadata -->
- **Date**: YYYY-MM-DD
- **Reviewer**: [Agent Name/Model]
- **Target**: [File paths or Module name]
- **Type**: [Security Audit | Refactoring | General Review]

## 1. Executive Summary
[Brief summary of overall health, key risks, and recommendation (Pass/Fail/Warn)]

## 2. Severity Summary
| Level | Count |
|-------|-------|
| 🔴 CRITICAL | 0 |
| 🟠 HIGH | 0 |
| 🟡 MEDIUM | 0 |
| 🔵 LOW | 0 |

## 3. Detailed Findings

### 🔴 CRITICAL
- **[Issue ID]**: [Title of issue]
  - **Location**: `path/to/file.ts:line`
  - **Description**: [Detailed description]
  - **Recommendation**: [How to fix]

### 🟠 HIGH
- ...

### 🟡 MEDIUM
- ...

## 4. Architectural Observations
- [Observation about patterns, structure, or state management]

## 5. Action Items
- [ ] [Action 1]
- [ ] [Action 2]
```

---

## 5. Workflow

1. **Scan**: Read code and identify issues.
2. **Tag**: Add inline comments (`@BUG`, etc.) in the code files (if creating a PR) or note them for the report (if read-only).
3. **Draft**: Create the report file using the template above.
4. **Save**: Save to the appropriate location in `.opencode/reviews/`.
5. **Summarize**: Present the Executive Summary to the user in the chat.

---

## Evolution

- v2.0.0 (2026-01-07): Adapted to Anthropic skill standard
- v1.0.0 (2026-01-07): Initial extraction from AGENTS.md into standalone skill
