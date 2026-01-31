# Ferramentas de Busca

## Glob - Busca de Arquivos por Padrao

### Descricao
Ferramenta rapida de correspondencia de padroes de arquivos que funciona com qualquer tamanho de codebase.

### Parametros

| Parametro | Tipo | Obrigatorio | Descricao |
|-----------|------|-------------|-----------|
| `pattern` | string | Sim | Padrao glob para corresponder |
| `path` | string | Nao | Diretorio para buscar (padrao: diretorio atual) |

### Exemplos de Padroes

```bash
**/*.js          # Todos os arquivos JavaScript
**/*.{ts,tsx}    # Arquivos TypeScript e TSX
src/**/*.py      # Python dentro de src/
**/test*.js      # Arquivos JS que comecam com "test"
**/.env*         # Arquivos de ambiente
```

### Teste Realizado

```
Input:  pattern="**/*.js", path="/home/guilherme/Workdir/playground"
Output: 
  - /home/guilherme/Workdir/playground/test-files/javascript/utils.js
  - /home/guilherme/Workdir/playground/test-files/javascript/app.js
```

### Comportamento
- Retorna caminhos completos
- Ordenado por data de modificacao
- Funciona recursivamente

---

## Grep - Busca de Conteudo por Regex

### Descricao
Ferramenta rapida de busca de conteudo usando expressoes regulares.

### Parametros

| Parametro | Tipo | Obrigatorio | Descricao |
|-----------|------|-------------|-----------|
| `pattern` | string | Sim | Expressao regular para buscar |
| `path` | string | Nao | Diretorio para buscar |
| `include` | string | Nao | Filtro de arquivos (ex: "*.js") |

### Exemplos de Padroes

```regex
function\s+\w+     # Declaracoes de funcao
import.*from      # Imports ES6
TODO|FIXME|HACK   # Comentarios de tarefa
console\.log      # Logs de console
async\s+function  # Funcoes assincronas
```

### Teste Realizado

```
Input:  pattern="function|print|console"
Output: Found 3 matches
  - app.js:1     console.log("hello");
  - main.py:1    print("hello python")
  - utils.js:1   function test() { return 42; }
```

### Comportamento
- Retorna arquivo, linha e conteudo
- Suporta sintaxe regex completa
- Ordenado por data de modificacao

---

## Quando Usar Qual?

| Cenario | Ferramenta |
|---------|------------|
| Encontrar arquivos por nome/extensao | Glob |
| Encontrar arquivos contendo texto | Grep |
| Encontrar definicao de funcao | Grep |
| Listar todos os arquivos .ts | Glob |
| Encontrar imports de um modulo | Grep |

## Nota Importante

Para buscas abertas ou exploracao de codebase, usar a ferramenta **Task** com agente **explore** ao inves de Glob/Grep diretamente. Isso economiza contexto e permite analise mais profunda.
