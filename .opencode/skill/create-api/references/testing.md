# API Testing Patterns Reference

> Testing patterns and examples for Nuxt 4 Server API routes.

---

## API Route Test Pattern

```typescript
// test/unit/api/denuncia.test.ts
import { describe, it, expect, vi } from 'vitest'

describe('API: /api/denuncia', () => {
  describe('POST /api/denuncia', () => {
    it('should create denuncia with valid data', async () => {
      const body = {
        tipo: 'trafico',
        descricao: 'Venda de drogas na esquina da rua principal',
        urgencia: 'alta',
        cidade: 'Fortaleza',
        bairro: 'Centro'
      }
      
      const response = await $fetch('/api/denuncia', {
        method: 'POST',
        body
      })
      
      expect(response.success).toBe(true)
      expect(response.data.token).toBeDefined()
      expect(response.data.pin).toBeDefined()
    })
    
    it('should return 400 for invalid data', async () => {
      const body = {
        tipo: '',
        descricao: 'curto',
        urgencia: 'invalida'
      }
      
      await expect(
        $fetch('/api/denuncia', {
          method: 'POST',
          body
        })
      ).rejects.toMatchObject({
        statusCode: 400
      })
    })
  })
  
  describe('GET /api/denuncia/[token]', () => {
    it('should return 404 for invalid token', async () => {
      await expect(
        $fetch('/api/denuncia/invalid-token')
      ).rejects.toMatchObject({
        statusCode: 404
      })
    })
  })
})
```

---

## Quick Reference

### Nuxt Server Imports (Auto-imported)

```typescript
// These are auto-imported in server/
defineEventHandler  // Create route handler
readBody           // Parse JSON body
readMultipartFormData // Parse multipart/form-data
getQuery           // Get URL query params
getRouterParam     // Get route params (/api/[id])
getRouterParams    // Get all route params
createError        // Create error response
setResponseStatus  // Set HTTP status
setResponseHeader  // Set response header
getCookie          // Get cookie value
setCookie          // Set cookie
useRuntimeConfig   // Access runtime config
```

### File Naming Convention

| File Name | HTTP Method | Route |
|-----------|-------------|-------|
| `users.get.ts` | GET | `/api/users` |
| `users.post.ts` | POST | `/api/users` |
| `users/[id].get.ts` | GET | `/api/users/:id` |
| `users/[id].put.ts` | PUT | `/api/users/:id` |
| `users/[id].delete.ts` | DELETE | `/api/users/:id` |
| `users/[id]/posts.get.ts` | GET | `/api/users/:id/posts` |

### Error Status Codes

| Code | When to Use |
|------|-------------|
| 400 | Invalid input, validation error |
| 401 | Not authenticated |
| 403 | Not authorized (authenticated but no permission) |
| 404 | Resource not found |
| 409 | Conflict (duplicate) |
| 422 | Unprocessable entity |
| 500 | Internal server error |

---

## Utils Testing Pattern

```typescript
// test/unit/utils/denuncia.test.ts
import { describe, it, expect, vi, beforeEach } from 'vitest'
import { createDenuncia, getDenunciaByToken } from '~/server/utils/denuncia'

// Mock Redis
vi.mock('@upstash/redis', () => ({
  Redis: vi.fn().mockImplementation(() => ({
    hset: vi.fn().mockResolvedValue('OK'),
    hgetall: vi.fn().mockResolvedValue({ id: '123', token: 'abc' }),
    get: vi.fn().mockResolvedValue('123'),
    pipeline: vi.fn().mockReturnValue({
      hset: vi.fn().mockReturnThis(),
      set: vi.fn().mockReturnThis(),
      zadd: vi.fn().mockReturnThis(),
      sadd: vi.fn().mockReturnThis(),
      exec: vi.fn().mockResolvedValue([])
    })
  }))
}))

describe('Utils: denuncia', () => {
  beforeEach(() => {
    vi.clearAllMocks()
  })
  
  describe('createDenuncia', () => {
    it('should create denuncia and return token', async () => {
      const input = {
        tipo: 'trafico',
        descricao: 'Test description',
        urgencia: 'alta',
        cidade: 'Fortaleza',
        bairro: 'Centro'
      }
      
      const result = await createDenuncia(input)
      
      expect(result).toHaveProperty('id')
      expect(result).toHaveProperty('token')
      expect(result).toHaveProperty('pin')
      expect(result.status).toBe('pendente')
    })
  })
  
  describe('getDenunciaByToken', () => {
    it('should return null for invalid token', async () => {
      const redis = require('@upstash/redis').Redis
      redis.mockImplementation(() => ({
        get: vi.fn().mockResolvedValue(null)
      }))
      
      const result = await getDenunciaByToken('invalid-token')
      
      expect(result).toBeNull()
    })
  })
})
```

---

## Integration Test Pattern

```typescript
// test/integration/api/denuncia.test.ts
import { describe, it, expect, beforeAll, afterAll } from 'vitest'
import { setup, $fetch } from '@nuxt/test-utils'

describe('Integration: Denuncia API', async () => {
  await setup({
    server: true
  })
  
  let createdToken: string
  
  it('should create and retrieve denuncia', async () => {
    // Create
    const createResponse = await $fetch('/api/denuncia', {
      method: 'POST',
      body: {
        tipo: 'porte_ilegal',
        descricao: 'Teste de integracao',
        urgencia: 'media',
        cidade: 'Fortaleza',
        bairro: 'Centro'
      }
    })
    
    expect(createResponse.success).toBe(true)
    createdToken = createResponse.data.token
    
    // Retrieve
    const getResponse = await $fetch(`/api/denuncia/${createdToken}`)
    
    expect(getResponse.success).toBe(true)
    expect(getResponse.data.tipo).toBe('porte_ilegal')
  })
})
```

---

## Test Commands

```bash
# Run tests in watch mode
npm test

# Run tests once
npm run test:run

# Run with coverage
npm run test:coverage

# Run specific test file
npm test -- denuncia.test.ts

# Run with UI
npm run test:ui
```
