# Custom Tools - Ferramentas Personalizadas

## Visao Geral

Custom tools sao funcoes que voce cria e que o LLM pode chamar durante conversas. Elas funcionam junto com as ferramentas built-in (read, write, bash, etc).

## Localizacao

Colocar em:
- Projeto: `.opencode/tool/`
- Global: `~/.config/opencode/tool/`

## Formato

Ferramentas sao definidas em **TypeScript** ou **JavaScript**, mas podem invocar scripts em **qualquer linguagem**.

## Estrutura Basica

```typescript
// .opencode/tool/database.ts
import { tool } from "@opencode-ai/plugin"

export default tool({
  description: "Query the project database",
  args: {
    query: tool.schema.string().describe("SQL query to execute"),
  },
  async execute(args) {
    // Sua logica aqui
    return `Executed query: ${args.query}`
  },
})
```

O **nome do arquivo** se torna o **nome da ferramenta**.

## Multiplas Tools por Arquivo

```typescript
// .opencode/tool/math.ts
import { tool } from "@opencode-ai/plugin"

export const add = tool({
  description: "Add two numbers",
  args: {
    a: tool.schema.number().describe("First number"),
    b: tool.schema.number().describe("Second number"),
  },
  async execute(args) {
    return args.a + args.b
  },
})

export const multiply = tool({
  description: "Multiply two numbers",
  args: {
    a: tool.schema.number().describe("First number"),
    b: tool.schema.number().describe("Second number"),
  },
  async execute(args) {
    return args.a * args.b
  },
})
```

Cria duas ferramentas: `math_add` e `math_multiply`.

## Argumentos com Zod

Use `tool.schema` (que e Zod) para definir tipos:

```typescript
args: {
  query: tool.schema.string().describe("SQL query"),
  limit: tool.schema.number().optional().describe("Max results"),
  tables: tool.schema.array(tool.schema.string()).describe("Tables to query")
}
```

Ou importe Zod diretamente:

```typescript
import { z } from "zod"

export default {
  description: "Tool description",
  args: {
    param: z.string().describe("Parameter"),
  },
  async execute(args, context) {
    return "result"
  },
}
```

## Contexto

Tools recebem contexto da sessao:

```typescript
async execute(args, context) {
  const { agent, sessionID, messageID } = context
  return `Agent: ${agent}, Session: ${sessionID}`
}
```

## Exemplo: Tool em Python

### Script Python
```python
# .opencode/tool/add.py
import sys

a = int(sys.argv[1])
b = int(sys.argv[2])
print(a + b)
```

### Definicao da Tool
```typescript
// .opencode/tool/python-add.ts
import { tool } from "@opencode-ai/plugin"

export default tool({
  description: "Add two numbers using Python",
  args: {
    a: tool.schema.number().describe("First number"),
    b: tool.schema.number().describe("Second number"),
  },
  async execute(args) {
    const result = await Bun.$`python3 .opencode/tool/add.py ${args.a} ${args.b}`.text()
    return result.trim()
  },
})
```

## Exemplo: Tool de API

```typescript
// .opencode/tool/weather.ts
import { tool } from "@opencode-ai/plugin"

export default tool({
  description: "Get current weather for a city",
  args: {
    city: tool.schema.string().describe("City name"),
  },
  async execute(args) {
    const response = await fetch(
      `https://wttr.in/${encodeURIComponent(args.city)}?format=j1`
    )
    const data = await response.json()
    return JSON.stringify(data.current_condition[0])
  },
})
```

## Exemplo: Tool de Banco de Dados

```typescript
// .opencode/tool/db-query.ts
import { tool } from "@opencode-ai/plugin"

export default tool({
  description: "Query SQLite database",
  args: {
    sql: tool.schema.string().describe("SQL query to execute"),
  },
  async execute(args) {
    const result = await Bun.$`sqlite3 ./database.db "${args.sql}"`.text()
    return result
  },
})
```

## Boas Praticas

1. **Descricoes claras**: Ajudam o LLM a escolher a ferramenta certa
2. **Validacao**: Use Zod para garantir tipos corretos
3. **Tratamento de erros**: Retorne mensagens uteis em caso de falha
4. **Seguranca**: Valide inputs antes de executar comandos
5. **Idempotencia**: Quando possivel, torne tools idempotentes

## Estrutura de Projeto

```
.opencode/
  tool/
    database.ts      -> ferramenta "database"
    math.ts          -> ferramentas "math_add", "math_multiply"
    api/
      weather.ts     -> ferramenta "api_weather"
    scripts/
      helper.py      -> script auxiliar (nao e ferramenta)
```
