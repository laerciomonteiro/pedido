# OpenCode - Visao Geral

## O que e o OpenCode?

OpenCode e um agente de codificacao CLI (Command Line Interface) interativo projetado para auxiliar em tarefas de engenharia de software. Ele se autodescreve como "the best coding agent on the planet".

## Ambiente de Execucao

| Propriedade | Valor |
|-------------|-------|
| Diretorio de trabalho | `/home/guilherme/Workdir/playground` |
| Plataforma | Linux |
| E repositorio Git? | Nao (no momento do teste) |
| Data do teste | Tue Jan 06 2026 |

## Arquitetura

O OpenCode opera como um **agente principal** que pode:

1. Executar ferramentas diretamente
2. Delegar tarefas a **subagentes especializados**
3. Gerenciar listas de tarefas (todos)
4. Executar multiplas operacoes em paralelo

## Principios de Funcionamento

### Objetividade Profissional
- Prioriza precisao tecnica sobre validacao emocional
- Discorda quando necessario
- Evita superlativos desnecessarios

### Comunicacao
- Respostas curtas e concisas
- Formato Markdown (GitHub-flavored)
- Evita emojis (a menos que solicitado)
- Output renderizado em fonte monoespaco

### Autonomia
- Alto nivel de autonomia para tarefas de desenvolvimento
- Pode criar, editar e excluir arquivos
- Pode executar comandos shell
- Restricoes de seguranca para operacoes Git destrutivas

## Proximos Documentos

- [02-ferramentas.md](./02-ferramentas.md) - Ferramentas disponiveis
- [03-ferramentas-busca.md](./03-ferramentas-busca.md) - Ferramentas de busca
- [04-sistema-tarefas.md](./04-sistema-tarefas.md) - Sistema de tarefas
- [05-agentes.md](./05-agentes.md) - Agentes e subagentes
- [06-execucao-paralela.md](./06-execucao-paralela.md) - Execucao paralela
- [07-autonomia.md](./07-autonomia.md) - Nivel de autonomia
