# Available Models Reference

<!-- @META: Model Reference -->
<!--
    File: .opencode/specs/AVAILABLE_MODELS.md
    Version: 1.0.0
    Created: 2026-01-06
    Updated: 2026-01-07
    Scope: Available AI models for OpenCode via Antigravity
-->

<!-- @NOTE(models-def): Definition -->
> Modelos disponíveis via Antigravity Provider para OpenCode

## Sumário por Categoria

<!-- @NOTE(summary): Summary -->
| Categoria | Modelo Recomendado | Contexto | Uso |
|-----------|-------------------|----------|-----|
| **Orquestração** | `google/gemini-3-pro-high` | 1M | Visão global, coordenação |
| **Planejamento** | `google/antigravity-claude-opus-4-5-thinking-high` | 200k | Decisões estratégicas |
| **Programação** | `google/antigravity-claude-opus-4-5-thinking-medium` | 200k | Implementação complexa |
| **Debugging** | `google/antigravity-claude-opus-4-5-thinking-high` | 200k | Root cause analysis |
| **Pesquisa** | `google/gemini-3-flash` | 1M | Exploração rápida |
| **Tasks rápidas** | `google/antigravity-claude-sonnet-4-5` | 200k | Tarefas simples |

---

## Modelos Gemini 3 (1M Context)

<!-- @SCHEMA: Gemini Models -->
| ID do Modelo | Nome | Contexto | Output | Modalidades |
|--------------|------|----------|--------|-------------|
| `google/gemini-3-pro-high` | Gemini 3 Pro High | 1,048,576 | 65,535 | text, image, pdf |
| `google/gemini-3-pro-low` | Gemini 3 Pro Low | 1,048,576 | 65,535 | text, image, pdf |
| `google/gemini-3-flash` | Gemini 3 Flash | 1,048,576 | 65,536 | text, image, pdf |
| `google/gemini-3-flash-preview` | Gemini 3 Flash Preview | 1,048,576 | 65,536 | text, image, pdf |
| `google/gemini-3-pro-preview` | Gemini 3 Pro Preview | 1,048,576 | 65,535 | text, image, pdf |

### Aliases Antigravity
| ID do Modelo | Equivalente |
|--------------|-------------|
| `google/antigravity-gemini-3-pro-high` | gemini-3-pro-high |
| `google/antigravity-gemini-3-pro-low` | gemini-3-pro-low |
| `google/antigravity-gemini-3-flash` | gemini-3-flash |

---

## Modelos Gemini 2.5 (1M Context)

<!-- @SCHEMA: Gemini 2.5 Models -->
| ID do Modelo | Nome | Contexto | Output | Modalidades |
|--------------|------|----------|--------|-------------|
| `google/gemini-2.5-pro` | Gemini 2.5 Pro | 1,048,576 | 65,536 | text, image, pdf |
| `google/gemini-2.5-flash` | Gemini 2.5 Flash | 1,048,576 | 65,536 | text, image, pdf |

---

## Modelos Claude (200k Context)

### Claude Sonnet 4.5

<!-- @SCHEMA: Claude Sonnet Models -->
| ID do Modelo | Nome | Contexto | Output | Thinking |
|--------------|------|----------|--------|----------|
| `google/antigravity-claude-sonnet-4-5` | Claude Sonnet 4.5 | 200,000 | 64,000 | Sem |
| `google/antigravity-claude-sonnet-4-5-thinking-low` | Claude Sonnet 4.5 Think Low | 200,000 | 64,000 | Low |
| `google/antigravity-claude-sonnet-4-5-thinking-medium` | Claude Sonnet 4.5 Think Medium | 200,000 | 64,000 | Medium |
| `google/antigravity-claude-sonnet-4-5-thinking-high` | Claude Sonnet 4.5 Think High | 200,000 | 64,000 | High |

### Claude Opus 4.5 (Thinking Only)

<!-- @SCHEMA: Claude Opus Models -->
| ID do Modelo | Nome | Contexto | Output | Thinking |
|--------------|------|----------|--------|----------|
| `google/antigravity-claude-opus-4-5-thinking-low` | Claude Opus 4.5 Think Low | 200,000 | 64,000 | Low |
| `google/antigravity-claude-opus-4-5-thinking-medium` | Claude Opus 4.5 Think Medium | 200,000 | 64,000 | Medium |
| `google/antigravity-claude-opus-4-5-thinking-high` | Claude Opus 4.5 Think High | 200,000 | 64,000 | High |

---

## Matriz de Seleção por Tarefa

<!-- @RULE: Task Selection -->
| Tarefa | Modelo | Justificativa |
|--------|--------|---------------|
| Orquestração de projetos | `gemini-3-pro-high` | 1M context para visão global |
| Arquitetura e design | `claude-opus-4-5-thinking-high` | Raciocínio profundo |
| Implementação de código | `claude-opus-4-5-thinking-medium` | Balance performance/qualidade |
| Debugging complexo | `claude-opus-4-5-thinking-high` | Análise profunda |
| Code review | `claude-sonnet-4-5-thinking-medium` | Eficiente e preciso |
| Pesquisa de codebase | `gemini-3-flash` | Rápido, 1M context |
| Tarefas simples | `claude-sonnet-4-5` | Custo-efetivo |
| Análise de imagens/PDFs | `gemini-3-pro-high` | Melhor multimodal |

---

## Níveis de Thinking

<!-- @NOTE(thinking): Levels -->
| Nível | Uso Recomendado |
|-------|-----------------|
| **High** | Decisões arquiteturais, debugging complexo, planejamento estratégico |
| **Medium** | Implementação de features, refactoring, code review |
| **Low** | Tarefas rotineiras, correções simples |
| **Sem** | Respostas rápidas, queries simples |

---

## Configuração no opencode.json

<!-- @SCHEMA: Config JSON -->
```json
{
  "$schema": "https://opencode.ai/config.json",
  "plugin": ["opencode-antigravity-auth@1.4.3"],
  "provider": {
    "google": {
      "models": { ... }
    }
  }
}
```

---

**Atualizado**: 2026-01-06
**Fonte**: Configuração Antigravity Provider
