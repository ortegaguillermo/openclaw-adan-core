# Onboarding Checklist

## Prerequisites
- [ ] OpenClaw installed
- [ ] Gateway running (`openclaw status`)
- [ ] Git installed
- [ ] Common repository root chosen (e.g., `~/Projects`)

## Baseline Skills (recommended first)
- [ ] `tmux` installed/configured (purpose: persistent worker/session supervision)
  - Validation: `tmux ls` (or `tmux new -d -s adan-check`)
- [ ] `github` skill path ready with GitHub CLI access (purpose: deterministic issue/PR/CI operations)
  - Validation: `gh auth status`
- [ ] `session-logs` available (purpose: post-run auditing and retrospective debugging)
  - Validation: run one sample `session-logs` query and verify records are returned

## VCS
- [ ] VCS provider selected (GitHub/GitLab/Bitbucket/Azure DevOps/Other)
- [ ] VCS credentials configured
- [ ] Repositories identified and reachable

## Subscriptions / Providers (as applicable)
- [ ] GitHub Copilot configured
- [ ] Google Antigravity configured
- [ ] OpenAI Codex configured
- [ ] Local model runtime configured (Ollama/LM Studio/vLLM)
- [ ] `onboarding.auth-bootstrap.yaml` recorded (available/configured/validated)

## Tool Availability Inventory
- [ ] Gemini CLI available
- [ ] Copilot available
- [ ] OpenCode available
- [ ] GitHub CLI (`gh`) available
- [ ] Other required tools recorded in onboarding answers

## Adan Core Configuration
- [ ] `SOUL.md` installed
- [ ] `WORKFLOW_AUTO.md` installed
- [ ] `adan.flags.json` created
- [ ] `onboarding.answers.yaml` completed
- [ ] Overlay files generated (`GOALS.md`, `USER_CUSTOM.md`, `WORKFLOW_CUSTOM.md`)

## Rollout
- [ ] Report-only mode enabled
- [ ] Autonomous write actions still disabled
- [ ] First review scheduled after 24–48h
