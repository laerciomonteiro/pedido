## TASK COMPLETE

### Objective
Aprimorar o componente `CityStatsChart` e suas APIs relacionadas para suportar UI de carregamento, cache incremental no Redis e integração com o assistente IA.

### Deliverables
- `app/components/CityStatsChart.vue`: Adicionado prop `loadingCidades` e indicador de carregamento no dropdown.
- `app/pages/admin/index.vue`: Passagem do estado `loadingCidades` para o componente filho.
- `server/api/denuncia.post.ts`: Implementado incremento atômico (`HINCRBY`) no hash `metrics:cidade:{cidade}:tipo`.
- `server/api/admin/stats-by-city.get.ts`: Implementada leitura otimizada (O(1)) do hash de métricas, com fallback para contagem manual.
- `server/api/admin/rebuild-indices.post.ts`: Atualizado script de reconstrução para popular os novos hashes de métricas.
- `server/utils/gemini.ts`: Atualizada função `getDistributionByType` para usar métricas otimizadas e corrigidos erros de tipagem TypeScript.

### Subtasks Completed
- [x] UX: Loading spinner no dropdown de cidades
- [x] Performance: Cache incremental (Write) em `denuncia.post.ts`
- [x] Performance: Leitura otimizada (Read) em `stats-by-city.get.ts`
- [x] Migration: Script de rebuild atualizado para novas métricas
- [x] AI: Integração do Gemini com métricas otimizadas

### Decisions Made
1. **Schema do Redis**: Utilizado Hash `metrics:cidade:{cidade}:tipo` -> `{ trafico: N, porte: M, ... }`. Isso permite leitura O(1) de todos os tipos para uma cidade sem precisar fazer scan ou contagem manual.
2. **Fallback Strategy**: Se o hash estiver vazio (ex: nova cidade sem denúncias ou cache expirado/não populado), o sistema faz fallback transparente para o método antigo (contagem via `smembers`), garantindo consistência.
3. **Migration**: O script de rebuild agora limpa e repopula esses hashes, permitindo "hidratar" o cache para dados existentes.
4. **AI Context**: O Gemini agora acessa esses dados rápidos, reduzindo latência nas respostas sobre estatísticas locais.

### Notes for Main Orchestrator
Execute o script de rebuild (`POST /api/admin/rebuild-indices`) ou clique no botão "Atualizar Métricas" no dashboard para garantir que as denúncias antigas sejam contabilizadas na nova estrutura de dados otimizada.

### Status: COMPLETE