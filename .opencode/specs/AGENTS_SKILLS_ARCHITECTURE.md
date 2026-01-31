# OpenCode Agents & Skills Architecture

<!-- @META: Architecture Specs -->
<!--
    File: .opencode/specs/AGENTS_SKILLS_ARCHITECTURE.md
    Version: 2.0.0
    Created: 2026-01-06
    Updated: 2026-01-07
    Scope: Architecture of agents and skills for OpenCode
-->

<!-- @NOTE(arch-def): Definition -->
> Complete architecture of agents and skills for the OpenCode template.

## 1. Overview

<!-- @NOTE(overview): Overview -->
This document describes the architecture of agents and skills for the OpenCode system. The template provides a foundation for AI-powered development environments.

---

## 2. Model Architecture

<!-- @SCHEMA: Architecture Diagram -->
```
+-------------------------------------------------------------+
|                    ORCHESTRATION LAYER                       |
|                                                              |
|    +--------------------------------------------------+     |
|    |         Leonidas (Gemini 3 Pro High)             |     |
|    |         1M context - Global Vision               |     |
|    +---------------------+----------------------------+     |
|                          |                                   |
+--------------------------|----------------------------------+
                           |
         +----------------+----------------+
         |                |                |
         v                v                v
+---------------+ +-----------+ +---------------+
|   PLANNING    | |  CODING   | |   RESEARCH    |
| Claude Opus   | |Claude Opus| | Gemini 3 Flash|
| Thinking High | |Think Med  | |               |
+---------------+ +-----------+ +---------------+
         |               |               |
         v               v               v
+---------------+ +-----------+ +---------------+
| - planning    | | - code    | | - research    |
| - architect   | | - debug   | |               |
+---------------+ +-----------+ +---------------+
```

---

## 3. Agents

### 3.1 Leonidas (Orchestrator)
<!-- @REF(.opencode/agent/leonidas.md): Leonidas -->
| Field | Value |
|-------|-------|
| **File** | `.opencode/agent/leonidas.md` |
| **Model** | `google/antigravity-gemini-3-pro-high` |
| **Context** | 1,048,576 tokens |
| **Purpose** | Autonomous project orchestration |
| **SOPs** | LEO-001 to LEO-008 |

### 3.2 Architect
<!-- @REF(.opencode/agent/architect.md): Architect -->
| Field | Value |
|-------|-------|
| **File** | `.opencode/agent/architect.md` |
| **Model** | `google/antigravity-claude-opus-4-5-thinking-high` |
| **Context** | 200,000 tokens |
| **Purpose** | System design (only .md files) |
| **SOPs** | ARC-001 |

### 3.3 Code
<!-- @REF(.opencode/agent/code.md): Code -->
| Field | Value |
|-------|-------|
| **File** | `.opencode/agent/code.md` |
| **Model** | `google/antigravity-claude-opus-4-5-thinking-medium` |
| **Context** | 200,000 tokens |
| **Purpose** | Implementation and tests |
| **SOPs** | CODE-001 (6 phases) |

### 3.4 Debug
<!-- @REF(.opencode/agent/debug.md): Debug -->
| Field | Value |
|-------|-------|
| **File** | `.opencode/agent/debug.md` |
| **Model** | `google/antigravity-claude-opus-4-5-thinking-high` |
| **Context** | 200,000 tokens |
| **Purpose** | Systematic debugging |
| **SOPs** | DEBUG-001 (6 phases) |

### 3.5 Research
<!-- @REF(.opencode/agent/research.md): Research -->
| Field | Value |
|-------|-------|
| **File** | `.opencode/agent/research.md` |
| **Model** | `google/antigravity-gemini-3-flash` |
| **Context** | 1,048,576 tokens |
| **Purpose** | Codebase investigation |
| **SOPs** | RSH-001 to RSH-006 |

### 3.6 Planning
<!-- @REF(.opencode/agent/planning.md): Planning -->
| Field | Value |
|-------|-------|
| **File** | `.opencode/agent/planning.md` |
| **Model** | `google/antigravity-claude-opus-4-5-thinking-high` |
| **Context** | 200,000 tokens |
| **Purpose** | Strategic planning and RFCs |
| **SOPs** | PLN-001 to PLN-006 |

---

## 4. Skills

