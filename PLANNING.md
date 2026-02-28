# Planning: Adan Core

## Goal
Package the Adan persona and autonomous engineering framework into a portable, file-first OpenClaw skill.

## Phases

### Phase 1: Core Extraction (completed)
- [x] Universal `SOUL.md`
- [x] Universal `WORKFLOW_AUTO.md`
- [x] Public repository initialized

### Phase 2: Platform-Agnostic Onboarding (completed)
- [x] File-based onboarding flow (`ONBOARDING.md`)
- [x] Structured answers template (`onboarding.answers.example.yaml`)
- [x] Additive overlays (`GOALS.md`, `USER_CUSTOM.md`, `WORKFLOW_CUSTOM.md`)

### Phase 3: Safety & Release Model (completed)
- [x] Feature-flag policy with conservative defaults
- [x] Manual/non-rolling upgrade policy
- [x] Prerequisite matrix (VCS + subscriptions)

### Phase 4: Meta-Agent Evolution (completed for v0.3 scope)
- [x] `skill-synthesizer` MVP scaffold
- [x] Weekly refinement loop templates and governance checks
- [x] Promotion workflow (`pending -> validated -> promoted`)
- [x] Field testing protocol and output refinement contract
- [x] Provider-specific onboarding presets

## Open Decisions
1. How much meta-skill automation to allow in v1.1 without increasing risk?
2. Which gates should be hard blockers for production promotion?
3. Should provider presets expand to include auth bootstrap hints?

## Immediate Next Steps
- Execute first 5-case field-testing batch using `docs/field-testing-protocol.md`.
- Capture learnings in `.learnings/*` and tune templates.
- Apply v0.4 governance hard-blockers and promotion templates.
