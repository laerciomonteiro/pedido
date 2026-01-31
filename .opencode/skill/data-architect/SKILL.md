---
name: data-architect
description: "Expert guidance on data architecture for modern systems, including schema design, storage selection, indexing strategies, and Redis/Upstash patterns. Provides expertise from 25 years of data engineering experience at global scale. Use when: (1) Designing data schemas and key structures, (2) Choosing storage strategies (Redis, Upstash), (3) Planning data pipelines and access patterns, (4) Implementing search and indexing with Sets and Sorted Sets, (5) Optimizing Redis queries and pipeline operations."
license: MIT
---

# Skill: Data Architect

> Design data schemas, storage strategies, and persistence with expertise from 25 years of data engineering.

---

## Quick Start

### For Small Additions (< 1 hour)
1. Add field to existing Hash
2. Update TypeScript types
3. Test existing queries

### For New Data Modules (2-4 hours)
1. Analyze access patterns (SOP-001)
2. Create TypeScript interfaces
3. Implement Redis keys with indexes
4. Add pipeline operations
5. Document access patterns

### For Breaking Schema Migrations (multiple sessions)
1. Create parallel key namespace
2. Implement dual-write
3. Migrate data in background
4. Deprecate old keys
5. Cleanup

---

## Main Prompt

```
You are an experienced Data Architect with 25 years of experience architecting data systems at global scale. Your task is [INSERT TASK].

PROJECT CONTEXT:
- Platform: Nuxt 4 / Vue 3 / TypeScript
- Storage: Upstash Redis (@upstash/redis)
- Connectivity: Online (serverless edge functions)
- Cache strategy: TTL-based with invalidation

CURRENT CONTEXT:
- Module: [MODULE NAME]
- Current data: [DESCRIBE CURRENT STRUCTURE]
- Problems: [LIST ISSUES]

REQUIREMENTS:
1. Think at scale (thousands of documents)
2. Maintain query performance < 100ms
3. Use Redis data structures efficiently
4. Allow schema evolution

DELIVERABLES:
- Key naming conventions
- TypeScript interfaces
- Index definitions (Sets, Sorted Sets)
- Caching strategy with TTL
- Pipeline operations for batch writes

Remember: "Make it work, make it right, make it fast" - but you always think of all three simultaneously.
```

---

## Key Naming Conventions

| Pattern | Usage | Example |
|---------|-------|---------|
| `entity:${id}` | Single entity (Hash) | `denuncia:abc123` |
| `entity_por_${field}` | Sorted Set by field | `denuncias_por_data` |
| `idx:${field}:${value}` | Index Set | `idx:cidade:fortaleza` |
| `metrics:${name}` | Metrics Hash | `metrics:daily_count` |
| `cache:${key}` | Cached data with TTL | `cache:admin:stats` |
| `token:${token}` | Token lookup | `token:xyz789` |
| `counter:${name}` | Atomic counters | `counter:denuncias` |

### Key Naming Rules

1. **Use colons as separators**: `namespace:entity:id`
2. **Lowercase with underscores**: `denuncias_por_data`
3. **Prefix by type**: `idx:` for indexes, `cache:` for cached data
4. **Include version for breaking changes**: `v2:denuncia:${id}`

---

## Data Structure Selection

| Usage Pattern | Recommended Structure |
|---------------|----------------------|
| Single Entity | Hash (`hset`, `hgetall`) |
| Time-based ordering | Sorted Set (`zadd`, `zrange`) |
| Field filtering | Set indexes (`sadd`, `sinter`) |
| Ordered append-only | List (`rpush`, `lrange`) |
| Simple key-value | String (`set`, `get`) |
| Unique items | Set (`sadd`, `smembers`) |
| Counters | String with `incr`/`decr` |

---

## Essential Patterns

### Hash for Main Entities

```typescript
import { Redis } from '@upstash/redis'
const redis = Redis.fromEnv()

// Store entity
await redis.hset(`denuncia:${id}`, {
  token, tipo, urgencia, status, cidade,
  createdAt: Date.now()
})

// Get entity
const denuncia = await redis.hgetall(`denuncia:${id}`)
```

### Sorted Set for Time Ordering

```typescript
// Add to sorted set
await redis.zadd('denuncias_por_data', { score: Date.now(), member: id })

// Get newest 20
const ids = await redis.zrange('denuncias_por_data', -20, -1, { rev: true })
```

### Set for Field Indexes

```typescript
// Create index
await redis.sadd(`idx:cidade:${cidade}`, id)

// Query with AND
const results = await redis.sinter('idx:cidade:fortaleza', 'idx:status:pendente')
```

### Pipeline for Atomic Writes

```typescript
const pipeline = redis.pipeline()
pipeline.hset(`denuncia:${id}`, data)
pipeline.zadd('denuncias_por_data', { score: Date.now(), member: id })
pipeline.sadd(`idx:cidade:${data.cidade}`, id)
await pipeline.exec()
```

---

## References

For detailed Redis patterns, see:
- [redis-patterns.md](./references/redis-patterns.md) - Complete Redis/Upstash patterns

---

## Standard Operating Procedures

### SOP-001: Schema Design

Before creating any data structure:

1. **Understand Access Patterns**
   - Which queries will be most frequent?
   - Read-heavy or write-heavy?
   - Which fields will be filtered/sorted?

2. **Choose Adequate Redis Structure** (see table above)

3. **Define Indexes Strategically**
   - Create Set indexes for filterable fields
   - Use Sorted Sets for orderable fields
   - Maintain indexes in pipelines with writes

4. **Plan Key Evolution**
   - Use versioned key prefixes for breaking changes
   - Implement migration scripts
   - Support dual-read during migrations

### SOP-002: Performance Rules

1. **Always use pipelines** for multiple operations
2. **Cache expensive computations** with TTL
3. **Limit result sets** in sorted set queries
4. **Use `sinter`** instead of fetching and filtering

---

## Storage Reference for Sonaris

| Storage | Usage | TTL | Notes |
|---------|-------|-----|-------|
| Upstash Redis | Primary data store | Permanent | Hashes, Sets, Sorted Sets |
| Upstash Redis | Cache layer | 5-30 min | Stats, aggregations |
| Vercel Blob | Media files | Permanent | Photos, videos |
| Runtime Config | Secrets | N/A | API keys, passwords |

---

## Mantras

> "Data lasts longer than code"

> "Schema is destiny"

> "Indexes are trade-offs, not free features"

> "Pipeline everything, round trips kill performance"

> "Cache aggressively, invalidate precisely"

---

## Skill Chaining

### Chains To
- **migrate-database**: When schema changes needed
- **os-architect**: For system integration

### Can Be Chained From
- **audit-code-review**: When data patterns are broken
- **os-architect**: When planning data layer

---

## Evolution

### v2.1.0 (2026-01-07)
- Adapted to Anthropic skill standard
- Simplified frontmatter (name, description, license)
- Split detailed patterns to references/redis-patterns.md
- Reduced SKILL.md from 679 to ~250 lines

### v2.0.0 (2026-01-07)
- Adapted for Upstash Redis / Sonaris project
- Added Redis/Upstash patterns section
- Updated SOPs for Redis context
- Removed IndexedDB/OPFS references

### v1.0.0 (2026-01-06)
- Initial version with IndexedDB/OPFS focus
