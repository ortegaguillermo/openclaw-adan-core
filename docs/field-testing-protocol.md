# Field Testing Protocol (Phase 4)

Use this protocol to refine module outputs before production adoption.

## Goal
Validate that generated skills are safe, consistent, and useful across real requests.

## Test Set
Run at least 5 synthesis requests:
- 2 low-risk utility skills
- 2 medium complexity workflow skills
- 1 edge-case request with ambiguous triggers

## Procedure

1. Fill a request using `templates/skill-request.example.yaml` (or `modules/skill-synthesizer/templates/skill-request.example.yaml`)
2. Review trigger design against `templates/TRIGGER-GUIDELINES.md`
3. Generate output scaffold
4. Review with:
   - `modules/skill-synthesizer/REVIEW-CHECKLIST.md`
   - `docs/skill-quality-rubric.md`
5. Record outcome in `.learnings/LEARNINGS.md` or `.learnings/ERRORS.md`
6. Capture promotion candidate or rejection reason

## Pass Criteria

- No critical safety failures
- >= 75 score for at least 4/5 cases
- >= 85 score for at least 2/5 cases
- Clear, reproducible output structure across all cases

## Promotion Rule

Do not promote behavior changes to core files without explicit human approval.

## Reporting Template

- Date:
- Request ID:
- Score:
- Critical failures: yes/no
- Decision: reject/trial/production
- Notes:
