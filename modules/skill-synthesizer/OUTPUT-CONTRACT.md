# OUTPUT-CONTRACT (MVP)

Generated skills must follow this output contract.

## Required Output

1. Target folder created at `output.target_folder`
2. `SKILL.md` created in target folder
3. Optional directories created based on request flags:
   - `templates/`
   - `references/`
   - `assets/`
4. `REVIEW-NOTES.md` generated with:
   - assumptions,
   - unresolved questions,
   - risk summary,
   - recommended next steps.

## Required `SKILL.md` Rules

- Frontmatter contains only:
  - `name`
  - `description`
- `name` must be lowercase + hyphenated
- `description` must include explicit trigger conditions

## Required Reporting Block

Every generation response must include:

- Target path
- Files created
- Validation result (pass/fail)
- Risk level
- Human review required: yes/no (must be yes for MVP)

## Forbidden Behavior (MVP)

- Auto-executing generated scripts
- Auto-publishing generated skill packages
- Auto-merging generated content to main branches
- Any networked external action without explicit user request
