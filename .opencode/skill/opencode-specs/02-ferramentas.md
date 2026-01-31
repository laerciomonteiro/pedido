# Ferramentas do OpenCode

## Inventario Completo

O OpenCode (agente principal) tem acesso a **11 ferramentas**:

### Ferramentas de Sistema de Arquivos

| Ferramenta | Descricao | Parametros Principais |
|------------|-----------|----------------------|
| **Read** | Le arquivos do sistema | `filePath`, `offset`, `limit` |
| **Write** | Escreve/sobrescreve arquivos | `filePath`, `content` |
| **Edit** | Substituicao exata de strings | `filePath`, `oldString`, `newString`, `replaceAll` |

### Ferramentas de Busca

| Ferramenta | Descricao | Parametros Principais |
|------------|-----------|----------------------|
| **Glob** | Busca arquivos por padrao | `pattern`, `path` |
| **Grep** | Busca conteudo com regex | `pattern`, `path`, `include` |

### Ferramentas de Execucao

| Ferramenta | Descricao | Parametros Principais |
|------------|-----------|----------------------|
| **Bash** | Executa comandos shell | `command`, `timeout`, `workdir`, `description` |
| **Task** | Lanca subagentes | `description`, `prompt`, `subagent_type`, `session_id` |

### Ferramentas de Rede

| Ferramenta | Descricao | Parametros Principais |
|------------|-----------|----------------------|
| **WebFetch** | Busca conteudo de URLs | `url`, `format`, `timeout` |

### Ferramentas de Gerenciamento

| Ferramenta | Descricao | Parametros Principais |
|------------|-----------|----------------------|
| **TodoWrite** | Gerencia lista de tarefas | `todos` (array de objetos) |
| **TodoRead** | Le a lista de tarefas | `_placeholder` |
| **Skill** | Carrega instrucoes especificas | `name` |

## Restricoes de Uso

### Read
- Caminho deve ser absoluto
- Maximo 2000 linhas por padrao
- Linhas > 2000 caracteres sao truncadas
- Suporta leitura de imagens

### Write
- Sobrescreve arquivos existentes
- **Requer leitura previa** para arquivos existentes
- Preferir editar vs criar novos arquivos

### Edit
- Requer leitura previa do arquivo
- Falha se `oldString` nao for encontrada
- Falha se `oldString` aparecer multiplas vezes (sem contexto suficiente)

### Bash
- Timeout padrao: 2 minutos
- Timeout maximo: 10 minutos
- Output truncado em 30.000 caracteres
- NAO usar para operacoes de arquivo (usar ferramentas dedicadas)
- Suporta execucao em background

## Preferencias de Uso

```
Operacao          | Ferramenta Preferida | Evitar
------------------|---------------------|--------
Buscar arquivos   | Glob                | find, ls
Buscar conteudo   | Grep                | grep, rg
Ler arquivos      | Read                | cat, head, tail
Editar arquivos   | Edit                | sed, awk
Criar arquivos    | Write               | echo, cat <<EOF
Comunicar usuario | Texto direto        | echo, printf
```
