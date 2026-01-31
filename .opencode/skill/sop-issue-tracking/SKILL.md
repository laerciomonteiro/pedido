---
name: sop-issue-tracking
description: "Standard Operating Procedure for tracking, managing, and resolving issues in the project. Unifies ISSUE_CATALOG.md, detailed docs in .opencode/issues/, and inline code tags into a coherent system with defined lifecycle, severity levels, and templates. Use when: (1) Identifying and logging new issues, (2) Tracking issue lifecycle from open to resolved, (3) Documenting bugs with reproduction steps, (4) Linking code issues to catalog entries, (5) Maintaining issue documentation."
license: MIT
---

# Skill: Issue Tracking SOP

## 1. Overview
This skill defines the standard process for issue tracking within the Sonaris project. It unifies the `ISSUE_CATALOG.md`, detailed docs in `.opencode/issues/`, and inline code tags.

## 2. Issue Structure

### 2.1 File Organization
- **Central Catalog**: `ISSUE_CATALOG.md` (Root) - High-level index of all issues.
- **Detailed Docs**: `.opencode/issues/ISSUE-XXX.md` - In-depth context, reproduction steps, and analysis.
- **Inline Tags**: `// @TODO(ISSUE-XXX): description` or `// @BUG(ISSUE-XXX): description` in source code.

### 2.2 Issue Schema
Every issue must adhere to the following schema (YAML/JSON compatible):

```yaml
issues:
  - id: ISSUE-XXX                 # Format: ISSUE-001, ISSUE-020
    title: "Descriptive Title"    # Clear and concise
    type: bug                     # Options: bug, feature, improvement, security
    severity: high                # Options: critical, high, medium, low
    status: open                  # Options: open, investigating, fixing, resolved, wontfix
    created: YYYY-MM-DD           # YYYY-MM-DD
    resolved: null                # YYYY-MM-DD (when status is resolved)
    resolution: null              # Brief description of the fix
    related_files:                # List of files primarily affecting or affected by the issue
      - path/to/file.ts
    related_commits: []           # List of commit hashes that fixed the issue
```

## 3. Classifications

### 3.1 Types
| Type | Description |
|------|-------------|
| **bug** | Functionality not working as intended. |
| **feature** | New functionality or significant addition. |
| **improvement** | Enhancement to existing code, performance, or UX. |
| **security** | Vulnerability or security risk. |

### 3.2 Severity
| Level | Description |
|-------|-------------|
| **critical** | System breakage, data loss, or blocking main workflow. Immediate action required. |
| **high** | Major functionality broken or significant performance degradation. |
| **medium** | Minor functionality broken or cosmetic issues affecting UX. |
| **low** | Trivial issues, typos, or suggestions. |

## 4. Lifecycle & Workflow

The issue lifecycle follows these states:

1.  **Open**: Issue identified and logged in `ISSUE_CATALOG.md`.
2.  **Investigating**: Agent/Dev is analyzing the root cause. Create detailed doc if needed.
3.  **Fixing**: Solution is being implemented.
4.  **Resolved**: Fix merged, tests passed.
5.  **Wontfix**: Decision made not to address the issue (provide rationale).

### 4.1 Process

#### Step 1: Identification
- Identify the problem.
- Search `ISSUE_CATALOG.md` to ensure no duplicate exists.
- Assign next available ID (e.g., `ISSUE-021`).

#### Step 2: Documentation
- Add entry to `ISSUE_CATALOG.md`.
- (Optional but recommended for complex issues) Create `.opencode/issues/ISSUE-XXX.md`.
- If found in code during audit, add inline tag: `// @BUG(ISSUE-XXX): Description`.

#### Step 3: Resolution
- Implement fix.
- Ensure commit message references issue: `Fixes ISSUE-XXX` or `Ref ISSUE-XXX`.
- Run tests.

#### Step 4: Closing
- Update `ISSUE_CATALOG.md`:
    - Change `status` to `resolved`.
    - Set `resolved` date.
    - Update `resolution` summary.
    - Add `related_commits`.
- Update/Close `.opencode/issues/ISSUE-XXX.md` if it exists.
- Remove or update inline tags in code (e.g., change `@BUG` to `@NOTE` regarding the fix, or remove entirely if cleaner).

## 5. Templates

### 5.1 Catalog Entry (Markdown Table Row)
```markdown
| ID | Title | Type | Severity | Status | Created |
|----|-------|------|----------|--------|---------|
| ISSUE-XXX | Title here | bug | medium | open | 2024-01-01 |
```

### 5.2 Detailed Issue Document (ISSUE-XXX.md)
```markdown
# ISSUE-XXX: [Title]

## Meta
- **Type**: bug
- **Severity**: medium
- **Status**: open
- **Created**: 2024-01-01

## Description
Detailed description of the issue.

## Reproduction
1. Step 1
2. Step 2

## Analysis
Root cause analysis...

## Proposed Solution
Plan to fix...
```

## 6. Maintenance
- Periodically synchronize `ISSUE_CATALOG.md` with codebase state.
- Check for resolved issues that are still marked 'open'.

---

## Evolution

- v2.0.0 (2026-01-07): Adapted to Anthropic skill standard, added YAML frontmatter
- v1.0.0 (2026-01-07): Initial creation with issue schema, lifecycle, and templates
