# Redis/Upstash Patterns Reference

> Detailed Redis patterns for the Sonaris project using Upstash Redis.

---

## Data Structures

### Hash for Main Entities

```typescript
import { Redis } from '@upstash/redis'

const redis = Redis.fromEnv()

// Store a denuncia as Hash
await redis.hset(`denuncia:${id}`, {
  token: generateToken(),
  pin: hashedPin,
  tipo: 'porte_ilegal',
  urgencia: 'alta',
  status: 'pendente',
  cidade: 'fortaleza',
  estado: 'CE',
  bairro: 'centro',
  descricao: encryptedDescricao,
  latitude: -3.7319,
  longitude: -38.5267,
  createdAt: Date.now(),
  updatedAt: Date.now()
})

// Get full entity
const denuncia = await redis.hgetall(`denuncia:${id}`)

// Get specific fields
const status = await redis.hget(`denuncia:${id}`, 'status')
const fields = await redis.hmget(`denuncia:${id}`, 'status', 'tipo', 'urgencia')
```

### Sorted Set for Time-Based Queries

```typescript
// Add to sorted set (score = timestamp)
await redis.zadd('denuncias_por_data', {
  score: Date.now(),
  member: id
})

// Get most recent 100
const recentIds = await redis.zrange('denuncias_por_data', -100, -1)

// Get in date range
const startOfDay = new Date().setHours(0, 0, 0, 0)
const endOfDay = new Date().setHours(23, 59, 59, 999)
const todayIds = await redis.zrangebyscore(
  'denuncias_por_data',
  startOfDay,
  endOfDay
)

// Get with scores (timestamps)
const withScores = await redis.zrange('denuncias_por_data', 0, -1, {
  withScores: true
})
```

### Set for Field Indexes

```typescript
// Create indexes on write
await redis.sadd(`idx:cidade:${cidade}`, id)
await redis.sadd(`idx:status:${status}`, id)
await redis.sadd(`idx:tipo:${tipo}`, id)
await redis.sadd(`idx:urgencia:${urgencia}`, id)

// Query by single field
const fortalezaIds = await redis.smembers('idx:cidade:fortaleza')

// Intersection (AND query)
const pendentesFortaleza = await redis.sinter(
  'idx:cidade:fortaleza',
  'idx:status:pendente'
)

// Union (OR query)
const urgentes = await redis.sunion(
  'idx:urgencia:alta',
  'idx:urgencia:critica'
)

// Count members
const totalPendentes = await redis.scard('idx:status:pendente')
```

### List for Ordered Collections

```typescript
// Chat messages (append-only)
await redis.rpush(`chat:${denunciaId}`, JSON.stringify({
  id: messageId,
  remetente: 'admin',
  mensagem: 'Received your report',
  createdAt: Date.now()
}))

// Get last 50 messages
const messages = await redis.lrange(`chat:${denunciaId}`, -50, -1)

// Get all messages
const allMessages = await redis.lrange(`chat:${denunciaId}`, 0, -1)
```

---

## Query Patterns

### Get by ID

```typescript
const denuncia = await redis.hgetall(`denuncia:${id}`)
if (!denuncia || Object.keys(denuncia).length === 0) {
  throw createError({ statusCode: 404, message: 'Not found' })
}
```

### Get by Token (Secondary Index)

```typescript
// On create, store token -> id mapping
await redis.hset('denuncias_por_token', { [token]: id })

// Lookup by token
const id = await redis.hget('denuncias_por_token', token)
const denuncia = id ? await redis.hgetall(`denuncia:${id}`) : null
```

### Paginated List

```typescript
async function getDenuncias(page: number, limit: number = 20) {
  const start = (page - 1) * limit
  const end = start + limit - 1
  
  // Get IDs from sorted set (newest first)
  const ids = await redis.zrange('denuncias_por_data', start, end, {
    rev: true // Reverse order (newest first)
  })
  
  // Fetch all entities
  const pipeline = redis.pipeline()
  for (const id of ids) {
    pipeline.hgetall(`denuncia:${id}`)
  }
  const results = await pipeline.exec()
  
  // Get total count
  const total = await redis.zcard('denuncias_por_data')
  
  return {
    data: results.filter(Boolean),
    pagination: {
      page,
      limit,
      total,
      totalPages: Math.ceil(total / limit)
    }
  }
}
```

### Complex Filtering

```typescript
async function filterDenuncias(filters: {
  cidade?: string
  status?: string
  tipo?: string
}) {
  const indexKeys: string[] = []
  
  if (filters.cidade) indexKeys.push(`idx:cidade:${filters.cidade}`)
  if (filters.status) indexKeys.push(`idx:status:${filters.status}`)
  if (filters.tipo) indexKeys.push(`idx:tipo:${filters.tipo}`)
  
  if (indexKeys.length === 0) {
    // No filters, return all
    return redis.zrange('denuncias_por_data', 0, -1, { rev: true })
  }
  
  if (indexKeys.length === 1) {
    return redis.smembers(indexKeys[0])
  }
  
  // Intersection of all filters
  return redis.sinter(...indexKeys)
}
```

---

## Pipeline for Batch Operations

### Create with Indexes

