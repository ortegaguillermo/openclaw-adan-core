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

### Phase 4: Meta-Agent Evolution (in progress)
- [x] `skill-synthesizer` MVP scaffold
- [x] Weekly refinement loop templates and governance checks
- [x] Promotion workflow (`pending -> validated -> promoted`)
- [ ] Field testing and refinement of module outputs

## Open Decisions
1. How much meta-skill automation to allow in v1 without increasing risk?
2. Which quality gates are mandatory before promoting new behaviors?
3. Which provider profiles should ship as first-class templates?

## Immediate Next Steps
- Implement `skill-synthesizer` MVP as optional feature-flagged module.
- Add governance checklist for weekly refinement promotions.
- Add provider-specific onboarding presets.
