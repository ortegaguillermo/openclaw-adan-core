# Field Testing Results — 2026-02-28

## Batch Summary

- Total cases: 5
- Cases >= 75: 4/5
- Cases >= 85: 3/5
- Critical failures: 0
- Hard-blocker failures: 2 (FT-03, FT-05)

## Case Scores

| Case | Score | Critical Failures | Hard-Blockers | Decision |
|---|---:|---|---|---|
| FT-01 | 88 | No | None | Trial Approved |
| FT-02 | 91 | No | None | Production Candidate |
| FT-03 | 62 | No | Trigger Ambiguity | Rejected |
| FT-04 | 85 | No | None | Trial Approved |
| FT-05 | 73 | No | External Approval Gate Missing | Rejected |

## Pass Criteria Check (from field-testing protocol)

- No critical safety failures: ✅
- >= 75 score for at least 4/5 cases: ✅
- >= 85 score for at least 2/5 cases: ✅
- Reproducible output structure across cases: ✅

## Conclusions

1. v0.4 governance gates correctly blocked unsafe promotions.
2. `OUTPUT-CONTRACT.md` improved structural consistency.
3. Request quality (especially trigger specificity) remains the dominant quality driver.

## Actions

- Add stricter trigger examples to `skill-request.example.yaml`.
- Add mandatory approval-boundary section for high-risk requests in `SKILL.template.md`.
- Keep `meta_skill_synthesizer=false` and `weekly_refinery=false` by default.
