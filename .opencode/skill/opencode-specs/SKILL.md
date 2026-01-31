---
name: opencode-specs
description: "The master engine specifications for OpenCode agent operations. Provides comprehensive documentation on engine architecture, tool usage (11+ tools), task management, sub-agent orchestration, and security protocols. Use when: (1) Understanding OpenCode capabilities and limitations, (2) Configuring agent behavior or permissions, (3) Debugging agent operations or tool issues, (4) Training new patterns or extending the engine."
license: MIT
---

<!-- @META: OpenCode Engine Specs -->
<!--
    File: .opencode/skill/opencode-specs/SKILL.md
    Version: 2.4.0
    Created: 2025-12-18
    Updated: 2026-01-07
    Scope: Master engine specifications for OpenCode agent operations
-->

# Skill: OpenCode Engine Specifications

<!-- @NOTE(spec-def): Definition -->
> The master engine specifications for OpenCode agent operations.

---

## Source Code Location

<!-- @NOTE(source): Codebase Reference -->
> **OpenCode Source Code**: `vendors/agents/opencode/packages/opencode/`

### Key Source Files

| File | Purpose |
|------|---------|
| `src/agent/agent.ts` | Agent definition and permission system |
| `src/config/config.ts` | Configuration schema and loading |
| `src/permission/next.ts` | Permission rules and evaluation |
| `src/skill/skill.ts` | Skill loading and management |
| `src/tool/*.ts` | Tool implementations |
| `src/session/*.ts` | Session management and prompts |

### Agent Configuration Schema

```typescript
// From src/agent/agent.ts
export const Info = z.object({
  name: z.string(),
  description: z.string().optional(),
  mode: z.enum(["subagent", "primary", "all"]),
  color: z.string().optional(),
  permission: PermissionNext.Ruleset,
  model: z.object({
    modelID: z.string(),
    providerID: z.string(),
  }).optional(),
  prompt: z.string().optional(),
  options: z.record(z.string(), z.any()),
  steps: z.number().int().positive().optional(),
})
```

### Permission System

```typescript
// From src/config/config.ts - Permission tools
// Available permissions:
// read, edit, glob, grep, list, bash, task,
// todowrite, todoread, webfetch, websearch, codesearch, skill,
// external_directory, doom_loop

// Example: Restrict agent to only planning (no code execution)
permission: {
  "*": "deny",           // Deny all by default
  task: "allow",         // Allow sub-agent delegation
  todowrite: "allow",    // Allow task management
  todoread: "allow",     // Allow reading tasks
  skill: "allow",        // Allow loading skills
}
```

---

## Purpose

<!-- @NOTE(purpose): Purpose -->
This skill provides the comprehensive specification for OpenCode operations:

- **Engine Architecture**: How the system works
- **Tool Usage**: All 11+ tools and their protocols
- **Task Management**: State persistence and decomposition
- **Sub-agent Orchestration**: Parallel execution patterns
- **Security Protocols**: What the engine can/cannot do

**Use when**:
- Understanding OpenCode capabilities
- Configuring agent behavior
- Debugging agent operations
- Training new patterns

---

## Approaches

### Quick (5 min)
- Read overview (01-visao-geral.md)
- Check specific tool docs

### Complete (1 hour)
- Full specification review
- All 13 modules
- Configuration deep-dive

### Reference
- Use as lookup during operations
- Chain from other skills for context

---

# OpenCode: The Development Engine Specification

## 1. Introdução e Contexto Estratégico

<!-- @NOTE(intro): Context -->
O OpenCode não é apenas um assistente de IA ou um chatbot; ele é a **Engine de Desenvolvimento (DevEngine)** central do seu projeto. Ele atua como a camada de execução e inteligência que reside entre o desenvolvedor humano e a infraestrutura do projeto.

### O Papel do OpenCode na Engine

<!-- @NOTE(role): Role -->
Como Engine, o OpenCode é responsável por:
- **Orquestração de Contexto**: Interpretar a hierarquia de documentos `AGENTS.md` para garantir que toda alteração de código respeite a "constituição" do projeto.
- **Automação de Ciclo de Vida**: Desde o scaffolding inicial até a verificação final via testes e linting, o agente gerencia a integridade do repositório.
- **Memória Operacional**: Através do sistema de `Tasks` e `Todos`, ele mantém a persistência do estado mental da sessão de codificação, permitindo decomposições complexas que humanos poderiam perder o rastro.

