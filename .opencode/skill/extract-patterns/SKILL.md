---
name: extract-patterns
description: "Meta-skill to analyze conversations and extract implicit patterns, SOPs, and skills that emerge during work sessions. Automatically identifies recurring workflows, decision heuristics, and domain knowledge for codification. Use when: (1) Completing a significant work session, (2) Noticing repeated workflows in conversations, (3) Wanting to codify tribal knowledge, (4) Building a knowledge repository from agent interactions."
license: MIT
---

# Skill: Extract Patterns

> Meta-skill to analyze conversations and extract implicit patterns, SOPs, and skills that emerge during work, automatically creating new skills.

---

## Purpose

This skill analyzes conversations, tasks, or work sessions to:

1. **Identify recurring patterns** of work and decision-making
2. **Extract implicit SOPs** that were followed but not documented
3. **Discover emergent skills** that arose during work
4. **Automatically generate new skills** via `create-skill` chain

**Use when**:
- Completing a significant work session
- Noticing repeated workflows
- Wanting to codify tribal knowledge

---

## Approaches

### Quick (10 min)
Focus on workflow patterns only:
- List repeated action sequences
- Create 1-2 obvious skills
- Generate minimal report

### Complete (30 min)
Deep analysis of all pattern types:
- All 5 phases documented
- Full SOP and heuristic extraction
- Complete report with chaining

### Continuous
Run at end of each significant session:
- Accumulate patterns in knowledge repository
- Evolve existing skills incrementally

---

## Main Prompt

```
Perform a **comprehensive conversation analysis** to extract implicit patterns, SOPs, and skills that emerged during the work session.

## Analysis Scope
[Specify: CURRENT_CONVERSATION | SPECIFIC_TASK | LOG_FILES]

## Focus Area (Optional)
[e.g., "Backend patterns", "UI decisions", "Architecture choices"]

---

## Phase 1: Conversation Mining (PLANNING)

### A. Interaction Analysis
1. **Map User Requests**: List all explicit requests
2. **Map Agent Actions**: List major actions taken
3. **Identify Decision Points**: Where were choices made?
4. **Track Iterations**: What was refined or corrected?

### B. Pattern Recognition

#### Workflow Patterns
- [ ] Recurring sequences of steps
- [ ] Consistent file organization
- [ ] Standard naming conventions

#### Decision Heuristics
- [ ] Prioritization criteria (e.g., security > performance)
- [ ] When to ask vs proceed
- [ ] Thresholds triggering actions

#### Domain Knowledge
- [ ] Project-specific terminology
- [ ] Architectural patterns
- [ ] Integration points

#### Quality Standards
- [ ] Code quality expectations
- [ ] Documentation requirements
- [ ] Testing expectations

---

## Phase 2: SOP Extraction (EXECUTION)

### A. Formalize Implicit SOPs

For each pattern found:
```
SOP: [Name]
Trigger: [When this applies]
Steps:
1. [Step 1]
2. [Step 2]
Outcome: [Expected result]
```

### B. Document Heuristics

For each decision rule:
```
Heuristic: [Name]
Situation: [When to apply]
Rule: [The decision rule]
Example: [Concrete example from conversation]
```

### C. Capture Domain Knowledge

```
Concept: [Name]
Definition: [Clear explanation]
Context: [Where this applies]
Related: [Connected concepts]
```

---

## Phase 3: Skill Synthesis (EXECUTION)

### A. Evaluate Skill Candidates

For each pattern/SOP, evaluate:
- [ ] Is it **reusable** across contexts?
- [ ] Is it **specific enough** to be actionable?
- [ ] Is it **different** from existing skills?
- [ ] Is it **valuable** enough to document?

Rate: HIGH | MEDIUM | LOW priority

### B. Draft Skill Specifications

For HIGH priority candidates:
```
Skill Name: [Descriptive name]
Domain: [Backend | Frontend | Full-Stack | Meta]
Objective: [One-line purpose]
Key Phases: [Main execution phases]
Deliverables: [Expected outputs]
```

---

## Phase 4: Report Generation (VERIFICATION)

Generate Pattern Report:
1. **Executive Summary**: Key insights
2. **Patterns Discovered**: Table with category and priority
3. **SOPs Extracted**: Formalized procedures
4. **Skill Recommendations**: Skills to create

---

## Phase 5: Automatic Skill Creation (CHAINING)

### >> CHAIN TO: create-skill

For each HIGH priority skill candidate:
1. Load `create-skill` methodology
2. Apply prompt with synthesized specification
3. Create skill in `.opencode/skill/[skill-name]/`
4. Validate skill follows standards

```
>> CHAIN TO: create-skill
>> INPUT: Skill specification from Phase 3B
>> OUTPUT: Complete skill folder with SKILL.md
```

---

## Constraints

- **Be Thorough**: Don't miss subtle patterns
- **Be Selective**: Only formalize truly valuable patterns
- **Be Faithful**: Extract what happened, don't invent
- **Chain Automatically**: Always invoke create-skill for HIGH priority
- **Preserve Context**: Reference origin in new skills
```

---

## Extraction Heuristics

### 1. Frequency Analysis
```
If sequence appears 3+ times -> Workflow Pattern
If phrase appears 5+ times -> Convention/Term
If file type always checked -> Quality Pattern
```

### 2. Correction Analysis
```
Correction: "Use @nexus/logger, not console.log"
-> Heuristic: System logging via @nexus/logger
```

### 3. Justification Analysis
```
Justification: "Prioritize security over performance"
-> Heuristic: Priority = Security > Performance
```

### 4. Sequence Analysis
```
Always: "grep" -> "read" -> "edit"
-> Workflow: Find-Read-Fix pattern
```

### 5. Finalization Analysis
```
Before completion:
- pnpm test
- pnpm build
- Update docs
-> Workflow: Verification Checklist
```

---

## Pattern Categories

| Category | Description | Example |
|----------|-------------|---------|
| **Workflow** | Sequence of steps | "Always tag code before reporting" |
| **Heuristic** | Decision rule | "Security issues get CRITICAL severity" |
| **Template** | Reusable structure | "Skill README format" |
| **Convention** | Naming/organization | "Use TODO(DEBT) for tech debt" |
| **Integration** | System connections | "Services call Repositories, never DB" |

---

## Skill Chaining

### Chains To
- **create-skill**: When HIGH priority candidates found
  - Trigger: `skill_candidates.filter(s => s.priority == 'HIGH').length > 0`
  - Input: Skill specification
  - Output: New skill created

### Can Be Chained From
- **code-review**: To extract review patterns
- **Any work session**: For continuous improvement

---

## Example Invocations

### Analyze Current Conversation
```
/extract-patterns CURRENT_CONVERSATION
```

### Analyze with Focus
```
/extract-patterns CURRENT_CONVERSATION "Backend audit patterns"
```

### Analyze Task Logs
```
/extract-patterns LOG_FILES "/path/to/logs/*.txt"
```

---

## Evolution

### v3.0.0 (2026-01-07)
- Adapted to Anthropic standard frontmatter
- Removed version/category/complexity/estimated_time/chains_to from frontmatter
- Description enriched with "Use when" triggers

### v2.0.0 (2026-01-06)
- Migrated to .opencode/skill/ format
- Added multiple approaches (Quick/Complete/Continuous)
- Enhanced extraction heuristics
- Added pattern categories table

### v1.1 (2025-12-21)
- Added multiple approaches
- Added skills related section
- Added evolution tracking

### v1.0 (2025-12-21)
- Initial version with pattern taxonomy
- Automatic chaining to create-skill