```typescript
async function createDenuncia(data: Denuncia) {
  const id = generateId()
  const token = generateToken()
  const now = Date.now()
  
  const pipeline = redis.pipeline()
  
  // 1. Store main entity
  pipeline.hset(`denuncia:${id}`, {
    ...data,
    id,
    token,
    status: 'pendente',
    createdAt: now,
    updatedAt: now
  })
  
  // 2. Add to time-sorted index
  pipeline.zadd('denuncias_por_data', { score: now, member: id })
  
  // 3. Create field indexes
  pipeline.sadd(`idx:cidade:${data.cidade}`, id)
  pipeline.sadd(`idx:status:pendente`, id)
  pipeline.sadd(`idx:tipo:${data.tipo}`, id)
  pipeline.sadd(`idx:urgencia:${data.urgencia}`, id)
  
  // 4. Token lookup index
  pipeline.hset('denuncias_por_token', { [token]: id })
  
  // 5. Increment counter
  pipeline.incr('counter:denuncias')
  
  await pipeline.exec()
  
  return { id, token }
}
```

### Update with Index Maintenance

```typescript
async function updateDenunciaStatus(id: string, newStatus: string) {
  // Get current status
  const oldStatus = await redis.hget(`denuncia:${id}`, 'status')
  
  const pipeline = redis.pipeline()
  
  // Update entity
  pipeline.hset(`denuncia:${id}`, {
    status: newStatus,
    updatedAt: Date.now()
  })
  
  // Update indexes
  if (oldStatus) {
    pipeline.srem(`idx:status:${oldStatus}`, id)
  }
  pipeline.sadd(`idx:status:${newStatus}`, id)
  
  await pipeline.exec()
}
```

### Batch Read

```typescript
async function getDenunciasByIds(ids: string[]) {
  if (ids.length === 0) return []
  
  const pipeline = redis.pipeline()
  for (const id of ids) {
    pipeline.hgetall(`denuncia:${id}`)
  }
  
  const results = await pipeline.exec()
  return results.filter(Boolean)
}
```

---

## Cache with TTL

### Cache Stats

```typescript
const CACHE_TTL = 300 // 5 minutes

async function getAdminStats() {
  const cacheKey = 'cache:admin:stats'
  
  // Try cache first
  const cached = await redis.get(cacheKey)
  if (cached) {
    return typeof cached === 'string' ? JSON.parse(cached) : cached
  }
  
  // Compute stats
  const stats = await computeExpensiveStats()
  
  // Cache with TTL
  await redis.setex(cacheKey, CACHE_TTL, JSON.stringify(stats))
  
  return stats
}
```

### Invalidate Cache

```typescript
async function invalidateStatsCache() {
  await redis.del('cache:admin:stats')
}

// Invalidate by pattern (using scan)
async function invalidateCachePattern(pattern: string) {
  let cursor = 0
  const keysToDelete: string[] = []
  
  do {
    const [nextCursor, keys] = await redis.scan(cursor, {
      match: pattern,
      count: 100
    })
    cursor = nextCursor
    keysToDelete.push(...keys)
  } while (cursor !== 0)
  
  if (keysToDelete.length > 0) {
    await redis.del(...keysToDelete)
  }
}
```

---

## Metrics and Counters

```typescript
// Increment daily counter
const today = new Date().toISOString().split('T')[0]
await redis.hincrby('metrics:daily', today, 1)

// Get daily counts
const dailyMetrics = await redis.hgetall('metrics:daily')

// Atomic counter
const total = await redis.incr('counter:denuncias')

// Decrement
await redis.decr('counter:pendentes')
```

---

## Error Handling

```typescript
import { Redis } from '@upstash/redis'

const redis = Redis.fromEnv()

// Wrap with retry logic
async function withRetry<T>(
  fn: () => Promise<T>,
  maxRetries: number = 3
): Promise<T> {
  let lastError: Error
  
  for (let i = 0; i < maxRetries; i++) {
    try {
      return await fn()
    } catch (error) {
      lastError = error as Error
      if (i < maxRetries - 1) {
        await new Promise(r => setTimeout(r, 100 * (i + 1)))
      }
    }
  }
  
  throw lastError!
}

// Usage
const result = await withRetry(() => redis.hgetall(`denuncia:${id}`))
```

---

## Performance Optimization

### Use Pipelines for Multiple Operations

```typescript
// BAD: Multiple round trips
for (const id of ids) {
  const data = await redis.hgetall(`denuncia:${id}`)
  results.push(data)
}

// GOOD: Single pipeline
const pipeline = redis.pipeline()
for (const id of ids) {
  pipeline.hgetall(`denuncia:${id}`)
}
const results = await pipeline.exec()
```

### Cache Expensive Computations

```typescript
// Cache aggregations that don't change frequently
const stats = await getCachedOrCompute(
  'cache:admin:stats',
  300, // 5 min TTL
  computeStats
)
```

### Limit Result Sets

```typescript
// Always limit sorted set queries
const recent = await redis.zrange('denuncias_por_data', -100, -1, {
  rev: true
})

// Use pagination
const page = await redis.zrange('denuncias_por_data', 0, 19, {
  rev: true
})
```