---

## 2. Filosofia Operacional

<!-- @NOTE(phil): Philosophy -->
A Engine opera sob três pilares fundamentais:

### I. Documentation-First (Semantic Awareness)
O OpenCode "vê" o projeto através de notas semânticas. Cada arquivo `AGENTS.md` serve como um contrato de API entre o humano e a Engine. O agente deve sempre buscar o "Porquê" (`@WHY`) antes de executar o "Como".

### II. Interface-First Implementation
Antes de escrever lógica, a Engine deve definir ou validar interfaces. Isso garante que a base de código permaneça modular e que os subagentes possam trabalhar em paralelo sem colisões de contrato.

### III. Autonomia com Responsabilidade
A Engine possui alto nível de autonomia (instalar dependências, rodar servidores, refatorar diretórios inteiros), mas é limitada por protocolos de segurança rigorosos (especialmente em Git e gestão de segredos) para garantir que a autonomia nunca resulte em degradação da estabilidade do sistema.

---

## 🗺️ Mapa de Conhecimento Modular (The Specs Deep-Dive)

<!-- @REF(.opencode/skill/opencode-specs/): Modules -->
Esta skill é um grafo de conhecimento. Abaixo estão os links para os módulos detalhados que compõem a inteligência da Engine:

### 🛠️ Camada de Execução (Core Tools)
- **[A Visão Geral da Engine](./01-visao-geral.md)**: Detalhes sobre o ambiente Linux, arquitetura de memória e identidade.
- **[Manual de Ferramentas (The Toolset)](./02-ferramentas.md)**: As 11 ferramentas que permitem à Engine interagir com o mundo físico.
- **[Estratégias de Busca](./03-ferramentas-busca.md)**: Como a Engine navega em monorepos massivos usando Regex e Glob.
- **[Gerenciamento de Estado (Tasks)](./04-sistema-tarefas.md)**: O protocolo `TodoWrite/TodoRead` que evita a deriva de contexto.

### 🧠 Camada de Inteligência e Paralelismo
- **[Orquestração de Subagentes](./05-agentes.md)**: Como a Engine delega carga de trabalho para especialistas (`explore`, `architect`, `debug`).
- **[Otimização Paralela](./06-execucao-paralela.md)**: Teoria e prática de execução simultânea para máxima performance.
- **[Capacidades de Autonomia](./07-autonomia.md)**: O que a Engine pode e não pode fazer sem supervisão humana.

### 🌐 Conectividade e Expansão
- **[Internet e WebFetch](./09-webfetch-internet.md)**: Protocolos para ingestão de documentação externa e pesquisa em tempo real.
- **[Configuração da Engine](./12-configuracao-completa.md)**: O arquivo `opencode.json` e a hierarquia de configurações.
- **[Desenvolvimento de Extensões](./13-custom-tools.md)**: Como expandir o vocabulário da Engine com novas ferramentas em TypeScript.

---

## MCP Server Configuration

<!-- @NOTE(mcp-config): MCP Setup -->
OpenCode supports the Model Context Protocol (MCP) to extend agent capabilities with external servers.

### Basic Configuration (`opencode.json`)

Configuration resides in the `mcp` object of your configuration file:

```json
{
  "mcp": {
    "git": {
      "type": "local",
      "command": ["docker", "run", "-i", "--rm", "mcp/git"],
      "enabled": true
    },
    "memory": {
      "type": "remote",
      "url": "https://mcp.example.com/memory",
      "headers": { "Authorization": "Bearer {env:MEMORY_KEY}" }
    }
  }
}
```

### Local Servers
Local servers run as subprocesses (stdio).
- `command`: Array of arguments (e.g., `["npx", "-y", "@modelcontextprotocol/server-filesystem"]`)
- `environment`: Object with environment variables

### Remote Servers
Remote servers are accessed via SSE (Server-Sent Events).
- `url`: Full URL to the MCP endpoint
- `headers`: HTTP headers (supports `{env:VAR}` expansion)

