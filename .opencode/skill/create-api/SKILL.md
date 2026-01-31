---
name: create-api
description: "Complete API creation guide for Nuxt 4 server routes using defineEventHandler pattern. Covers CRUD operations, validation with Zod, file uploads with Vercel Blob, and Redis operations with Upstash. Use when: (1) Creating new API endpoints, (2) Implementing CRUD operations for a resource, (3) Adding file upload functionality, (4) Integrating with Redis database, (5) Refactoring existing API routes."
license: MIT
---

# Skill: Create API (Nuxt Server)

> Design and implement RESTful APIs using Nuxt Server patterns with defineEventHandler.

---

## Quick Start

### For Simple Single-Endpoint (15 min)
1. Add Zod schema
2. Implement route handler with `defineEventHandler`
3. Basic validation and response
4. Quick test

### For Full API Implementation (30-60 min)
1. Requirements & Data Modeling
2. Schema & Validation
3. Route Implementation
4. Utils/Services
5. Testing

---

## Main Prompt

```
Perform a **comprehensive API design and implementation** for Nuxt Server.

## Target Resource
[Specify the resource, e.g., `denuncia`, `apreensao`]

## API Scope
[Describe the resource and actions, e.g., "CRUD for denuncias"]

---

## Phase 1: Requirements & Data Modeling (PLANNING)
1.  **Define the Resource**: What entity does this API manage?
2.  **List Operations**:
    -   [ ] `GET /api/[resource]` - List with pagination
    -   [ ] `GET /api/[resource]/[id]` - Get single
    -   [ ] `POST /api/[resource]` - Create
    -   [ ] `PUT /api/[resource]/[id]` - Update
    -   [ ] `DELETE /api/[resource]/[id]` - Delete
    -   [ ] Custom actions
3.  **Define Data Shapes**: Request/response JSON structures
4.  **Identify Dependencies**: Auth middleware, rate limits, Redis keys
5.  **Document in AGENTS.md if needed**

---

## Phase 2: Schema & Validation (EXECUTION)
1.  **Create Zod Schemas**:
    -   [ ] Request body schemas
    -   [ ] Query params schemas
    -   [ ] Response schemas
2.  **Export TypeScript Types**: Inferred from Zod
3.  **File**: `app/utils/validation.ts` or `server/utils/[resource].ts`

---

## Phase 3: Route Implementation (EXECUTION)
1.  **Create Route Handlers**: `server/api/[resource].get.ts`, etc.
2.  **Follow Patterns**:
    -   [ ] Use `defineEventHandler`
    -   [ ] Parse body with `readBody` or `readMultipartFormData`
    -   [ ] Validate with Zod, throw createError on failure
    -   [ ] Get params with `getRouterParam`
    -   [ ] Return data directly or throw createError

---

## Phase 4: Utils/Services (EXECUTION)
1.  **Utils**: `server/utils/[resource].ts`
    -   Business logic and data access
    -   Redis operations with @upstash/redis
    -   File uploads with @vercel/blob
2.  **Types**: `app/types/index.ts`

---

## Phase 5: Testing (VERIFICATION)
1.  **API Tests**: All HTTP methods, validation, auth, not found
2.  **Unit Tests**: Business logic in utils
3.  **Run**: `npm run test:run`

---

## Expected Deliverables
1.  Zod schemas
2.  Route handlers (defineEventHandler)
3.  Utils/Services layer
4.  TypeScript types
5.  Unit tests
6.  JSDoc documentation

---

## Constraints
-   **Follow Nuxt Conventions**: File-based routing in server/api/
-   **Use createError**: For all error responses
-   **Validate Everything**: All input through Zod
-   **Thin Routes**: Parse, validate, call utils, respond
```

---

## File Structure

