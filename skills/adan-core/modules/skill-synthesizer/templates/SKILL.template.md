---
name: {{skill.name}}
description: {{skill.description}}. Use when: {{triggers[0]}}
---

# {{skill.name}}

## Core Capabilities
- {{capabilities[0]}}
- {{capabilities[1]}}

## Operating Rules
- Keep behavior deterministic when possible.
- Use additive files; avoid replacing core safety constraints.
- Require human review before enabling in production.

## Approval Boundary (Required)
- Any external action requires explicit human approval.
- No auto-publish, auto-merge, or implicit external execution.

## Included Resources
- `SKILL.md`
- `templates/` (optional)
- `references/` (optional)
- `assets/` (optional)
