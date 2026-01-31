# Agentes e Subagentes

## Arquitetura

O OpenCode opera em uma arquitetura de **agente principal + subagentes**:

```
+------------------+
|  OpenCode Main   |  <-- Agente Principal (voce interage com ele)
+------------------+
        |
        | Task tool
        v
+-------+--------+
|               |
v               v
+--------+  +--------+
| general|  | explore|  <-- Subagentes Especializados
+--------+  +--------+
```

## Tipos de Subagentes

### 1. General

**Proposito**: Tarefas complexas multi-step e execucao paralela.

**Ferramentas Disponiveis**:
| Ferramenta | Disponivel |
|------------|------------|
| Bash | Sim |
| Read | Sim |
| Write | Sim |
| Edit | Sim |
| Glob | Sim |
| Grep | Sim |
| WebFetch | Sim |
| Skill | Sim |
| Task | NAO |
| TodoWrite | NAO |
| TodoRead | NAO |

**Capacidades Testadas**:
- Criar arquivos autonomamente
- Executar comandos bash
- Ler e analisar multiplos arquivos
- Produzir relatorios detalhados

**Limitacoes**:
- Nao pode delegar tarefas (sem Task)
- Nao pode gerenciar todos (sem TodoWrite/TodoRead)
- Comandos interativos nao suportados
- Timeout de 2-10 minutos

### 2. Explore

**Proposito**: Exploracao rapida de codebases.

**Niveis de Profundidade**:
| Nivel | Uso |
|-------|-----|
| `quick` | Buscas basicas, estrutura geral |
| `medium` | Exploracao moderada |
| `very thorough` | Analise completa em multiplos locais |

**Ferramentas Disponiveis**:
| Ferramenta | Disponivel |
|------------|------------|
| Bash | Sim |
| Read | Sim |
| Glob | Sim |
| Grep | Sim |
| WebFetch | Sim |
| Write | NAO |
| Edit | NAO |
| Task | NAO |
| TodoWrite | NAO |

**Capacidades Testadas**:
- Mapear estruturas de diretorios
- Contar arquivos e linhas
- Identificar linguagens
- Analisar padroes de codigo
- Buscar funcoes e variaveis

**Limitacoes**:
- SOMENTE LEITURA (nao pode criar/editar)
- Nao pode executar codigo
- Nao pode fazer commits

## Comparacao

| Aspecto | general | explore |
|---------|---------|---------|
| Escrita de arquivos | Sim | Nao |
| Execucao de bash | Sim | Sim |
| Delegacao (Task) | Nao | Nao |
| Gerenciamento de tarefas | Nao | Nao |
| Foco | Execucao | Analise |

## Invocacao via Task

```
Task(
  description: "Descricao curta 3-5 palavras",
  prompt: "Instrucoes detalhadas para o subagente",
  subagent_type: "general" | "explore",
  session_id?: "ID para continuar sessao existente"
)
```

## Execucao Paralela de Subagentes

**CONFIRMADO**: Multiplos subagentes podem ser lancados em paralelo!

No teste, 4 subagentes foram lancados simultaneamente:
1. general - tarefa complexa
2. explore (quick)
3. explore (very thorough)
4. general - teste de autonomia

Todos retornaram resultados independentemente.

## Notas Importantes

1. **Resultado nao visivel ao usuario**: O agente principal deve resumir
2. **Stateless por padrao**: Use session_id para persistencia
3. **Prompt detalhado**: Subagente nao tem contexto da conversa
4. **Especificar output**: Diga exatamente o que deve retornar
