# Monorepo Governance & Structure

<!-- @META: Monorepo Governance -->
<!--
    File: .opencode/specs/architecture/MONOREPO_GOVERNANCE.md
    Version: 2.0.0
    Created: 2026-01-07
    Updated: 2026-01-07
    Scope: Governance, Architecture, and Policy for monorepo projects
    Status: Living Document
-->

---

## 1. Monorepo Philosophy

<!-- @NOTE(phil-001): Core Principles -->

This document provides a **Unified Monorepo Strategy** template designed to maximize code sharing while maintaining strict modular boundaries.

### Benefits
1.  **Atomic Changes**: A single PR can update a core library and all its consumers simultaneously.
2.  **Shared Intelligence**: AI Agents (`.opencode/`) have visibility over the entire stack, enabling cross-domain reasoning.
3.  **Unified Tooling**: A single lockfile and build pipeline ensures deterministic builds.

### Trade-offs
We accept **higher initial complexity** (workspace management, build pipelines) in exchange for **long-term velocity** and **consistency**.

---

## 2. Governance & Ownership

<!-- @NOTE(own-001): Ownership Matrix -->
<!-- @RULE: CODEOWNERS compliance is mandatory for critical paths -->

Ownership should be distributed by **Domain** rather than by language.

| Domain | Scope (Glob Pattern) | Primary Owner | Criticality |
|--------|----------------------|---------------|-------------|
| **Platform** | `/.opencode/**/*`, `/infrastructure/**/*` | Platform Ops | Critical |
| **Frontend** | `/frontend/**/*` | UI/UX Core | High |
| **Backend** | `/backend/**/*` | Cloud Svcs | High |
| **AI/Neural** | `/ai/**/*` | AI Research | Medium |
| **Shared** | `/**/packages/types/**/*` | API Design | Critical |
| **Documentation** | `/**/AGENTS.md`, `/docs/**/*` | DevRel | Low |

### Ownership Responsibilities
- **Architects**: Define interfaces, approve schema changes, and manage `AGENTS.md`.
- **Maintainers**: Fix bugs, upgrade dependencies, and ensure test coverage.
- **Contributors**: Submit PRs adhering to the governance model.

---

## 3. Dependency Management

<!-- @RULE: strict-pnpm -->

We recommend **pnpm workspaces** to manage dependencies.

### Policy 3.1: Internal Dependencies
Internal packages MUST be referenced using the `workspace:*` protocol.

**Correct:**
```json
{
  "dependencies": {
    "@project/types": "workspace:*"
  }
}
```

**Incorrect:**
```json
{
  "dependencies": {
    "@project/types": "1.0.0"
  }
}
```

### Policy 3.2: External Dependencies
- **Peer Dependencies**: Shared libraries (`react`, `lodash`) should be peer dependencies in library packages.
- **Version Pinning**: All production dependencies must use exact versions or carets (`^`) with strict lockfile checks.
- **Vendoring**: Third-party code that requires heavy modification MUST be placed in `vendors/`.

---

## 4. Build System & CI Pipelines

<!-- @NOTE(build-001): Toolchain -->

### 4.1 Global Orchestration (Turborepo)
- **Caching**: Inputs/Outputs are hashed. If code hasn't changed, the task is replayed from cache.
- **Pipeline**:
    1.  `build` depends on `^build` (topological sort).
    2.  `test` depends on `build`.
    3.  `lint` runs in parallel.

### 4.2 Frontend Optimization (Vite)
- **SWC/Esbuild**: Used for transpilation.
- **Chunking**: Manual chunk splitting for optimal loading.

---

## 5. Package Lifecycle & Standards

<!-- @NOTE(lifecycle-001): CRUD for Packages -->

### 5.1 Creating a New Package
1.  **Identify Scope**: Is it Backend, Frontend, or Universal?
2.  **Scaffold**: Create directory structure (`src/`, `tsconfig.json`, `package.json`).
3.  **Document**: Create `AGENTS.md` (Mandatory).
4.  **Register**: Add to `pnpm-workspace.yaml` if not covered by wildcards.

