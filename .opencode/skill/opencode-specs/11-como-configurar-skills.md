# Como Configurar Skills

## O que sao Skills?

Skills sao instrucoes reutilizaveis que o OpenCode pode descobrir e carregar sob demanda. Elas permitem definir comportamentos especializados que podem ser invocados quando necessario.

## Estrutura de Arquivos

Criar uma pasta por skill com um arquivo `SKILL.md` dentro:

```
.opencode/skill/<nome>/SKILL.md
~/.config/opencode/skill/<nome>/SKILL.md
```

Tambem suporta locais compativeis com Claude:
```
.claude/skills/<nome>/SKILL.md
~/.claude/skills/<nome>/SKILL.md
```

## Descoberta de Skills

O OpenCode busca skills:
1. Caminha do diretorio atual ate a raiz do git worktree
2. Carrega `skill/*/SKILL.md` em `.opencode/`
3. Carrega `.claude/skills/*/SKILL.md`
4. Carrega definicoes globais de `~/.config/opencode/skill/`

## Formato do SKILL.md

```markdown
---
name: git-release
description: Create consistent releases and changelogs
license: MIT
compatibility: opencode
metadata:
  audience: maintainers
  workflow: github
---

## What I do
- Draft release notes from merged PRs
- Propose a version bump
- Provide a copy-pasteable `gh release create` command

## When to use me
Use this when you are preparing a tagged release.
Ask clarifying questions if the target versioning scheme is unclear.
```

## Campos do Frontmatter

| Campo | Obrigatorio | Descricao |
|-------|-------------|-----------|
| `name` | Sim | Nome da skill (1-64 caracteres) |
| `description` | Sim | Descricao (1-1024 caracteres) |
| `license` | Nao | Licenca da skill |
| `compatibility` | Nao | Compatibilidade |
| `metadata` | Nao | Mapa string-string |

## Regras de Nomes

O `name` deve:
- Ter 1-64 caracteres
- Ser alfanumerico minusculo
- Usar hifens simples como separadores
- NAO comecar ou terminar com `-`
- NAO ter `--` consecutivos
- Corresponder ao nome do diretorio

Regex: `^[a-z0-9]+(-[a-z0-9]+)*$`

## Como o Agent Ve as Skills

O OpenCode lista skills disponiveis na descricao da ferramenta `skill`:

```xml
<available_skills>
  <skill>
    <name>git-release</name>
    <description>Create consistent releases and changelogs</description>
  </skill>
</available_skills>
```

O agent carrega uma skill chamando:
```typescript
skill({ name: "git-release" })
```

## Configurar Permissoes

### Global (opencode.json)
```json
{
  "permission": {
    "skill": {
      "pr-review": "allow",
      "internal-*": "deny",
      "experimental-*": "ask",
      "*": "allow"
    }
  }
}
```

### Por Agent (markdown)
```markdown
---
permission:
  skill:
    "documents-*": "allow"
---
```

### Por Agent Built-in (JSON)
```json
{
  "agent": {
    "plan": {
      "permission": {
        "skill": {
          "internal-*": "allow"
        }
      }
    }
  }
}
```

## Valores de Permissao

| Permissao | Comportamento |
|-----------|---------------|
| `allow` | Skill carrega imediatamente |
| `deny` | Skill oculta do agent, acesso rejeitado |
| `ask` | Usuario solicitado para aprovacao |

## Desabilitar a Ferramenta Skill

### Para agents customizados:
```markdown
---
tools:
  skill: false
---
```

### Para agents built-in:
```json
{
  "agent": {
    "plan": {
      "tools": {
        "skill": false
      }
    }
  }
}
```

## Troubleshooting

Se uma skill nao aparecer:

1. Verificar se `SKILL.md` esta em maiusculas
2. Confirmar que frontmatter tem `name` e `description`
3. Garantir que nomes sao unicos em todos os locais
4. Verificar permissoes - skills com `deny` sao ocultas

## Exemplo Completo

Criar `.opencode/skill/code-style/SKILL.md`:

```markdown
---
name: code-style
description: Enforce project coding standards and conventions
license: MIT
metadata:
  category: development
---

## Purpose
Ensure consistent code style across the project.

## Guidelines
- Use TypeScript strict mode
- Prefer functional components in React
- Use camelCase for variables
- Use PascalCase for components

## When to use
Invoke when writing new code or reviewing PRs.
```