## Per-Agent Configuration

<!-- @NOTE(agent-config): Scoped Configuration -->
You can restrict or enable specific tools and MCP servers for individual agents in `opencode.json`.

```json
{
  "mcp": {
    "github": { "type": "local", "command": [...] },
    "sentry": { "type": "remote", "url": "..." }
  },
  // Default: disable these tools globally
  "tools": {
    "github*": false,
    "sentry*": false
  },
  "agent": {
    "release-manager": {
      "tools": {
        "github*": true,  // Enable GitHub only for this agent
        "sentry*": true
      }
    }
  }
}
```

## Skills Configuration

<!-- @NOTE(skill-config): Skill Management -->
Skills are defined in `.opencode/skill/<name>/SKILL.md`.

### Anatomy of a Skill
The `SKILL.md` frontmatter defines metadata and permissions:

```yaml
---
name: my-skill
description: Does amazing things
permissions:
  read: allow
  write: ask     # Ask user before writing
  webfetch: deny
---
```

### Permissions
- **allow**: Grant capability automatically
- **deny**: Block capability
- **ask**: Require user confirmation

### Agent Specific Skills
You can force specific skills for an agent in `opencode.json` or the agent definition:

```json
"agent": {
  "architect": {
    "skills": ["system-design", "diagram-gen"]
  }
}
```

## Practical Examples

### 1. GitHub MCP (Local)
Enable GitHub repository operations:
```json
"mcp": {
  "github": {
    "type": "local",
    "command": ["npx", "-y", "@modelcontextprotocol/server-github"],
    "environment": {
      "GITHUB_TOKEN": "{env:GITHUB_TOKEN}"
    }
  }
}
```

### 2. Sentry MCP (Remote)
Query error logs:
```json
"mcp": {
  "sentry": {
    "type": "remote",
    "url": "https://mcp.sentry.io/sse",
    "headers": { "Authorization": "Bearer {env:SENTRY_AUTH_TOKEN}" }
  }
}
```

### 3. Context7 (Research)
Deep research capabilities:
```json
"mcp": {
  "context7": {
    "type": "remote",
    "url": "https://api.context7.ai/mcp",
    "headers": { "x-api-key": "{env:CONTEXT7_KEY}" }
  }
}
```

---

## 🛡️ Protocolos de Segurança da Engine

<!-- @RULE: Security Protocols -->
- **Secret Zero**: Nunca, sob nenhuma circunstância, ler ou escrever arquivos `.env` ou chaves privadas.
- **Git Guard**: Operações destrutivas em ramos principais exigem confirmação explícita.
- **Verification Loop**: Nenhuma tarefa de código é considerada `completed` sem a execução dos comandos de verificação (`npm test`, `pnpm lint`, etc) identificados no projeto.

## How to Use This Skill

<!-- @NOTE(usage): Usage -->
When loading `opencode-specs`, the agent assumes "Engine Master" mode. It should use this knowledge repository to:
- Educate sub-agents on proper operation
- Validate actions against official specifications
- Serve as final authority on "How OpenCode should operate"

---

## Skill Chaining

### Can Be Chained From
- Any skill needing engine context
- Debugging operations
- Configuration questions

### Chains To
- None (reference skill)

---

## Evolution

### v2.5.0 (2026-01-07)
- Adapted to Anthropic skill standard
- Simplified frontmatter (name, description, license)
- Added comprehensive triggers in description

### v2.4.0 (2026-01-07)
- Added MCP Server Configuration section
- Added Per-Agent Configuration details
- Added Skills Configuration documentation
- Added practical examples (GitHub, Sentry, Context7)

### v2.3.0 (2026-01-07)
- Added Source Code Location section with codebase path
- Added key source files reference
- Added Agent Configuration Schema
- Added Permission System documentation

### v2.2.0 (2026-01-06)
- Enhanced with standard SKILL.md format
- Added approaches section
- Added skill chaining section
- Added evolution tracking

### v2.1.0 (2025-12-18)
- Modular documentation split
- Added 13 specification modules

### v2.0.0 (2025-12-18)
- Complete rewrite as Engine specification
