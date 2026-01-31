# Documentation Compliance Framework

<!-- @META: Master framework for documentation standards and compliance -->
<!--
    File: .opencode/specs/doctrine/DOCUMENTATION_STANDARDS.md
    Version: 2.0.0
    Created: 2026-01-07
    Updated: 2026-01-07
    Scope: Monorepo Documentation Governance
-->

## 1. Documentation Standards

<!-- @RULE: What must be documented -->
All code and systems in the monorepo must adhere to the following documentation coverage standards.

### 1.1 Core Components
- **Architecture**: High-level design, data flow, and dependency graphs.
- **APIs**: Public interfaces, types, and contract definitions.
- **Configuration**: Environment variables, feature flags, and settings.
- **Operation**: Build, deploy, and troubleshooting guides.

### 1.2 Code Level
- **Exported Members**: All exported classes, functions, and interfaces MUST have TSDoc/JSDoc.
- **Complex Logic**: Algorithms with cyclomatic complexity > 5 MUST have inline comments explaining the *why*.
- **Hacks/Workarounds**: Must be tagged with `@TECH-DEBT` or `@HACK` and link to an issue/task.

## 2. AGENTS.md Requirements

<!-- @RULE: AGENTS.md Constitution -->
Every directory containing business logic or architectural significance MUST have an `AGENTS.md`.

### 2.1 Mandatory Sections
1.  **Header**: Meta tags (File, Version, Scope).
2.  **Identity/Purpose**: What this module does.
3.  **Rules (@RULE)**: Specific constraints for agents working here.
4.  **Patterns (@NOTE)**: Architectural patterns in use.
5.  **Project Relationships**: Dependencies and consumers.

### 2.2 Creation Triggers
Create an `AGENTS.md` when:
- A new directory/module is created.
- A directory reaches > 5 source files.
- A directory contains complex logic requiring specific agent instructions.

## 3. README Standards

<!-- @RULE: README Structure -->
Every project root (e.g., `frontend`, `backend`) MUST have a `README.md`.

### 3.1 Template
```markdown
# [Project Name]

> [Short Description]

## 🚀 Getting Started
[Installation and Run commands]

## 🏗️ Architecture
[Brief overview or link to AGENTS.md]

## 🤝 Contributing
[Link to contribution guide]

## 📜 License
[License type]
```

## 4. API Documentation

### 4.1 REST/HTTP APIs
- MUST provide an OpenAPI 3.0+ specification.
- `swagger.json` or `openapi.yaml` must be co-located or generated.

### 4.2 TypeScript/JS APIs
- **TSDoc**: strictly enforced.
- **Examples**: Public methods should have `@example` tags.
- **Throws**: Explicitly list all known error conditions.

## 5. Changelog Policy

<!-- @RULE: Keep a Changelog -->
All releasable artifacts MUST maintain a `CHANGELOG.md` following [Keep a Changelog](https://keepachangelog.com/).

### 5.1 Format
```markdown
## [Version] - YYYY-MM-DD
### Added
- Feature X
### Changed
- Behavior Y
### Fixed
- Bug Z
```

## 6. Versioning Standards

<!-- @RULE: Semantic Versioning -->
We follow [SemVer 2.0.0](https://semver.org/).

- **Major (X.y.z)**: Breaking changes to public API or AGENTS.md rules.
- **Minor (x.Y.z)**: New features, backward compatible.
- **Patch (x.y.Z)**: Bug fixes, docs updates (unless policy change).

## 7. Documentation Review

<!-- @RULE: Review Process -->
Documentation is treated as code.

- **PR Requirement**: No PR is merged without corresponding doc updates.
- **Reviewer**: Technical Writer or Senior Engineer.
- **Automated Checks**: Spellcheck (cspell) and broken link check.

## 8. Freshness Policy

<!-- @RULE: Stale Documentation -->
Documentation is considered **STALE** if:
- `AGENTS.md` Version date is > 90 days old.
- Referenced files no longer exist.
- API definitions mismatch code signatures.

**Action**: Agents encountering stale docs must tag them with `<!-- @STALE: Reason -->` and create a task to update.

## 9. Translation Strategy

- **Primary Language**: English (US).
- **Localization**: Not currently supported. All docs must be in English.
- **Spelling**: US English spelling conventions (Color vs Colour).

## 10. Documentation Metrics

We track the following metrics to ensure compliance:

| Metric | Target | Definition |
|--------|--------|------------|
| **Coverage** | > 80% | % of exported symbols with TSDoc |
| **Freshness** | < 90 days | Average age of AGENTS.md update |
| **Compliance** | 100% | % of modules with valid AGENTS.md |

## 11. Compliance Audit Matrix

<!-- @META: Current Status of Monorepo Documentation -->
<!-- Audit Date: 2026-01-07 -->

### Root & Infrastructure
| Path | Status | Last Review | Score | Missing | Action Items |
|------|--------|-------------|-------|---------|--------------|
| `AGENTS.md` | **COMPLIANT** | 2026-01-07 | 100% | None | Maintain as master |
| `backend/AGENTS.md` | **PARTIAL** | - | 70% | API Docs, Changelog | Add OpenAPI spec link |

### Frontend
| Path | Status | Last Review | Score | Missing | Action Items |
|------|--------|-------------|-------|---------|--------------|
| `frontend/AGENTS.md` | **PARTIAL** | - | 60% | Component docs | Add diagrams |

### Agents & Resources
| Path | Status | Last Review | Score | Missing | Action Items |
|------|--------|-------------|-------|---------|--------------|
| `.opencode/agent/*.md` | **COMPLIANT** | 2026-01-07 | 98% | None | Keep updated |

### Vendors & Frameworks (External)
| Path | Status | Notes |
|------|--------|-------|
| `vendors/**` | **EXTERNAL** | Third-party framework docs |

### Summary Statistics
- **Total Tracked Files**: TBD (project-specific)
- **Compliant**: TBD
- **Partial**: TBD
- **Non-Compliant**: TBD

### Global Action Plan
1.  **Phase 1 (Immediate)**: Audit all core modules for AGENTS.md presence.
2.  **Phase 2 (This Sprint)**: Standardize all `Partial` files with new headers.
3.  **Phase 3 (Next Sprint)**: Add Mermaid diagrams for architecture.
