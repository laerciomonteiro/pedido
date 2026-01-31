# Como Adicionar Novos Agents

## Visao Geral

Agents sao assistentes de IA especializados que podem ser configurados para tarefas e workflows especificos.

## Tipos de Agents

### 1. Primary Agents
- Interacao direta com usuario
- Alternar com tecla **Tab**
- Acesso a todas as ferramentas configuradas
- Built-in: **build** e **plan**

### 2. Subagents
- Invocados por primary agents para tarefas especificas
- Podem ser invocados manualmente com **@mencao**
- Built-in: **general** e **explore**

## Metodos de Configuracao

### Metodo 1: JSON (opencode.json)

```json
{
  "$schema": "https://opencode.ai/config.json",
  "agent": {
    "code-reviewer": {
      "description": "Reviews code for best practices",
      "mode": "subagent",
      "model": "anthropic/claude-sonnet-4-20250514",
      "prompt": "You are a code reviewer. Focus on security and performance.",
      "tools": {
        "write": false,
        "edit": false
      }
    }
  }
}
```

### Metodo 2: Markdown

Criar arquivo em:
- Global: `~/.config/opencode/agent/<nome>.md`
- Projeto: `.opencode/agent/<nome>.md`

```markdown
---
description: Reviews code for quality and best practices
mode: subagent
model: anthropic/claude-sonnet-4-20250514
temperature: 0.1
tools:
  write: false
  edit: false
  bash: false
---

You are in code review mode. Focus on:
- Code quality and best practices
- Potential bugs and edge cases
- Performance implications
- Security considerations
```

O nome do arquivo se torna o nome do agent (ex: `review.md` -> agent `review`).

## Opcoes de Configuracao

| Opcao | Descricao |
|-------|-----------|
| `description` | Descricao do agent (obrigatorio) |
| `mode` | `primary`, `subagent`, ou `all` |
| `model` | Override do modelo (formato: `provider/model-id`) |
| `temperature` | 0.0-1.0 (controla criatividade) |
| `maxSteps` | Limite de iteracoes |
| `prompt` | System prompt customizado |
| `tools` | Habilitar/desabilitar ferramentas |
| `permission` | Configurar permissoes por ferramenta |
| `disable` | Desabilitar o agent |

## Permissoes por Agent

```json
{
  "agent": {
    "build": {
      "permission": {
        "edit": "ask",
        "bash": {
          "git push": "ask",
          "git *": "allow",
          "*": "ask"
        }
      }
    }
  }
}
```

Valores de permissao:
- `"allow"` - Permite sem aprovacao
- `"ask"` - Solicita aprovacao
- `"deny"` - Bloqueia

## Criar Agent via CLI

```bash
opencode agent create
```

Este comando interativo:
1. Pergunta onde salvar (global ou projeto)
2. Solicita descricao
3. Gera prompt apropriado
4. Permite selecionar ferramentas
5. Cria arquivo markdown

## Exemplos de Agents

### Agent de Documentacao
```markdown
---
description: Writes and maintains project documentation
mode: subagent
tools:
  bash: false
---

You are a technical writer. Create clear documentation.
Focus on: Clear explanations, proper structure, code examples.
```

### Agent de Seguranca
```markdown
---
description: Performs security audits
mode: subagent
tools:
  write: false
  edit: false
---

You are a security expert. Look for:
- Input validation vulnerabilities
- Authentication flaws
- Data exposure risks
```

## Uso

### Alternar entre Primary Agents
```
<Tab>
```

### Invocar Subagent
```
@general help me search for this function
@review analyze this code
```

## Locais de Configuracao

| Local | Caminho |
|-------|---------|
| Global | `~/.config/opencode/agent/` |
| Projeto | `.opencode/agent/` |
| Config JSON | `opencode.json` → `agent` key |
