# Execucao Paralela

## Capacidades de Paralelismo

O OpenCode suporta execucao paralela em multiplos niveis:

### 1. Chamadas de Ferramentas Paralelas

Multiplas ferramentas podem ser chamadas simultaneamente em uma unica resposta.

**Teste Realizado**:
```
Chamadas simultaneas:
1. Bash: mkdir -p opencode-destil
2. Bash: mkdir -p test-files/{javascript,python,typescript}
3. Bash: echo "Teste 1" && sleep 1 && echo "Teste 2"

Resultado: Todas executaram em paralelo
```

### 2. Subagentes Paralelos

Multiplos subagentes podem ser lancados simultaneamente via Task.

**Teste Realizado**:
```
4 Tasks lancadas simultaneamente:
1. general - exploracao de diretorios
2. explore (quick) - estrutura basica
3. explore (very thorough) - analise completa
4. general - teste de autonomia

Resultado: Todos retornaram resultados independentes
```

## Regras para Paralelismo

### PODE paralelizar quando:
- Chamadas sao **independentes**
- Nao ha dependencia de valores entre chamadas
- Resultados nao afetam parametros de outras chamadas

### NAO paralelizar quando:
- Uma chamada depende do resultado de outra
- Operacoes precisam ser sequenciais (mkdir antes de cp)
- Valores de parametros vem de resultados anteriores

## Exemplos

### Paralelo (multiplas chamadas independentes)
```
Cenario: Buscar arquivos JS, TS e PY simultaneamente

Chamada 1: Glob pattern="**/*.js"
Chamada 2: Glob pattern="**/*.ts"
Chamada 3: Glob pattern="**/*.py"

-> Todas executam ao mesmo tempo
```

### Sequencial (dependencias)
```
Cenario: Criar diretorio e depois copiar arquivo

Passo 1: Bash mkdir -p novo-dir
Passo 2: (aguarda) Bash cp arquivo.txt novo-dir/

-> Passo 2 precisa esperar Passo 1
```

### Sequencial com && (dentro de Bash)
```
Cenario: Git add, commit, push

Bash: git add . && git commit -m "msg" && git push

-> Executa em sequencia dentro de um unico comando
```

## Otimizacao de Performance

### Maximizar Paralelismo
1. Identificar operacoes independentes
2. Agrupar todas em uma unica resposta
3. Deixar engine executar em paralelo

### Exemplo Otimizado
```
Usuario: "Analise o projeto e rode os testes"

Resposta do Agente (1 mensagem, 4 chamadas paralelas):
1. Glob **/*.ts -> encontrar arquivos
2. Grep "test|spec" -> encontrar testes
3. Bash npm test -> rodar testes
4. Bash npm run lint -> rodar linter
```

## Confirmacao Experimental

**Data**: Jan 06 2026

| Teste | Resultado |
|-------|-----------|
| 3 comandos Bash em paralelo | SUCESSO |
| 4 subagentes Task em paralelo | SUCESSO |
| Glob + Grep + Task em paralelo | SUCESSO |
| Write de 6 arquivos em paralelo | SUCESSO |

## Limitacoes

- Nao ha limite explicito de chamadas paralelas documentado
- Cada subagente tem seu proprio timeout
- Resultados grandes podem ser truncados
