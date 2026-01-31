# Nivel de Autonomia

## Classificacao: ALTO

O OpenCode possui um nivel **alto** de autonomia para tarefas de desenvolvimento de software.

## Capacidades Autonomas

### Sistema de Arquivos
| Operacao | Autonomia |
|----------|-----------|
| Ler arquivos | Total |
| Criar arquivos | Total |
| Editar arquivos | Total (requer leitura previa) |
| Excluir arquivos | Via Bash |
| Navegar diretorios | Total |

### Execucao de Comandos
| Operacao | Autonomia |
|----------|-----------|
| Comandos shell | Total (com timeout) |
| Scripts de build | Total |
| Testes automatizados | Total |
| Instalacao de pacotes | Total |
| Comandos interativos | NAO SUPORTADO |

### Git
| Operacao | Autonomia |
|----------|-----------|
| git status | Total |
| git diff | Total |
| git log | Total |
| git add | Apenas quando solicitado |
| git commit | Apenas quando solicitado |
| git push | Apenas quando solicitado |
| git push --force | PROIBIDO para main/master |
| git rebase -i | NAO SUPORTADO (interativo) |

### Delegacao
| Operacao | Autonomia |
|----------|-----------|
| Lancar subagentes | Total |
| Multiplos subagentes paralelos | Total |
| Subagente criar arquivos | Sim (general) |
| Subagente executar bash | Sim (general, explore) |

## Restricoes de Seguranca

### Git Safety Protocol
1. **NUNCA** atualizar git config
2. **NUNCA** comandos destrutivos sem solicitacao
3. **NUNCA** skip hooks (--no-verify)
4. **NUNCA** force push para main/master
5. **NUNCA** commit sem solicitacao explicita
6. **EVITAR** git commit --amend (regras estritas)

### Arquivos Sensiveis
- NAO commitar .env, credentials.json
- Alertar usuario se solicitado

### Operacoes Irreversiveis
- Avisar antes de executar
- Confirmar com usuario quando apropriado

## Teste de Autonomia do Subagente

### Comando Enviado ao Subagente General:
```
1. Criar arquivo autonomous-test.txt
2. Executar comando bash
3. Verificar criacao
```

### Resultado:
```
Arquivo criado: SIM
Bash executado: SIM
Verificacao: SUCESSO

Conteudo: "Criado autonomamente pelo subagente general"
```

**Conclusao**: Subagentes tambem possuem alto nivel de autonomia (com restricoes).

## Hierarquia de Autonomia

```
1. OpenCode (Agente Principal)
   - Todas as ferramentas
   - Gerenciamento de tarefas
   - Delegacao de subagentes
   
2. Subagente General
   - Ferramentas de sistema de arquivos
   - Bash
   - Web
   - NAO pode delegar
   
3. Subagente Explore
   - Apenas leitura
   - Busca e analise
   - NAO pode modificar
```

## Quando a Autonomia e Limitada

1. **Commits Git**: Requer solicitacao explicita
2. **Push para remoto**: Requer solicitacao explicita
3. **Operacoes destrutivas**: Requer confirmacao
4. **Comandos interativos**: Nao suportados
5. **Criacao de documentacao**: Apenas quando solicitado
6. **Uso de emojis**: Apenas quando solicitado

## Filosofia de Autonomia

O OpenCode opera com o principio de **autonomia responsavel**:
- Fazer o que e necessario para completar a tarefa
- Pedir confirmacao para acoes de alto impacto
- Nunca fazer mais do que foi solicitado em areas sensiveis
- Priorizar seguranca sobre conveniencia
