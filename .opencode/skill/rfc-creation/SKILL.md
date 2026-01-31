---
name: rfc-creation
description: "Create enterprise-grade Request for Comments documents for architectural decisions. Provides structured methodology for research, options analysis, and recommendation with stakeholder alignment. Use when: (1) New feature with architectural impact needs documentation, (2) Technology adoption decision requires trade-off analysis, (3) Process change affecting multiple teams needs consensus, (4) Initiative size M, L, or XL requires formal review."
license: MIT
---

<!-- @META: RFC Creation Skill -->
<!--
    File: .opencode/skill/rfc-creation/SKILL.md
    Version: 3.0.0
    Created: 2025-12-18
    Updated: 2026-01-07
    Scope: Methodology for creating architectural RFCs
-->

# Skill: RFC Creation

<!-- @NOTE(skill-def): Definition -->
> Create enterprise-grade Request for Comments documents for architectural decisions.

---

## Purpose

<!-- @NOTE(purpose): Purpose -->
Create RFCs when decisions require:
- Research before implementation
- Stakeholder alignment
- Trade-off documentation
- Long-term impact analysis

**Use when**:
- New feature with architectural impact
- Technology adoption decision
- Process change affecting teams
- Initiative size M, L, or XL

---

## Approaches

### Quick (30 min)
<!-- @SCHEMA: Approach 1 -->
- FOCUSED scope RFC
- 2 options comparison
- Basic recommendation

### Complete (2 hours)
<!-- @SCHEMA: Approach 2 -->
- COMPREHENSIVE scope
- Full options analysis
- Benchmarks if applicable
- Stakeholder review

### Incremental
<!-- @SCHEMA: Approach 3 -->
- Draft -> Research -> Refine
- Iterate with feedback
- Progressive detail

---

## Main Prompt

<!-- @SCHEMA: Main Prompt -->
```
Create an **RFC document** for the specified decision.

## RFC Topic
[What decision needs to be made]

## RFC Type
[RESEARCH | COMPARISON | ARCHITECTURE | SECURITY | PERFORMANCE]

## Scope
[FOCUSED | EXPLORATORY | COMPREHENSIVE]

---

## Phase 1: Problem Definition

### Define the Question
> What specific question does this RFC answer?

### State Hypothesis
> What do we believe the answer is and why?

### Establish Scope
| In Scope | Out of Scope |
|----------|--------------|
| [Include] | [Exclude] |

### Success Criteria
- [ ] Criterion for successful RFC

---

## Phase 2: Research

### Gather Information
- Official documentation
- Industry best practices
- Codebase analysis
- Similar implementations

### Identify Options
For each viable approach:
1. Name and description
2. How it works (with code)
3. Pros and cons
4. Evidence and sources

---

## Phase 3: Analysis

### Define Evaluation Criteria
| Criterion | Weight | Definition |
|-----------|--------|------------|
| [Criterion] | [Weight] | [What it measures] |

### Evaluate Options
| Criterion | Option A | Option B |
|-----------|----------|----------|
| [Criterion] | [Score] | [Score] |

### Identify Risks
| Risk | Probability | Impact | Mitigation |
|------|-------------|--------|------------|
| [Risk] | [H/M/L] | [H/M/L] | [Strategy] |

---

## Phase 4: Recommendation

### State Recommendation
> Based on analysis, recommend Option [X]

### Justify
1. Primary reason
2. Secondary reason
3. Trade-offs accepted

### Implementation Guidance
- Prerequisites
- Key steps
- Timeline estimate

---

## Phase 5: Documentation

### Create RFC File
Use template: `.opencode/skill/templates/rfc.mdx`

### Review Checklist
- [ ] Problem clearly stated
- [ ] 2+ alternatives documented
- [ ] Trade-offs explicit
- [ ] Success metrics defined
- [ ] Risks identified
- [ ] Plan realistic
- [ ] Stakeholders identified

---

## Output: RFC Document

```markdown
# RFC-[NUMBER]: [TITLE]

**Status**: Draft
**Author**: [Name]
**Created**: YYYY-MM-DD

## Summary
[2-3 sentence overview]

## Problem Statement
[Current state and pain points]

## Goals / Non-Goals
[What we're solving / not solving]

## Proposed Solution
[Overview and technical design]

## Alternatives Considered
[Other options and why not chosen]

## Risk Assessment
[Risks and mitigations]

## Success Metrics
[How we measure success]

## Open Questions
[Remaining unknowns]
```
```

---

## RFC Types

<!-- @NOTE(types): RFC Types -->
| Type | Focus | Time |
|------|-------|------|
| RESEARCH | Deep dive on technology | 2-4h |
| COMPARISON | A vs B vs C | 4-8h |
| ARCHITECTURE | Major design decision | 1-3d |
| SECURITY | Security-focused | 4-8h |
| PERFORMANCE | Optimization research | 4-8h |

---

## Scope Levels

<!-- @NOTE(scope): Scope Levels -->
| Scope | Description | Time |
|-------|-------------|------|
| FOCUSED | Single question, 2-3 options | 2-4h |
| EXPLORATORY | Broader investigation | 4-8h |
| COMPREHENSIVE | Full deep-dive | 1-3d |

---

## Quality Checklist

<!-- @RULE: QC -->
Before submitting:
- [ ] Question answerable
- [ ] Options covered fairly
- [ ] Evidence provided
- [ ] Comparison objective
- [ ] Recommendation justified
- [ ] Questions documented
- [ ] References linked

---

## Skill Chaining

### Can Be Chained From
- **code-review**: When architectural issue found
- **codebase-analysis**: When design decision needed

### Chains To
- **task-decomposition**: When RFC approved
  - Trigger: RFC status = ACCEPTED
  - Input: Recommendation and implementation guidance

---

## Evolution

### v3.0.0 (2026-01-07)
- Adapted to Anthropic standard frontmatter
- Removed version/category/complexity/estimated_time/chains_from/chains_to from frontmatter
- Description enriched with "Use when" triggers

### v2.0.0 (2026-01-06)
- Migrated to enhanced format
- Added multiple approaches
- Added skill chaining
- Added RFC types reference

### v1.0.0 (2025-12-18)
- Initial version with RFC structure
