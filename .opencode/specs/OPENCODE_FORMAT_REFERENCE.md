# OpenCode Format Reference

<!-- @META: Format Reference -->
<!--
    File: .opencode/specs/OPENCODE_FORMAT_REFERENCE.md
    Version: 1.0.0
    Created: 2026-01-06
    Updated: 2026-01-07
    Scope: File format specifications for agents, skills, and commands
-->

<!-- @NOTE(format-def): Definition -->
> Referência de formatos para agents, skills e commands no OpenCode

## 1. Formato Agent (.opencode/agent/*.md)

<!-- @SCHEMA: Agent Format -->
```markdown
---
mode: primary|secondary          # Modo de operação
hidden: true|false              # Visível no menu
model: provider/model-name      # Modelo a usar
color: "#HEXCODE"               # Cor do agent
tools:                          # Ferramentas permitidas
  "*": true|false               # Todas ou nenhuma
  "tool-name": true             # Tool específica
---

[Instruções do agent em markdown]
```

### Campos Disponíveis
- `mode`: primary (principal) ou secondary
- `hidden`: ocultar do menu de seleção
- `model`: modelo específico a usar
- `color`: cor de identificação visual
- `tools`: permissões de ferramentas

---

## 2. Formato Skill (.opencode/skill/[name]/SKILL.md)

<!-- @SCHEMA: Skill Format -->
```markdown
---
name: skill-name
description: quando usar esta skill
---

[Conteúdo da skill - instruções, templates, etc]
```

### Estrutura de Pasta
```
.opencode/skill/
└── skill-name/
    ├── SKILL.md          # Definição principal
    └── [recursos/]       # Arquivos auxiliares opcionais
```

---

## 3. Formato Command (.opencode/command/*.md)

<!-- @SCHEMA: Command Format -->
```markdown
---
description: descrição curta do comando
model: provider/model-name    # Opcional
subtask: true|false           # É subtarefa?
---

[Instruções do comando]
```

### Invocação
```
/command-name [args]
```

---

## 4. Modelos Disponíveis (conforme specs)

<!-- @REF(.opencode/specs/AVAILABLE_MODELS.md): Available Models -->
| Uso | Modelo | Identificador |
|-----|--------|---------------|
| Planejamento | Claude Opus | `anthropic/claude-opus` |
| Programação | Claude Opus | `anthropic/claude-opus` |
| Pesquisa | Gemini Flash | `google/gemini-2.5-flash` |
| Orquestração | Gemini Pro | `google/gemini-2.5-pro` |
| Tasks rápidas | Claude Haiku | `anthropic/claude-haiku-4-5` |

---

## 5. Boas Práticas

<!-- @RULE: Best Practices -->
1. **Agents**: Um propósito claro por agent
2. **Skills**: Reutilizáveis, genéricas, compostas
3. **Commands**: Ações específicas e atômicas
4. **Modelos**: Usar o modelo apropriado para a tarefa
