---
name: research-protocols
description: "Scientific research methodology for codebase investigation and knowledge discovery. Covers hypothesis formation, evidence gathering, systematic search strategies, and conclusion documentation with confidence levels. Use when: (1) Investigating unknown codebase areas or unfamiliar code structures, (2) Researching technology options and evaluating dependencies, (3) Analyzing patterns, anti-patterns, and architectural decisions, (4) Documenting findings systematically with evidence trails, (5) Tracing git history to understand code evolution, (6) Mapping API endpoints and request flows."
license: MIT
---

# Skill: Research Investigation Protocols (SOP-RSH)

## Overview
This skill provides structured, scientific protocols for investigating software systems. It ensures investigations are systematic, reproducible, and evidence-based.

## Core Approach

Apply the **scientific method** to software investigation:
1. Observe → Gather evidence
2. Hypothesize → Form theories
3. Test → Validate theories
4. Conclude → Document findings

## SOP-RSH-002: Codebase Investigation Protocol (CIP)

```
1. SCOPE DEFINITION
   - Define search boundaries
   - Identify relevant directories
   - List file patterns to search

2. SYSTEMATIC SEARCH
   - Use glob for file discovery
   - Use grep for content patterns
   - Use git log for history

3. PATTERN RECOGNITION
   - Identify naming conventions
   - Map module relationships
   - Document dependencies

4. DOCUMENTATION
   - Create findings report
   - Rate confidence levels
   - List remaining unknowns
```

## SOP-RSH-003: Git History Investigation Protocol (GHIP)

```
1. Identify time range of interest
2. Find relevant commits with git log
3. Analyze commit patterns
4. Identify key contributors
5. Map evolution of components
```

## SOP-RSH-004: API Investigation Protocol (AIP)

```
1. Find API entry points
2. Trace request flow
3. Map endpoints to handlers
4. Document authentication
5. List response schemas
```

## SOP-RSH-005: Dependency Investigation Protocol (DIP)

```
1. Parse package.json / requirements.txt
2. Categorize dependencies (runtime, dev, peer)
3. Check for vulnerabilities
4. Identify outdated packages
5. Map internal dependencies
```

## Confidence Levels (MET-CON-001)

Always rate findings:

| Level | Symbol | Criteria |
|-------|--------|----------|
| HIGH | GREEN | Multiple independent sources, runtime verified |
| MEDIUM | YELLOW | Code analysis supports, not runtime tested |
| LOW | ORANGE | Single source, logical inference |
| HYPOTHESIS | RED | Theory requiring further investigation |

## Search Strategies

### Finding Entry Points
```bash
# Find main/index files
glob: "**/main.{ts,tsx,js}"
glob: "**/index.{ts,tsx,js}"

# Find route definitions
grep: "router\.|app\.(get|post|put|delete)"

# Find exports
grep: "export (default|const|function|class)"
```

### Finding Dependencies
```bash
# Import analysis
grep: "from ['\"]"
grep: "require\(['\"]"

# Package usage
grep: "package-name"
```

### Finding Tests
```bash
glob: "**/*.test.{ts,tsx,js}"
glob: "**/*.spec.{ts,tsx,js}"
grep: "(describe|it|test)\("
```

## Report Format

```markdown
# Research Report: [Topic]

## Executive Summary
[1-2 sentence summary]

## Methodology
[What investigation methods were used]

## Findings

### Finding 1: [Title]
**Confidence**: HIGH

[Description with evidence]

**Evidence**:
- `path/to/file.ts:123` - [What it shows]
- git commit abc123 - [What it shows]

### Finding 2: [Title]
**Confidence**: MEDIUM
...

## Unknowns and Gaps
- [What couldn't be determined]
- [What needs more investigation]

## Recommendations
- [Actionable next steps]
```

## Evolution

### Version History
- **1.0.0** (2025-01-07): Initial research protocols for codebase investigation
- **1.1.0** (2026-01-07): Adapted to Anthropic skill standard with YAML frontmatter

### Planned Improvements
- Add security investigation protocols (vulnerability scanning patterns)
- Include performance investigation methodologies
- Define cross-repository research patterns for monorepos
- Add automated evidence collection templates