<!-- @REF(.opencode/skill/): Skills -->
| Skill | File | Purpose |
|-------|------|---------|
| `code-review` | `skill/code-review/SKILL.md` | Systematic code review |
| `rfc-creation` | `skill/rfc-creation/SKILL.md` | Enterprise RFC creation |
| `codebase-analysis` | `skill/codebase-analysis/SKILL.md` | Codebase analysis |
| `task-decomposition` | `skill/task-decomposition/SKILL.md` | Initiative decomposition |
| `opencode-specs` | `skill/opencode-specs/SKILL.md` | OpenCode engine documentation |
| `create-skill` | `skill/create-skill/SKILL.md` | Skill creation methodology |

---

## 5. Commands

<!-- @REF(.opencode/command/): Commands -->
| Command | File | Model | Purpose |
|---------|------|-------|---------|
| `/analyze` | `command/analyze.md` | `google/antigravity-gemini-3-flash` | Analyze codebase |
| `/rfc` | `command/rfc.md` | `google/antigravity-claude-opus-4-5-thinking-high` | Create RFC |
| `/decompose` | `command/decompose.md` | `google/antigravity-claude-opus-4-5-thinking-high` | Decompose tasks |
| `/review` | `command/review.md` | `google/antigravity-claude-sonnet-4-5-thinking-medium` | Code review |

---

## 6. Directory Structure

<!-- @NOTE(struct): Directory Structure -->
```
.opencode/
+-- agent/
|   +-- leonidas.md        # Orchestrator (Gemini 3 Pro High)
|   +-- architect.md       # Design (Claude Opus Thinking High)
|   +-- code.md            # Implementation (Claude Opus Thinking Medium)
|   +-- debug.md           # Debugging (Claude Opus Thinking High)
|   +-- research.md        # Research (Gemini 3 Flash)
|   +-- planning.md        # Planning (Claude Opus Thinking High)
|
+-- skill/
|   +-- code-review/
|   |   +-- SKILL.md
|   +-- rfc-creation/
|   |   +-- SKILL.md
|   +-- codebase-analysis/
|   |   +-- SKILL.md
|   +-- task-decomposition/
|       +-- SKILL.md
|
+-- command/
|   +-- analyze.md         # /analyze (Gemini Flash)
|   +-- rfc.md             # /rfc (Claude Opus High)
|   +-- decompose.md       # /decompose (Claude Opus High)
|   +-- review.md          # /review (Claude Sonnet Medium)
|
+-- specs/
    +-- AVAILABLE_MODELS.md
    +-- ORCHESTRATION_ARCHITECTURE.md
    +-- OPENCODE_FORMAT_REFERENCE.md
    +-- AGENTS_SKILLS_ARCHITECTURE.md  # This file
```

---

## 7. Patterns and Conventions

### 7.1 Models by Task Type

<!-- @RULE: Model Selection -->
| Task | Model | Thinking Level |
|------|-------|----------------|
| Orchestration | `antigravity-gemini-3-pro-high` | N/A (1M context) |
| Architecture | `claude-opus-4-5-thinking-high` | High |
| Planning | `claude-opus-4-5-thinking-high` | High |
| Debugging | `claude-opus-4-5-thinking-high` | High |
| Implementation | `claude-opus-4-5-thinking-medium` | Medium |
| Code Review | `claude-sonnet-4-5-thinking-medium` | Medium |
| Research | `antigravity-gemini-3-flash` | N/A (1M context) |

### 7.2 Confidence Levels

<!-- @NOTE(confidence): Levels -->
- GREEN HIGH: Multiple sources, verified
- YELLOW MEDIUM: Analysis supports, not verified
- ORANGE LOW: Single source, inference
- RED HYPOTHESIS: Theory to investigate

### 7.3 Issue Severity

<!-- @NOTE(severity): Severity -->
- RED CRITICAL: Must fix
- ORANGE MAJOR: Should fix
- YELLOW MINOR: Nice to fix
- BLUE SUGGESTION: Consider

---

## 8. Version History

| Version | Date | Changes |
|---------|------|---------|
| 2.0.0 | 2026-01-07 | Generalized for template use |
| 1.1.0 | 2026-01-07 | Antigravity models configured |
| 1.0.0 | 2026-01-06 | Initial document |
