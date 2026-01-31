# API Route Patterns Reference

> Detailed code patterns for Nuxt 4 Server API implementation.

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

## Basic Route Pattern (GET)

```typescript
// server/api/denuncias.get.ts
import { z } from 'zod'

const querySchema = z.object({
  page: z.coerce.number().min(1).default(1),
  limit: z.coerce.number().min(1).max(100).default(20),
  status: z.enum(['pendente', 'em_analise', 'resolvida']).optional(),
  cidade: z.string().optional()
})

export default defineEventHandler(async (event) => {
  const query = getQuery(event)
  
  // Validate query params
  const result = querySchema.safeParse(query)
  if (!result.success) {
    throw createError({
      statusCode: 400,
      statusMessage: 'Parametros invalidos',
      data: result.error.flatten()
    })
  }
  
  const { page, limit, status, cidade } = result.data
  
  try {
    const denuncias = await listDenuncias({ page, limit, status, cidade })
    return {
      success: true,
      data: denuncias.items,
      pagination: {
        page,
        limit,
        total: denuncias.total
      }
    }
  } catch (error) {
    console.error('Erro ao listar denuncias:', error)
    throw createError({
      statusCode: 500,
      statusMessage: 'Erro interno do servidor'
    })
  }
})
```

---

## Create Route Pattern (POST)

```typescript
// server/api/denuncia.post.ts
import { z } from 'zod'

const createSchema = z.object({
  tipo: z.string().min(1, 'Tipo obrigatorio'),
  descricao: z.string().min(10, 'Descricao deve ter no minimo 10 caracteres'),
  urgencia: z.enum(['baixa', 'media', 'alta', 'critica']),
  cidade: z.string().min(1),
  bairro: z.string().min(1),
  local: z.string().optional(),
  latitude: z.number().optional(),
  longitude: z.number().optional()
})

export default defineEventHandler(async (event) => {
  const body = await readBody(event)
  
  // Validate request body
  const result = createSchema.safeParse(body)
  if (!result.success) {
    throw createError({
      statusCode: 400,
      statusMessage: 'Dados invalidos',
      data: result.error.flatten()
    })
  }
  
  try {
    const denuncia = await createDenuncia(result.data)
    
    // Send notification
    await sendTelegramNotification(denuncia)
    
    return {
      success: true,
      data: {
        token: denuncia.token,
        pin: denuncia.pin,
        message: 'Denuncia registrada com sucesso'
      }
    }
  } catch (error) {
    console.error('Erro ao criar denuncia:', error)
    throw createError({
      statusCode: 500,
      statusMessage: 'Erro ao registrar denuncia'
    })
  }
})
```

---

## Route with Parameters (GET by ID)

```typescript
// server/api/denuncia/[token].get.ts
export default defineEventHandler(async (event) => {
  const token = getRouterParam(event, 'token')
  
  if (!token) {
    throw createError({
      statusCode: 400,
      statusMessage: 'Token obrigatorio'
    })
  }
  
  try {
    const denuncia = await getDenunciaByToken(token)
    
    if (!denuncia) {
      throw createError({
        statusCode: 404,
        statusMessage: 'Denuncia nao encontrada'
      })
    }
    
    return {
      success: true,
      data: denuncia
    }
  } catch (error) {
    if (error.statusCode) throw error // Re-throw createError
    
    console.error('Erro ao buscar denuncia:', error)
    throw createError({
      statusCode: 500,
      statusMessage: 'Erro interno do servidor'
    })
  }
})
```

---

## Update Route Pattern (PUT)

```typescript
// server/api/denuncia/[id]/status.post.ts
import { z } from 'zod'

const statusSchema = z.object({
  status: z.enum(['pendente', 'em_analise', 'resolvida', 'arquivada']),
  observacao: z.string().optional()
})

export default defineEventHandler(async (event) => {
  const id = getRouterParam(event, 'id')
  const body = await readBody(event)
  
  if (!id) {
    throw createError({
      statusCode: 400,
      statusMessage: 'ID obrigatorio'
    })
  }
  
  const result = statusSchema.safeParse(body)
  if (!result.success) {
    throw createError({
      statusCode: 400,
      statusMessage: 'Dados invalidos',
      data: result.error.flatten()
    })
  }
  
  try {
    const updated = await updateDenunciaStatus(id, result.data)
    
    if (!updated) {
      throw createError({
        statusCode: 404,
        statusMessage: 'Denuncia nao encontrada'
      })
    }
    
    return {
      success: true,
      data: updated
    }
  } catch (error) {
    if (error.statusCode) throw error
    
    console.error('Erro ao atualizar status:', error)
    throw createError({
      statusCode: 500,
      statusMessage: 'Erro ao atualizar status'
    })
  }
})
```

---

## File Upload Pattern (Multipart)

