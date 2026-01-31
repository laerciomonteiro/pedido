# Performance Optimization Techniques

> Detailed code examples and patterns for Vue 3/Nuxt 4 performance optimization.
> Referenced by: `.opencode/skill/perf-optimizer/SKILL.md`

---

## SOP-005: Vue 3 Optimization Techniques

### Computed vs Watch

```vue
<!-- ANTES: Watch desnecessario -->
<script setup lang="ts">
const items = ref<Item[]>([])
const filteredItems = ref<Item[]>([]) // Recalcula manualmente

watch(items, (newItems) => {
  filteredItems.value = newItems.filter(i => i.active)
})
</script>

<!-- DEPOIS: Computed reativo -->
<script setup lang="ts">
const items = ref<Item[]>([])
const filteredItems = computed(() => items.value.filter(i => i.active))
</script>
```

### shallowRef para Objetos Grandes

```vue
<script setup lang="ts">
// ANTES: Reatividade profunda desnecessaria
const largeData = ref<BigObject>(fetchedData) // Observa todas as propriedades

// DEPOIS: Reatividade superficial
const largeData = shallowRef<BigObject>(fetchedData) // So observa .value
// Para atualizar: largeData.value = newData
</script>
```

### v-once para Conteudo Estatico

```vue
<template>
  <!-- ANTES: Re-renderiza toda vez -->
  <header>
    <h1>Sonaris - Sistema de Denuncias</h1>
    <p>Relatos anonimos para seguranca publica</p>
  </header>

  <!-- DEPOIS: Renderiza uma vez -->
  <header v-once>
    <h1>Sonaris - Sistema de Denuncias</h1>
    <p>Relatos anonimos para seguranca publica</p>
  </header>
</template>
```

### v-memo para Listas com Renders Caros

```vue
<template>
  <!-- ANTES: Re-renderiza todos os items -->
  <div v-for="item in items" :key="item.id">
    <ExpensiveComponent :data="item" />
  </div>

  <!-- DEPOIS: So re-renderiza se item.id ou item.version mudar -->
  <div v-for="item in items" :key="item.id" v-memo="[item.id, item.version]">
    <ExpensiveComponent :data="item" />
  </div>
</template>
```

### defineAsyncComponent para Code Splitting

```vue
<script setup lang="ts">
// ANTES: Importa todo no bundle inicial
import HeavyChart from '~/components/HeavyChart.vue'

// DEPOIS: Carrega sob demanda
const HeavyChart = defineAsyncComponent(() => 
  import('~/components/HeavyChart.vue')
)

// Com loading state
const HeavyChart = defineAsyncComponent({
  loader: () => import('~/components/HeavyChart.vue'),
  loadingComponent: LoadingSpinner,
  delay: 200,
  timeout: 3000
})
</script>
```

### Virtual Scrolling com VueUse

```vue
<script setup lang="ts">
import { useVirtualList } from '@vueuse/core'

const allItems = ref<Denuncia[]>([]) // 10,000 items

const { list, containerProps, wrapperProps } = useVirtualList(allItems, {
  itemHeight: 80
})
</script>

<template>
  <div v-bind="containerProps" class="h-[600px] overflow-auto">
    <div v-bind="wrapperProps">
      <div v-for="{ data, index } in list" :key="data.id" class="h-20">
        <DenunciaCard :denuncia="data" />
      </div>
    </div>
  </div>
</template>
```

---

## SOP-006: Nuxt Data Fetching Optimization

### useFetch com Cache

```vue
<script setup lang="ts">
// ANTES: Sem cache
const { data } = await useFetch('/api/denuncias-stats')

// DEPOIS: Com cache key e TTL
const { data } = await useFetch('/api/denuncias-stats', {
  key: 'denuncias-stats',
  getCachedData(key, nuxtApp) {
    return nuxtApp.payload.data[key] || nuxtApp.static.data[key]
  }
})

// Lazy loading para dados nao-criticos
const { data: apreensoes, pending } = await useFetch('/api/apreensoes', {
  lazy: true
})
</script>
```

### Server Components

```vue
<!-- components/StaticFooter.server.vue -->
<!-- Renderiza apenas no servidor, zero JS no cliente -->
<template>
  <footer class="bg-gray-800 text-white p-4">
    <p>Sonaris - Sistema de Denuncias Anonimas</p>
    <p>Versao {{ version }}</p>
  </footer>
</template>

<script setup lang="ts">
const version = useRuntimeConfig().public.appVersion
</script>
```

