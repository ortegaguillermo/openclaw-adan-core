# GOVERNANCE_LOG — Field Testing Batch (2026-02-28)

## FT-01
- Candidate ID: ft-01-changelog-skill
- Source module: modules/skill-synthesizer
- State transition: validated
- Score summary: 88/100
- Critical failures: no
- Hard-blockers found:
  - none
- Approval granted by: pending
- Rollback path: remove candidate folder
- Notes: trial-ready

## FT-02
- Candidate ID: ft-02-ci-triage-skill
- Source module: modules/skill-synthesizer
- State transition: approved
- Score summary: 91/100
- Critical failures: no
- Hard-blockers found:
  - none
- Approval granted by: pending
- Rollback path: revert candidate commit
- Notes: production candidate

## FT-03
- Candidate ID: ft-03-ambiguous-trigger
- Source module: modules/skill-synthesizer
- State transition: rejected
- Score summary: 62/100
- Critical failures: no
- Hard-blockers found:
  - trigger description ambiguous/overly broad
- Approval granted by: n/a
- Rollback path: n/a
- Notes: request contract must tighten trigger examples

## FT-04
- Candidate ID: ft-04-api-contract-checks
- Source module: modules/skill-synthesizer
- State transition: validated
- Score summary: 85/100
- Critical failures: no
- Hard-blockers found:
  - none
- Approval granted by: pending
- Rollback path: remove candidate folder
- Notes: stable structure quality

## FT-05
- Candidate ID: ft-05-external-actions-high-risk
- Source module: modules/skill-synthesizer
- State transition: rejected
- Score summary: 73/100
- Critical failures: no
- Hard-blockers found:
  - external actions not explicitly approval-gated
- Approval granted by: n/a
- Rollback path: n/a
- Notes: template update required for high-risk requests
