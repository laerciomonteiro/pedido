---
name: code-review
description: "Systematic code review methodology with severity-based issue classification following SOP-002 standards. Covers correctness, security, performance, maintainability, testing, and documentation dimensions. Outputs findings as categorized markdown with inline annotations (@BUG, @VULN, @SECURITY, @PERF, @TODO). Use when: (1) Reviewing pull requests before merge, (2) Auditing existing codebase modules, (3) Pre-merge validation and quality gates, (4) Quality gate enforcement in CI/CD pipelines."
license: MIT
---

# Skill: Code Review

> Systematic code review following enterprise standards with severity-based issue classification.

---

## Purpose

Perform comprehensive code reviews that catch issues before merge:

- **Correctness**: Logic, edge cases, error handling
- **Security**: Vulnerabilities, secrets, injection
- **Performance**: N+1, caching, algorithms
- **Quality**: Style, naming, complexity
- **Testing**: Coverage, meaningfulness
- **Documentation**: APIs, complex logic

---

## Approaches

### Quick (5 min)
- Focus on CRITICAL and MAJOR only
- Skip documentation check
- Spot-check tests

### Complete (30 min)
- Full checklist coverage
- All severity levels
- Positive highlights
- Pattern extraction

### Incremental
- Review by file/module
- Track across sessions
- Aggregate findings

---

## Main Prompt

```
Perform a **systematic code review** of the specified code.

## Review Target
[PR #XXX | File path | Module name]

## Focus Area (Optional)
[e.g., "Security focus", "Performance audit"]

---

## Phase 1: Correctness Review

- [ ] Logic implements requirements correctly
- [ ] Edge cases handled (null, empty, boundary)
- [ ] Error paths covered with appropriate handling
- [ ] No obvious bugs or logic errors
- [ ] State management is correct

---

## Phase 2: Security Review

- [ ] No hardcoded secrets or credentials
- [ ] Input validation present for all user input
- [ ] SQL injection prevention (parameterized queries)
- [ ] XSS prevention (if frontend, proper escaping)
- [ ] Authentication/authorization correct
- [ ] Sensitive data handling appropriate

---

## Phase 3: Performance Review

- [ ] No N+1 queries (database access patterns)
- [ ] Appropriate caching where beneficial
- [ ] No unnecessary loops or iterations
- [ ] Efficient algorithms (O notation awareness)
- [ ] No memory leaks (cleanup, event listeners)

---

## Phase 4: Code Quality Review

- [ ] Follows project style guide
- [ ] Meaningful variable and function names
- [ ] Functions < 50 lines
- [ ] Cyclomatic complexity < 10
- [ ] DRY (no unnecessary duplication)
- [ ] Single Responsibility Principle
- [ ] Proper TypeScript types (no `any`)

---

## Phase 5: Testing Review

- [ ] Tests exist for new code
- [ ] Tests are meaningful (not just for coverage)
- [ ] Edge cases tested
- [ ] Error paths tested
- [ ] Mocking appropriate (not over-mocked)

---

## Phase 6: Documentation Review

- [ ] Complex logic documented (why, not what)
- [ ] Public APIs have JSDoc/TSDoc
- [ ] README updated if API changed
- [ ] Decision comments for non-obvious choices

---

## Output Format

Use severity-based categorization and **Mandatory SOP-002 Inline Annotations**:

```markdown
# Code Review: [Target]

## Summary
[2-3 sentence assessment]

## Critical Issues 🔴
[Must fix before merge] - Add `// @BUG` or `// @VULN` inline

## Major Issues 🟠
[Should fix before merge] - Add `// @SECURITY` or `// @FIXME` inline

## Minor Issues 🟡
[Nice to fix] - Add `// @TECH-DEBT` or `// @PERF` inline

## Suggestions 🔵
[Consider for future] - Add `// @TODO` inline
```

---

## Constraints

- Be specific with file:line references
- **SOP-002 Compliance**: Every finding MUST be annotated inline using the appropriate tag (@BUG, @VULN, etc.)
- Provide actionable feedback
- Balance criticism with positive recognition
- Prioritize by severity
```

---

## Evolution

### v2.1.0 (2026-01-07)
- Integrated SOP-002: Inline Audit Protocol
- Standardized inline tagging for terminal-based discovery

### v2.0.0 (2026-01-06)
- Migrated to enhanced format
- Added multiple approaches
- Added skill chaining
- Added review heuristics

### v1.0.0 (2025-12-18)
- Initial version with checklist format
