---
description: Review code for quality and issues
model: google/antigravity-claude-sonnet-4-5-thinking-medium
subtask: true
---

<!-- @META: Command /review -->
<!--
    File: .opencode/command/review.md
    Scope: Slash command to trigger code review
-->

<!-- @REF(.opencode/skill/code-review/SKILL.md): Skill -->
Perform a comprehensive code review.

Use the `code-review` skill for the checklist and format.

Check for:
1. Correctness and logic
2. Security issues (vulnerabilities, secrets)
3. Performance problems
4. Code quality and technical debt
5. Test coverage
6. Documentation

**Mandatory SOP-002 Compliance**:
- Add inline annotations using standardized tags (@BUG, @VULN, @SECURITY, @TECH-DEBT, @PERF, @TODO).
- Ensure all findings are discoverable via terminal search (`rg "@TAG"`).

Categorize issues by severity (Critical, Major, Minor, Suggestion).
