## TASK COMPLETE

### Objective
Simplificar Token para 10 Caracteres (Sem Hífens)

### Deliverables
- `server/api/denuncia.post.ts`: Atualizado `generateToken()` para 10 caracteres alfanuméricos sem hífens.

### Subtasks Completed
- [x] Analyze token usage and validation patterns in the codebase
- [x] Update generateToken in server/api/denuncia.post.ts
- [x] Update token validation logic (regex) in app/utils/validation.ts and other files if needed (Não foi necessário pois não há regex estrito para o token)

### Decisions Made
1. Mantido o uso de `crypto.randomInt` para segurança.
2. Não foi necessário alterar `app/utils/validation.ts` pois ele não valida o formato do token (apenas campos do formulário).
3. Não foi necessário alterar `app/pages/acompanhamento.vue` pois o input aceita qualquer texto e o modal de credenciais apenas exibe o valor retornado pela API.

### Status: COMPLETE