```typescript
// server/api/denuncia-com-midia.post.ts
import { put } from '@vercel/blob'
import { z } from 'zod'

const MAX_FILE_SIZE = 50 * 1024 * 1024 // 50MB
const ALLOWED_TYPES = ['image/jpeg', 'image/png', 'image/webp', 'video/mp4']

export default defineEventHandler(async (event) => {
  const formData = await readMultipartFormData(event)
  
  if (!formData) {
    throw createError({
      statusCode: 400,
      statusMessage: 'Dados do formulario ausentes'
    })
  }
  
  // Parse fields and files
  const fields: Record<string, string> = {}
  const files: Array<{ filename: string; data: Buffer; type: string }> = []
  
  for (const item of formData) {
    if (item.filename) {
      // Validate file
      if (item.data.length > MAX_FILE_SIZE) {
        throw createError({
          statusCode: 400,
          statusMessage: `Arquivo ${item.filename} excede o limite de 50MB`
        })
      }
      if (!ALLOWED_TYPES.includes(item.type || '')) {
        throw createError({
          statusCode: 400,
          statusMessage: `Tipo de arquivo nao permitido: ${item.type}`
        })
      }
      files.push({
        filename: item.filename,
        data: item.data,
        type: item.type || 'application/octet-stream'
      })
    } else if (item.name) {
      fields[item.name] = item.data.toString()
    }
  }
  
  // Validate required fields
  const fieldSchema = z.object({
    tipo: z.string().min(1),
    descricao: z.string().min(10),
    urgencia: z.enum(['baixa', 'media', 'alta', 'critica'])
  })
  
  const result = fieldSchema.safeParse(fields)
  if (!result.success) {
    throw createError({
      statusCode: 400,
      statusMessage: 'Campos invalidos',
      data: result.error.flatten()
    })
  }
  
  try {
    // Upload files to Vercel Blob
    const uploadedUrls: string[] = []
    for (const file of files) {
      const blob = await put(
        `denuncias/${Date.now()}-${file.filename}`,
        file.data,
        { 
          access: 'public',
          contentType: file.type
        }
      )
      uploadedUrls.push(blob.url)
    }
    
    // Create denuncia with media URLs
    const denuncia = await createDenuncia({
      ...result.data,
      midias: uploadedUrls
    })
    
    return {
      success: true,
      data: {
        token: denuncia.token,
        pin: denuncia.pin,
        midias: uploadedUrls.length
      }
    }
  } catch (error) {
    console.error('Erro ao criar denuncia com midia:', error)
    throw createError({
      statusCode: 500,
      statusMessage: 'Erro ao processar denuncia'
    })
  }
})
```

---

## Protected Route with Auth Middleware

```typescript
// server/api/admin/denuncias.get.ts
export default defineEventHandler(async (event) => {
  // Check auth (middleware should have set this)
  const session = event.context.session
  
  if (!session?.isAdmin) {
    throw createError({
      statusCode: 401,
      statusMessage: 'Acesso nao autorizado'
    })
  }
  
  const query = getQuery(event)
  
  try {
    const denuncias = await listAllDenuncias(query)
    return {
      success: true,
      data: denuncias
    }
  } catch (error) {
    console.error('Erro ao listar denuncias admin:', error)
    throw createError({
      statusCode: 500,
      statusMessage: 'Erro interno do servidor'
    })
  }
})
```

---

## Server Utils Pattern (Redis)

