# Sistema de Tarefas (Todo)

## Visao Geral

O OpenCode possui um sistema integrado de gerenciamento de tarefas atraves das ferramentas **TodoWrite** e **TodoRead**.

## Ferramentas

### TodoWrite

Cria e atualiza a lista de tarefas.

```typescript
interface Todo {
  id: string;        // Identificador unico
  content: string;   // Descricao da tarefa
  status: string;    // Estado atual
  priority: string;  // Prioridade
}

// Parametro
todos: Todo[]  // Array completo de tarefas
```

### TodoRead

Le a lista de tarefas atual.

```typescript
// Parametro
_placeholder: boolean  // Sempre passar true
```

## Estados de Tarefa

| Estado | Descricao |
|--------|-----------|
| `pending` | Tarefa ainda nao iniciada |
| `in_progress` | Tarefa em andamento |
| `completed` | Tarefa finalizada com sucesso |
| `cancelled` | Tarefa cancelada/nao mais necessaria |

## Prioridades

| Prioridade | Uso |
|------------|-----|
| `high` | Tarefas criticas, bloquadoras |
| `medium` | Tarefas importantes, mas nao urgentes |
| `low` | Tarefas opcionais ou menores |

## Quando Usar

### DEVE usar quando:
- Tarefa requer 3+ passos distintos
- Tarefas complexas que precisam planejamento
- Usuario solicita lista de tarefas
- Usuario fornece multiplas tarefas
- Ao receber novas instrucoes (capturar requisitos)
- Apos completar tarefa (marcar e adicionar follow-ups)

### NAO usar quando:
- Tarefa unica e simples
- Tarefa trivial
- Menos de 3 passos triviais
- Conversa informacional

## Regras de Gerenciamento

1. **Uma tarefa in_progress por vez**
2. **Marcar completed imediatamente** apos terminar
3. **Completar tarefas existentes** antes de iniciar novas
4. **Cancelar tarefas irrelevantes**
5. **Criar itens especificos e acionaveis**
6. **Quebrar tarefas complexas** em passos menores

## Exemplo de Uso

```
Usuario: "Run the build and fix any type errors"

Agente cria:
1. [pending] Run the build
2. [pending] Fix any type errors

Agente inicia:
1. [in_progress] Run the build
2. [pending] Fix any type errors

Build encontra 10 erros, agente expande:
1. [completed] Run the build
2. [in_progress] Fix error in file1.ts
3. [pending] Fix error in file2.ts
...
```

## Beneficios

- **Visibilidade**: Usuario ve progresso
- **Organizacao**: Tarefas complexas ficam gerenciaveis
- **Rastreamento**: Nada e esquecido
- **Planejamento**: Facilita quebra de problemas
