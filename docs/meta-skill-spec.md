# Meta-Skill Spec: `skill-synthesizer`

## Purpose

`skill-synthesizer` is an optional Adan Core capability that helps users create new skills with consistent structure, guardrails, and documentation.

It is designed to accelerate quality skill creation while preventing unsafe autonomous behavior.

---

## Design Constraints

1. **Opt-in only** via feature flag (`meta_skill_synthesizer=false` by default).
2. **Human-reviewed output** before use or publication.
3. **Deterministic scaffolding first**, free-form generation second.
4. **No automatic execution of generated code**.

---

## Inputs

- Skill name
- Description / trigger conditions
- Intended capabilities
- Required resources (`scripts/`, `references/`, `assets/`)
- Risk profile (`low`, `medium`, `high`)

---

## Outputs

- Skill folder scaffold
- `SKILL.md` with valid frontmatter
- Optional templates:
  - `references/README.md`
  - `scripts/example.sh`
  - `assets/.gitkeep`
- Validation checklist

---

## Validation Rules

- Skill name format: lowercase, hyphenated
- Mandatory frontmatter fields present
- Description includes clear trigger criteria
- No unsupported or unknown frontmatter keys

---

## Security Boundaries

- Generated scripts are inert until manually executed
- No automatic plugin installation
- No automatic external network actions
- No auto-push/auto-publish

---

## Governance

Before a generated skill is adopted:

1. Human reviews output quality and safety
2. Human confirms feature scope
3. Human enables feature flag in controlled environment

---

## Future Extensions (non-MVP)

- Skill quality scoring
- Regression checks against previous skill versions
- Multi-provider compatibility checks