```typescript
// server/utils/denuncia.ts
import { Redis } from '@upstash/redis'

const redis = new Redis({
  url: process.env.KV_REST_API_URL!,
  token: process.env.KV_REST_API_TOKEN!
})

interface CreateDenunciaInput {
  tipo: string
  descricao: string
  urgencia: string
  cidade: string
  bairro: string
  local?: string
  latitude?: number
  longitude?: number
  midias?: string[]
}

export async function createDenuncia(input: CreateDenunciaInput) {
  const id = crypto.randomUUID()
  const token = generateToken()
  const pin = generatePin()
  const now = new Date().toISOString()
  
  const denuncia = {
    id,
    token,
    pin,
    status: 'pendente',
    createdAt: now,
    updatedAt: now,
    ...input
  }
  
  // Use pipeline for atomic operations
  const pipeline = redis.pipeline()
  
  // Store main record
  pipeline.hset(`denuncia:${id}`, denuncia)
  
  // Create indices
  pipeline.set(`token:${token}`, id)
  pipeline.zadd('denuncias:by_date', { score: Date.now(), member: id })
  pipeline.sadd(`idx:cidade:${input.cidade}`, id)
  pipeline.sadd(`idx:status:pendente`, id)
  
  await pipeline.exec()
  
  return denuncia
}

export async function getDenunciaByToken(token: string) {
  const id = await redis.get<string>(`token:${token}`)
  if (!id) return null
  
  const denuncia = await redis.hgetall(`denuncia:${id}`)
  return denuncia
}

export async function listDenuncias(options: {
  page: number
  limit: number
  status?: string
  cidade?: string
}) {
  const { page, limit, status, cidade } = options
  const start = (page - 1) * limit
  const end = start + limit - 1
  
  // Get IDs from sorted set
  let ids: string[]
  
  if (cidade) {
    ids = await redis.smembers(`idx:cidade:${cidade}`)
  } else if (status) {
    ids = await redis.smembers(`idx:status:${status}`)
  } else {
    ids = await redis.zrange('denuncias:by_date', start, end, { rev: true })
  }
  
  // Fetch all records
  const pipeline = redis.pipeline()
  for (const id of ids.slice(start, end + 1)) {
    pipeline.hgetall(`denuncia:${id}`)
  }
  
  const results = await pipeline.exec()
  const items = results.filter(Boolean)
  
  return {
    items,
    total: ids.length
  }
}

export async function updateDenunciaStatus(id: string, data: {
  status: string
  observacao?: string
}) {
  const exists = await redis.exists(`denuncia:${id}`)
  if (!exists) return null
  
  const oldStatus = await redis.hget(`denuncia:${id}`, 'status')
  
  const pipeline = redis.pipeline()
  
  // Update record
  pipeline.hset(`denuncia:${id}`, {
    status: data.status,
    updatedAt: new Date().toISOString(),
    ...(data.observacao && { observacao: data.observacao })
  })
  
  // Update indices
  if (oldStatus) {
    pipeline.srem(`idx:status:${oldStatus}`, id)
  }
  pipeline.sadd(`idx:status:${data.status}`, id)
  
  await pipeline.exec()
  
  return redis.hgetall(`denuncia:${id}`)
}

// Helper functions
function generateToken(): string {
  return crypto.randomUUID().replace(/-/g, '').substring(0, 16)
}

function generatePin(): string {
  return Math.floor(1000 + Math.random() * 9000).toString()
}
```

---

## Zod Schema Definitions

```typescript
// app/utils/validation.ts
import { z } from 'zod'

// Denuncia schemas
export const createDenunciaSchema = z.object({
  tipo: z.string().min(1, 'Tipo e obrigatorio'),
  descricao: z.string().min(10, 'Descricao deve ter no minimo 10 caracteres'),
  urgencia: z.enum(['baixa', 'media', 'alta', 'critica']),
  cidade: z.string().min(1, 'Cidade e obrigatoria'),
  bairro: z.string().min(1, 'Bairro e obrigatorio'),
  local: z.string().optional(),
  latitude: z.number().optional(),
  longitude: z.number().optional()
})

export const updateStatusSchema = z.object({
  status: z.enum(['pendente', 'em_analise', 'resolvida', 'arquivada']),
  observacao: z.string().optional()
})

export const paginationSchema = z.object({
  page: z.coerce.number().min(1).default(1),
  limit: z.coerce.number().min(1).max(100).default(20)
})

// Type exports (inferred from Zod)
export type CreateDenunciaInput = z.infer<typeof createDenunciaSchema>
export type UpdateStatusInput = z.infer<typeof updateStatusSchema>
export type PaginationInput = z.infer<typeof paginationSchema>

// Validation helper
export function validateOrThrow<T>(
  schema: z.ZodSchema<T>,
  data: unknown
): T {
  const result = schema.safeParse(data)
  if (!result.success) {
    throw createError({
      statusCode: 400,
      statusMessage: 'Dados invalidos',
      data: result.error.flatten()
    })
  }
  return result.data
}
```

---

## TypeScript Interfaces

```typescript
// app/types/index.ts
export interface Denuncia {
  id: string
  token: string
  pin: string
  status: 'pendente' | 'em_analise' | 'resolvida' | 'arquivada'
  tipo: string
  descricao: string
  urgencia: 'baixa' | 'media' | 'alta' | 'critica'
  cidade: string
  bairro: string
  local?: string
  latitude?: number
  longitude?: number
  midias?: string[]
  createdAt: string
  updatedAt: string
  observacao?: string
  interacoes?: Interacao[]
}

export interface Interacao {
  id: string
  denunciaId: string
  tipo: 'mensagem' | 'status_change' | 'sistema'
  conteudo: string
  autor: 'cidadao' | 'admin' | 'sistema'
  createdAt: string
}

export interface PaginatedResponse<T> {
  success: boolean
  data: T[]
  pagination: {
    page: number
    limit: number
    total: number
  }
}

export interface ApiResponse<T> {
  success: boolean
  data: T
  message?: string
}

export interface ApiError {
  success: false
  statusCode: number
  statusMessage: string
  data?: Record<string, unknown>
}
```
