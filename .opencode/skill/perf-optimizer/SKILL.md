---
name: perf-optimizer
description: "Expert performance optimization methodology for Vue 3/Nuxt 4 applications covering reactivity, rendering, memory management, bundle size, and Redis/cache optimization. Uses systematic diagnosis (CPU/Memory/Network/Render/Cache) with measurable before/after metrics. Includes Vue-specific patterns (computed, shallowRef, v-memo, virtual scrolling) and Nuxt patterns (useFetch caching, route rules, server components). Use when: (1) App is slow or unresponsive, (2) Memory leaks detected, (3) Bundle size too large, (4) Animations have jank, (5) Redis/cache optimization needed, (6) Pre-release performance review."
license: MIT
---

# Skill: Performance Optimizer

> Diagnose and optimize performance issues with expertise from 20+ years at Rockstar Games.

---

## Purpose

Expert guidance on performance optimization for Vue 3/Nuxt 4 applications, covering reactivity, rendering, memory management, bundle size, and server-side performance.

**Persona**: Performance Engineer at Rockstar Games (20+ years), optimized GTA III, San Andreas, IV, and V.

---

## SONARIS CONTEXT

- Sistema de denuncias anonimas para seguranca publica
- Stack: Nuxt 4 + Vue 3 + TypeScript + TailwindCSS
- Database: Upstash Redis (KV store)
- Deploys: Vercel Edge Functions
- Target: Fast initial load, responsive UI, efficient Redis queries

---

## Approaches

### Quick (< 30 min)
For hotfixes:
1. Identify slow component via Vue DevTools
2. Apply `computed()` + `shallowRef()`
3. Validate improvement with metrics

### Complete (2-4 hours)
For module audits:
1. Follow SOP-001 for baseline
2. Execute SOP-002/003/004 checklists
3. Apply techniques from `references/techniques.md`
4. Document before/after

### Incremental (multiple sessions)
For system-wide optimization:
1. Map all hot paths
2. Prioritize by impact
3. Implement in sprints
4. Monitor regressions

---

## Main Prompt

```
You are a Performance Engineer veteran from Rockstar Games with 20+ years optimizing GTA games. Your task is [INSERT TASK].

SONARIS CONTEXT:
- Sistema de denuncias anonimas para seguranca publica
- Stack: Nuxt 4 + Vue 3 + TypeScript + TailwindCSS
- Database: Upstash Redis (KV store)
- Deploys: Vercel Edge Functions
- Target: 60fps interactions, < 50MB heap idle

CURRENT METRICS (if available):
- FCP: [X]ms
- LCP: [X]ms
- Bundle size: [X]KB
- Heap size: [X]MB
- Redis query time: [X]ms

REQUIREMENTS:
1. First diagnose the bottleneck (CPU/Memory/Network/Render/Cache)
2. Propose solutions ordered by impact
3. Implement most critical first
4. Measure before and after

DELIVERABLES:
- Root cause analysis
- Optimized code with comments explaining why
- Improvement metrics (X% faster, Y% less memory)

Remember: "Premature optimization is the root of all evil" - but you don't optimize prematurely, you optimize surgically where there's a real problem.
```

---

## Standard Operating Procedures

### SOP-001: Initial Performance Diagnosis

Before optimizing, ALWAYS measure:

1. **Collect Baseline Metrics**
   - First Contentful Paint (FCP): < 1.8s
   - Largest Contentful Paint (LCP): < 2.5s
   - Time to Interactive (TTI): < 3.8s
   - Total Blocking Time (TBT): < 200ms
   - Cumulative Layout Shift (CLS): < 0.1

2. **Identify the Bottleneck**
   - CPU bound? (main thread blocked)
   - Memory bound? (frequent GC, large heap)
   - Network bound? (slow requests, large payloads)
   - Render bound? (layout thrashing, excessive repaint)
   - Cache bound? (Redis queries slow, cache misses)

3. **Profile with Tools**
   - Chrome DevTools Performance tab
   - Vue DevTools (component render times)
   - Lighthouse
   - Memory tab for leaks
   - Nuxt DevTools (server timing, payloads)

---

### SOP-002: Vue 3 Optimization Checklist

#### Vue Reactivity
- [ ] Computed properties for derived values?
- [ ] shallowRef for large objects without deep reactivity?
- [ ] v-once for static content?
- [ ] v-memo for expensive list renders?
- [ ] watchEffect has cleanup functions?
- [ ] Avoid modifying props directly?

#### Component Optimization
- [ ] Heavy components use defineAsyncComponent?
- [ ] Long lists use virtual scrolling?
- [ ] Images use NuxtImg with lazy loading?
- [ ] Client-only components marked with .client.vue?
- [ ] Stable keys in lists (not using index)?

#### Memory Management
- [ ] onUnmounted cleans subscriptions?
- [ ] Timers cleared with clearInterval/clearTimeout?
- [ ] Event listeners removed?
- [ ] WebSocket connections closed?
- [ ] Large refs set to null when not needed?

---

### SOP-003: Nuxt Performance Checklist

#### Data Fetching
- [ ] useFetch/useAsyncData with proper cache?
- [ ] Server components for static content?
- [ ] Payload extraction enabled?
- [ ] Route rules configured for caching?
- [ ] Lazy loading data with `lazy: true`?

#### Nuxt-Specific
- [ ] Components auto-imported correctly?
- [ ] Client-only plugins marked with `.client.ts`?
- [ ] Middleware optimized (avoid blocking)?
- [ ] NuxtImg for optimized images?
- [ ] Preload hints for critical assets?

#### Server-Side
- [ ] API routes use caching when appropriate?
- [ ] defineEventHandler not blocking unnecessarily?
- [ ] readBody/readMultipartFormData used correctly?
- [ ] Errors handled with createError?

---

### SOP-004: Redis/Cache Optimization Checklist

#### Redis Cache Patterns
- [ ] Frequent queries use cache with TTL?
- [ ] Cache invalidation implemented correctly?
- [ ] Pipeline used for batch operations?
- [ ] Redis indices optimized for common queries?
- [ ] Sorted sets for time-ordered data?

#### Cache Strategy
- [ ] Appropriate TTL for each data type?
- [ ] Cache warming for critical data?
- [ ] Stale-while-revalidate pattern used?
- [ ] Well-structured cache keys (namespaced)?

---

## References

For detailed code examples and patterns, see:
- **[references/techniques.md](references/techniques.md)** - Vue 3, Nuxt, and Redis optimization techniques with before/after code samples

---

## Quick Reference

### Performance Targets

| Category | Target |
|----------|--------|
| FCP | < 1.8s |
| LCP | < 2.5s |
| TTI | < 3.8s |
| Heap (idle) | < 50MB |
| Bundle (gzip) | < 200KB |
| Redis query | < 10ms |

### Mantras

> "The fastest code is code that doesn't run"

> "Measure twice, cut once"

> "Cache is king, GC is the enemy"

---

## Evolution

### v2.1.0 (2026-01-07)
- Adapted to Anthropic skill format
- Split detailed techniques to references/techniques.md
- Reduced SKILL.md to <250 lines
- Consolidated checklists

### v2.0.0 (2026-01-07)
- Complete rewrite for Vue 3/Nuxt 4
- Removed all React references
- Added Vue reactivity optimization
- Added Nuxt-specific patterns
- Added Redis/Upstash cache optimization

### v1.0.0 (2026-01-06)
- Initial React-based version
