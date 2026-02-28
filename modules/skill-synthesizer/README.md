# skill-synthesizer (MVP)

Optional Adan Core module for generating new skill scaffolds safely.

## Safety Model
- Feature-flagged (`meta_skill_synthesizer`)
- Human-reviewed outputs
- No auto-execution / auto-publish

## Inputs
Use `templates/skill-request.example.yaml` as your request contract.

## Output Contract
See `OUTPUT-CONTRACT.md`.

Generate a skill folder with at least:
- `SKILL.md`
- `REVIEW-NOTES.md`
- optional `templates/`, `references/`, `assets/`

## Apply Flow (platform-agnostic)
1. Copy `templates/skill-request.example.yaml` -> `skill-request.yaml`
2. Fill the fields
3. Ask your OpenClaw agent:
   - Read `skill-request.yaml`
   - Create a new skill scaffold using `templates/SKILL.template.md`
   - Produce output in a target folder
4. Validate with `REVIEW-CHECKLIST.md` before adoption