### Route Rules para Caching

```typescript
// nuxt.config.ts
export default defineNuxtConfig({
  routeRules: {
    // Cache estatico para pagina de apreensoes publicas
    '/apreensao/**': { swr: 3600 },
    
    // ISR para dashboard (revalida a cada 5 min)
    '/admin': { isr: 300 },
    
    // Pre-render paginas estaticas
    '/': { prerender: true },
    
    // Cache no edge para API de stats
    '/api/denuncias-stats': { 
      cache: { maxAge: 60 },
      cors: true
    }
  }
})
```

---

## SOP-007: Redis Performance Patterns

### Pipeline para Operacoes em Lote

```typescript
// ANTES: Multiplas roundtrips
const denuncia1 = await redis.hgetall('denuncia:1')
const denuncia2 = await redis.hgetall('denuncia:2')
const denuncia3 = await redis.hgetall('denuncia:3')

// DEPOIS: Uma roundtrip
const pipeline = redis.pipeline()
pipeline.hgetall('denuncia:1')
pipeline.hgetall('denuncia:2')
pipeline.hgetall('denuncia:3')
const results = await pipeline.exec()
```

### Cache com TTL

```typescript
// server/utils/cache.ts
export async function getCachedStats(cidade: string) {
  const cacheKey = `cache:stats:${cidade}`
  
  // Tenta cache primeiro
  const cached = await redis.get(cacheKey)
  if (cached) {
    return JSON.parse(cached)
  }
  
  // Calcula e cacheia
  const stats = await calculateStats(cidade)
  await redis.set(cacheKey, JSON.stringify(stats), { ex: 300 }) // 5 min TTL
  
  return stats
}

// Invalidacao
export async function invalidateStatsCache(cidade: string) {
  await redis.del(`cache:stats:${cidade}`)
}
```

### Indices para Queries Eficientes

```typescript
// Criando denuncia com indices
async function createDenuncia(denuncia: Denuncia) {
  const id = generateId()
  const pipeline = redis.pipeline()
  
  // Dados principais
  pipeline.hset(`denuncia:${id}`, denuncia)
  
  // Indices para busca
  pipeline.sadd(`idx:cidade:${denuncia.cidade}`, id)
  pipeline.sadd(`idx:status:${denuncia.status}`, id)
  pipeline.zadd('denuncias_por_data', { score: Date.now(), member: id })
  
  await pipeline.exec()
}

// Query por cidade (O(1) lookup)
async function getDenunciasPorCidade(cidade: string) {
  const ids = await redis.smembers(`idx:cidade:${cidade}`)
  
  if (ids.length === 0) return []
  
  const pipeline = redis.pipeline()
  ids.forEach(id => pipeline.hgetall(`denuncia:${id}`))
  
  return pipeline.exec()
}
```

---

## Performance Targets

### Frame Budget (60fps)
```
Total budget: 16.67ms per frame

+-- JavaScript: ~10ms max
+-- Style/Layout: ~2ms
+-- Paint: ~2ms
+-- Composite: ~2ms
```

### Memory Targets
```
+-- Initial heap: < 50MB
+-- Active heap: < 100MB
+-- Per-component: < 1MB
+-- GC frequency: < 1/second
```

### Bundle Size Targets
```
+-- Initial JS: < 200KB (gzipped)
+-- Lazy chunks: < 50KB each
+-- CSS: < 50KB (gzipped)
+-- Total assets: < 2MB
```

### Redis Targets
```
+-- Single query: < 10ms
+-- Pipeline (10 ops): < 20ms
+-- Cache hit rate: > 80%
+-- Index lookup: O(1)
```

---

## Heavy Dependency Replacements

| Heavy | Light | Savings |
|-------|-------|---------|
| moment.js (300kb) | date-fns (30kb) | ~270kb |
| lodash (70kb) | lodash-es + tree shake | ~60kb |
| axios (15kb) | $fetch (native) | ~15kb |
| uuid (9kb) | crypto.randomUUID() | ~9kb |
| chart.js (200kb) | unovis/vue-chartjs lazy | ~150kb |

---

## Mantras

> "The fastest code is code that doesn't run"

> "Measure twice, cut once"

> "If you can't measure it, you can't improve it"

> "Death by a thousand allocations"

> "Cache is king, GC is the enemy"

> "shallowRef first, ref when needed"
