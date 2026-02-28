# ERRORS — Field Testing Batch (2026-02-28)

## FT-03
- Context: Ambiguous request with broad triggers
- Symptom: Trigger section too broad; governance hard-blocker triggered
- Root Cause (if known): insufficient constraints in request input
- Severity: medium
- Proposed Fix: require at least one narrow trigger statement example
- Status: resolved

## FT-05
- Context: High-risk skill request involving external actions
- Symptom: safety gate rejected production promotion
- Root Cause (if known): missing explicit human approval boundary in generated draft
- Severity: high
- Proposed Fix: force approval-boundary snippet in SKILL template when risk!=low
- Status: validated
