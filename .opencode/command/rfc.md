---
description: Create RFC for architectural decision
model: google/antigravity-claude-opus-4-5-thinking-high
subtask: true
---

<!-- @META: Command /rfc -->
<!--
    File: .opencode/command/rfc.md
    Scope: Slash command to trigger RFC creation
-->

<!-- @REF(.opencode/skill/rfc-creation/SKILL.md): Skill -->
Create an RFC document for the proposed change.

Use the `rfc-creation` skill for the template and process.

Requirements:
1. Clear problem statement
2. At least 2 alternatives
3. Explicit trade-offs
4. Implementation plan
5. Success metrics

Save to `docs/rfcs/` with format `RFC-XXX-title.md`
