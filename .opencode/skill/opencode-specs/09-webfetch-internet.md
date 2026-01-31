# Capacidade de Busca na Internet

## WebFetch - Ferramenta de Acesso a Web

### Confirmacao: SIM, OpenCode pode acessar a internet!

A ferramenta **WebFetch** permite buscar conteudo de URLs.

## Parametros

| Parametro | Tipo | Obrigatorio | Descricao |
|-----------|------|-------------|-----------|
| `url` | string | Sim | URL completa para buscar |
| `format` | string | Sim | `"markdown"`, `"text"`, ou `"html"` |
| `timeout` | number | Nao | Timeout em segundos (max 120) |

## Testes Realizados

### Teste 1: Documentacao do OpenCode
```
URL: https://opencode.ai/docs
Formato: markdown
Resultado: SUCESSO - Retornou documentacao completa
```

### Teste 2: Hacker News
```
URL: https://news.ycombinator.com
Formato: markdown
Resultado: SUCESSO - Retornou lista de noticias
```

### Teste 3: GitHub Repository
```
URL: https://github.com/anomalyco/opencode
Formato: markdown
Resultado: SUCESSO - Retornou README e info do repo
```

### Teste 4: Documentacao Especifica
```
URLs testadas:
- https://opencode.ai/docs/agents
- https://opencode.ai/docs/skills
- https://opencode.ai/docs/config
- https://opencode.ai/docs/custom-tools

Resultado: SUCESSO em todas
```

## Comportamentos

1. **HTTP -> HTTPS**: URLs HTTP sao automaticamente atualizadas para HTTPS
2. **Redirects**: Se houver redirect para outro host, uma nova requisicao deve ser feita
3. **Timeout**: Maximo de 120 segundos
4. **Resumo**: Conteudo muito grande pode ser resumido

## Capacidades Confirmadas

- Acessar documentacao oficial
- Buscar noticias e artigos
- Acessar repositorios GitHub
- Buscar paginas web publicas
- Retornar em diferentes formatos (markdown, text, html)

## Uso Recomendado

1. **Perguntas sobre OpenCode**: Buscar docs em `https://opencode.ai/docs`
2. **Pesquisa tecnica**: Buscar em sites especializados
3. **Contexto de codigo**: Buscar documentacao de bibliotecas
4. **Noticias**: Acessar sites de tecnologia

## Limitacoes

- URLs devem ser validas e completas
- Nao ha suporte para autenticacao
- Timeout maximo de 120 segundos
- Conteudo pode ser resumido se muito grande
- NAO usar para operacoes que requerem login
