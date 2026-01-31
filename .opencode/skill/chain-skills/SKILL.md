---
name: chain-skills
description: "Document and execute skill chaining for composed workflows with defined contracts and triggers. Enables skills to invoke other skills automatically, creating pipelines with typed data contracts and error handling. Use when: (1) Building multi-step workflows that connect multiple skills, (2) Connecting skill outputs to other skill inputs, (3) Creating automated pipelines with conditional or parallel execution, (4) Documenting chain contracts between skills."
license: MIT
---

# Skill: Chain Skills

> Document and execute skill chaining for composed workflows with defined contracts and triggers.

---

## Purpose

This skill defines how skills can invoke other skills automatically, creating composed workflows. It establishes:

- Chain contracts between skills
- Trigger conditions
- Data passing protocols
- Error handling strategies

**Use when**:
- Building multi-step workflows
- Connecting skill outputs to inputs
- Creating automated pipelines

---

## Approaches

### Quick (15 min)
- Define simple linear chain: A -> B
- Document trigger condition
- Add chain section to both skills

### Complete (1 hour)
- Full chain topology design
- Typed data contracts
- Error handling
- Validation steps

### Incremental
- Start with one chain link
- Add more as patterns emerge
- Evolve contracts over time

---

## Main Prompt

```
Implement **skill chaining** to create a composed workflow from multiple skills.

## Chain Definition
- **Source Skill**: [Skill that initiates the chain]
- **Target Skill**: [Skill to be invoked]
- **Trigger Condition**: [When to chain]
- **Data Contract**: [What data flows between skills]

---

## Phase 1: Chain Design (PLANNING)

### A. Choose Chain Topology

1. **Linear Chain**: A -> B -> C
   ```
   extract-patterns -> create-skill
   ```

2. **Conditional Chain**: A -> (if X) B (else) C
   ```
   audit-code-review -> (if security issues) security-fix
                     -> (else) debt-report only
   ```

3. **Parallel Chain**: A -> B, C (concurrent)
   ```
   create-api -> create-tests, create-docs
   ```

4. **Loop Chain**: A -> FOR EACH -> B
   ```
   extract-patterns -> FOR EACH skill: create-skill
   ```

5. **Recovery Chain**: A -> ON ERROR -> B
   ```
   deploy -> ON ERROR -> rollback
   ```

### B. Define Contract

```yaml
chain_link:
  source: [source-skill]
  target: [target-skill]
  trigger: [condition expression]
  input:
    - field_name: type
  output:
    - field_name: type
  error_handling:
    on_failure: abort | continue | retry
```

---

## Phase 2: Documentation (EXECUTION)

### A. Update Source Skill README

Add section:
```markdown
## Skill Chaining

### Chains to: [target-skill]
- **Trigger**: [When to invoke]
- **Input**: [Data passed to target]
- **Output**: [Expected result from target]
```

### B. Update Target Skill README

Add section:
```markdown
## Can Be Chained From

- **[source-skill]**: Receives [input description]
```

---

## Phase 3: Execution Protocol

When executing a chain:

1. **Complete Source Skill** up to chain point
2. **Evaluate Trigger Condition**
3. **If triggered**:
   a. Prepare input data per contract
   b. Log: `>> CHAIN TO: [target-skill]`
   c. Execute target skill with input
   d. Capture output
   e. Return to source if needed
4. **If not triggered**:
   a. Log: `>> CHAIN SKIPPED: [reason]`
   b. Continue source skill

---

## Phase 4: Validation

Verify chain:
- [ ] Source skill completes to chain point
- [ ] Trigger condition evaluates correctly
- [ ] Input data properly formatted
- [ ] Target skill receives and processes
- [ ] Output captured if needed
- [ ] No infinite loops possible
- [ ] Error handling works

---

## Constraints

- **No Circular Chains**: A -> B -> A is forbidden
- **Explicit Contracts**: All data must be typed
- **Graceful Failures**: Chain failure shouldn't crash source
- **Logging Required**: All chain events must be logged
```

---

## Chain Patterns Reference

### Pattern 1: Result-Based Chain

```
IF skill_output.contains(HIGH_PRIORITY) THEN chain
```

**Example**: extract-patterns -> create-skill

### Pattern 2: Error-Based Chain

```
IF skill_result == ERROR AND error.recoverable THEN chain to recovery
```

**Example**: deploy -> rollback

### Pattern 3: Completion Chain

```
AFTER skill_completes THEN always chain
```

**Example**: implement-feature -> run-tests

### Pattern 4: Threshold Chain

```
IF metric > threshold THEN chain
```

**Example**: audit-code-review -> escalate (if critical > 3)

---

## Contract Template

```yaml
chain:
  id: unique-chain-id
  pattern: linear | conditional | parallel | loop | recovery
  
  source:
    skill: skill-name
    completion_point: phase or condition
  
  trigger:
    type: condition | completion | error
    expression: "boolean expression"
  
  target:
    skill: target-skill-name
    input_mapping:
      target_field: source.expression
  
  output_handling:
    capture: boolean
    field_name: result
    
  error_handling:
    on_target_failure: abort | continue | retry
    max_retries: 3
```

---

## Example: extract-patterns -> create-skill

```yaml
chain:
  id: pattern-to-skill
  pattern: conditional
  
  source:
    skill: extract-patterns
    completion_point: Phase 3 complete
  
  trigger:
    type: condition
    expression: "skill_candidates.filter(s => s.priority == 'HIGH').length > 0"
  
  target:
    skill: create-skill
    input_mapping:
      skill_name: candidate.name
      domain: candidate.domain
      objective: candidate.objective
  
  execution:
    mode: loop
    for_each: "skill_candidates.filter(s => s.priority == 'HIGH')"
```

---

## Skill Chaining

### Can Be Chained From
- Any skill needing composition
- `extract-patterns`: When documenting new chains

### Chains To
- Dynamic based on chain definition
- No fixed target skills

---

## Evolution

### v3.0.0 (2026-01-07)
- Adapted to Anthropic standard frontmatter
- Removed version/category/complexity/estimated_time from frontmatter
- Description enriched with "Use when" triggers

### v2.0.0 (2026-01-06)
- Migrated to .opencode/skill/ format
- Added multiple approaches
- Enhanced contract template
- Added pattern reference

### v1.0.0 (2025-12-21)
- Initial version with chain patterns
- Basic contract documentation