### 5.2 Deprecation Process
1.  Add `<!-- @DEPRECATED: [Reason] -->` to the package's `AGENTS.md`.
2.  Add `console.warn` in the entry point.
3.  Move to `deprecated/` folder (optional).

### 5.3 Naming Conventions
- **Apps**: `kebab-case` (e.g., `billing-gateway`).
- **Packages**: Scoped `@project/[name]`.
- **Classes**: `PascalCase`.
- **Files**: `camelCase.ts` (logic) or `PascalCase.tsx` (components).

---

## 6. Performance Budgets

<!-- @RULE: perf-limits -->

| Metric | Target | Warning | Critical | Action |
|--------|--------|---------|----------|--------|
| **Initial Bundle (JS)** | < 150KB | > 200KB | > 300KB | Split chunks, lazy load |
| **First Contentful Paint** | < 1.0s | > 1.5s | > 2.5s | Optimize assets |
| **Backend Cold Start** | < 500ms | > 1s | > 3s | Optimize imports |
| **Docker Image Size** | < 200MB | > 500MB | > 1GB | Use Alpine/Distroless |

---

## 7. Inter-Package Communication

<!-- @NOTE(ipc-001): Contracts -->

### 7.1 Shared Types
All DTOs (Data Transfer Objects) MUST be defined in a shared types package.
- **No duplication**: Frontend and Backend must import the same interface.
- **Zod Validation**: Use `zod` schemas co-located with types for runtime validation.

### 7.2 API Contracts
- **REST**: OpenAPI (Swagger) generated from Zod schemas.
- **WebSocket**: Event types defined in shared types package.

---

## 8. Testing Strategy & Quality Assurance

<!-- @NOTE(qa-001): Testing Pyramid -->

### 8.1 The Testing Pyramid

| Type | Scope | Tool | Target Cov |
|------|-------|------|------------|
| **Unit** | Individual functions/components | `vitest` | 80% |
| **Integration** | Service interaction, database | `vitest` + `testcontainers` | 60% |
| **E2E** | Full user flows | `playwright` | Critical Flows |

### 8.2 Testing Rules
1.  **Co-location**: Unit tests MUST sit next to the file they test.
2.  **Mocking Boundaries**:
    - **Frontend**: Mock all network requests (MSW).
    - **Backend**: Mock external APIs, but use real DB in Docker for integration tests.
3.  **Snapshot Testing**: Use sparingly. Prefer assertion on logic over markup.

---

## 9. Contribution & Review Guidelines

<!-- @NOTE(contrib-001): Human-AI Collaboration -->

### 9.1 Pull Request Standards
All PRs must include:
- [ ] Reference to the Task ID.
- [ ] `AGENTS.md` updates if architecture changed.
- [ ] Test coverage report.
- [ ] Before/After screenshots (for UI).

### 9.2 Code Review Checklist
Reviewers (Human or AI) check for:
1.  **Security**: Are inputs sanitized? Are permissions checked?
2.  **Performance**: Any new N+1 queries? Large bundles?
3.  **Maintainability**: Is code readable? Are names descriptive?
4.  **Governance**: Does it respect the package boundaries?

### 9.3 Branching Strategy
- `main`: Production-ready. Protected.
- `dev`: Integration branch.
- `feat/name`: Feature branches.
- `fix/name`: Bug fixes.

---

## 10. Security Policy

<!-- @RULE: sec-001 -->

1.  **Secret Zero**: No `.env` files in git.
2.  **Audit**: Run `pnpm audit` weekly.
3.  **Dependencies**: No abandoned packages (< 1 year updates).
4.  **Sanitization**: All inputs must be sanitized via `zod`.

---

## 11. Version History

| Version | Date | Changes |
|---------|------|---------|
| 2.0.0 | 2026-01-07 | Generalized for template use |
| 1.0.0 | 2026-01-07 | Initial document |
