---
name: create-skill
description: "Definitive methodology and standard for creating OpenCode skills, including identification, extraction, and validation protocols. The meta-skill that creates, documents, and validates new skills. Use when: (1) You identify a recurring task executed more than 3 times, (2) You need to standardize a complex multi-step process, (3) You are extracting a pattern from existing code or documentation, (4) You want to share a capability with other agents."
license: MIT
---

# Skill: Create Skill

<!-- @META: Skill Factory -->
<!--
    File: .opencode/skill/create-skill/SKILL.md
    Version: 4.0.0
    Created: 2025-12-21
    Updated: 2026-01-07
    Scope: Methodology for creating new skills
-->

<!-- @NOTE(skill-def): Definition -->
> The meta-skill to create, document, and validate new skills following the official OpenCode standard.

---

## Purpose

<!-- @NOTE(purpose): Purpose -->
This skill defines the complete lifecycle for creating new skills in the OpenCode environment. It moves beyond simple prompt writing to a structured engineering process.

**Use when**:
- You identify a recurring task (executed > 3 times).
- You need to standardize a complex process.
- You are extracting a pattern from existing code/docs.
- You want to share a capability with other agents.

---

## Skill Identification Methodology

<!-- @RULE: Identification -->
Not everything should be a skill. Use this matrix to decide:

| Criteria | Threshold for Skill Creation |
|----------|------------------------------|
| **Frequency** | Task performed > 3 times manually or by agents. |
| **Complexity** | Requires > 5 steps or involves multiple files/tools. |
| **Risk** | High cost of error (e.g., security, db migration) requires strict protocol. |
| **Cognitive Load** | Requires looking up docs or context every time. |

**Decision Algorithm**:
1. Is it a one-off? -> **No Skill** (Use temporary prompt).
2. Is it simple (1 step)? -> **No Skill** (Use direct command).
3. Is it recurring AND (complex OR risky OR specialized)? -> **CREATE SKILL**.

---

## Skill Discovery Patterns

<!-- @NOTE(patterns): Discovery -->
Use these patterns to uncover hidden skills in your codebase:

### 1. "The Repeater" (Code Duplication)
- **Signal**: Similar code blocks in multiple files (e.g., API endpoints, UI components).
- **Skill**: `create-endpoint`, `create-component`.
- **Action**: Abstract the template into a skill.

### 2. "The Ritual" (Manual Process)
- **Signal**: "Always run X after Y" or long checklists in PR descriptions.
- **Skill**: `deploy-sequence`, `verify-release`.
- **Action**: Automate the checklist via a skill.

### 3. "The Fixer" (Recurring Bugs)
- **Signal**: Same type of bug fixed multiple times (e.g., "Fix useEffect dependency").
- **Skill**: `audit-react-hooks`, `fix-pattern-x`.
- **Action**: Create an audit skill to catch it early.

### 4. "The Lore" (Tribal Knowledge)
- **Signal**: "Ask Bob how to do this" or specialized knowledge in one person's head.
- **Skill**: `explain-architecture`, `guide-setup`.
- **Action**: Document the knowledge as an interactive skill.

---

## Extracting Implicit Skills

<!-- @NOTE(extraction): Extraction -->
How to turn existing code/docs into a skill:

### Phase 1: Analysis
1. **Gather Samples**: Collect 3-5 examples of the task being done correctly.
2. **Identify Variables**: What changes between examples? (Names, paths, types).
3. **Identify Constants**: What stays the same? (Structure, imports, error handling).

### Phase 2: Abstraction
1. **Create Template**: Replace variables with placeholders (e.g., `{{feature_name}}`).
2. **Define Rules**: Explicitly state the logic for the "Constants".
3. **Draft Prompt**: Combine Template + Rules into the Main Prompt.

### Phase 3: Codification
1. **Structure**: Apply the OpenCode Skill Format.
2. **Constraints**: Add "MUST" and "MUST NOT" rules based on past failures.
3. **Validation**: Test the skill on a new case to ensure it produces the expected result.

---

## Official OpenCode Skill Template

<!-- @SCHEMA: Official Template -->
Create new skills in `.opencode/skill/<name>/SKILL.md` using this EXACT format:

```markdown
---
name: skill-name-kebab-case
description: "Clear description of what this skill does. Include triggers. Use when: (1) condition 1, (2) condition 2, (3) condition 3."
license: MIT
---

# Skill: [Display Name]

<!-- @META: Metadata tags -->
<!--
    File: .opencode/skill/<name>/SKILL.md
    Version: 1.0.0
    Scope: [Scope definition]
-->

> [Quote style summary]

## Purpose
[Why this skill exists. What problem it solves.]

**Use when**:
- [Condition 1]
- [Condition 2]

## Approaches
### Default
[Description of the standard approach]

## Main Prompt
```
[THE ACTUAL PROMPT CONTENT]
[CONTEXT]
...
[INSTRUCTIONS]
...
[CONSTRAINTS]
...
```

## Validation Checklist
- [ ] [Criterion 1]
- [ ] [Criterion 2]

## Evolution
### v1.0.0 (YYYY-MM-DD)
- Initial version
```

---

## Validation Checklist

<!-- @RULE: Validation -->
Before marking a skill as "Ready", verify:

1. **Naming**:
   - [ ] Lowercase alphanumeric with hyphens (kebab-case).
   - [ ] 1-64 characters.
   - [ ] Action-oriented (e.g., `create-api` not `api-creator`).

2. **Structure**:
   - [ ] Valid YAML frontmatter with only name, description, license.
   - [ ] Contains `Main Prompt` section.
   - [ ] Located in correct directory: `.opencode/skill/<name>/SKILL.md`.

3. **Prompt Quality**:
   - [ ] **Context**: Does it explain *what* the agent is (Role)?
   - [ ] **Objective**: Is the goal clear and measurable?
   - [ ] **Constraints**: Does it forbid bad patterns?
   - [ ] **Examples**: (Optional) Are there few-shot examples?

4. **Safety**:
   - [ ] Does it warn about destructive actions?
   - [ ] Does it require user confirmation for high-risk tasks?

---

## Skill Chaining

### Can Be Chained From
- **extract-patterns**: When patterns are identified, this skill creates the formal artifact.

### Chains To
- **review-skill**: (Hypothetical) To validate the created skill.

---

## Evolution

### v4.0.0 (2026-01-07)
- Adapted to Anthropic standard frontmatter (name, description, license only)
- Removed nested metadata structure
- Description enriched with "Use when" triggers
- Template updated to new standard

### v3.0.0 (2026-01-07)
- **Major**: Updated to official OpenCode YAML frontmatter standard.
- **Added**: Skill Identification Methodology.
- **Added**: Skill Discovery Patterns.
- **Added**: Explicit Extraction Protocol.
- **Added**: Validation Checklist.

### v2.0.0 (2026-01-06)
- Migrated to .opencode/skill/ format.
- Added complete SKILL.md template.

### v1.0.0 (2025-12-21)
- Initial version.