```
server/
├── api/
│   ├── [resource].get.ts           # GET /api/[resource] - List
│   ├── [resource].post.ts          # POST /api/[resource] - Create
│   ├── [resource]/
│   │   ├── [id].get.ts             # GET /api/[resource]/:id
│   │   ├── [id].put.ts             # PUT /api/[resource]/:id
│   │   ├── [id].delete.ts          # DELETE /api/[resource]/:id
│   │   └── [id]/
│   │       └── status.post.ts      # POST /api/[resource]/:id/status
├── middleware/
│   └── auth.ts                     # Authentication middleware
└── utils/
    └── [resource].ts               # Business logic + Redis
app/
├── types/
│   └── index.ts                    # TypeScript interfaces
└── utils/
    └── validation.ts               # Zod schemas (shared)
test/
└── unit/
    └── api/
        └── [resource].test.ts      # API tests
```

---

## Essential Patterns

### GET Route (List with Validation)

```typescript
// server/api/denuncias.get.ts
import { z } from 'zod'

const querySchema = z.object({
  page: z.coerce.number().min(1).default(1),
  limit: z.coerce.number().min(1).max(100).default(20),
  status: z.enum(['pendente', 'em_analise', 'resolvida']).optional()
})

export default defineEventHandler(async (event) => {
  const result = querySchema.safeParse(getQuery(event))
  if (!result.success) {
    throw createError({ statusCode: 400, data: result.error.flatten() })
  }
  
  const denuncias = await listDenuncias(result.data)
  return { success: true, data: denuncias }
})
```

### POST Route (Create with Zod)

```typescript
// server/api/denuncia.post.ts
import { z } from 'zod'

const createSchema = z.object({
  tipo: z.string().min(1),
  descricao: z.string().min(10),
  urgencia: z.enum(['baixa', 'media', 'alta', 'critica'])
})

export default defineEventHandler(async (event) => {
  const result = createSchema.safeParse(await readBody(event))
  if (!result.success) {
    throw createError({ statusCode: 400, data: result.error.flatten() })
  }
  
  const denuncia = await createDenuncia(result.data)
  return { success: true, data: { token: denuncia.token } }
})
```

### Route with Parameters

```typescript
// server/api/denuncia/[token].get.ts
export default defineEventHandler(async (event) => {
  const token = getRouterParam(event, 'token')
  if (!token) throw createError({ statusCode: 400 })
  
  const denuncia = await getDenunciaByToken(token)
  if (!denuncia) throw createError({ statusCode: 404 })
  
  return { success: true, data: denuncia }
})
```

---

## References

For detailed patterns and examples, see:
- [patterns.md](./references/patterns.md) - Complete code patterns
- [testing.md](./references/testing.md) - Testing patterns and examples

---

## Quick Reference

### Auto-imported in server/

```typescript
defineEventHandler  // Create route handler
readBody           // Parse JSON body
readMultipartFormData // Parse multipart/form-data
getQuery           // Get URL query params
getRouterParam     // Get route params
createError        // Create error response
useRuntimeConfig   // Access runtime config
```

### Error Status Codes

| Code | When to Use |
|------|-------------|
| 400 | Invalid input, validation error |
| 401 | Not authenticated |
| 403 | Not authorized |
| 404 | Resource not found |
| 500 | Internal server error |

---

## Skill Chaining

### Can Be Called By
- **audit-code-review**: When API refactoring is necessary
- **planning**: When new features require API endpoints

### Chains To
- **audit-code-review**: After implementation for validation
- **data-architect**: When database schema changes needed

---

## Evolution

### v2.1.0 (2026-01-07)
- Adapted to Anthropic skill standard
- Simplified frontmatter (name, description, license)
- Split detailed patterns to references/patterns.md
- Split testing patterns to references/testing.md
- Reduced SKILL.md from 875 to ~300 lines

### v2.0.0 (2026-01-07)
- Complete rewrite for Nuxt Server patterns
- Updated file structure for server/api/
- Added Zod validation examples
- Added @upstash/redis patterns
- Added @vercel/blob upload patterns

### v1.0.0 (2026-01-06)
- Initial version with Express patterns
