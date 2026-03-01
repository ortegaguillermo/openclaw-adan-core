# Onboarding (Platform-Agnostic)

Use this process on any OS or environment.

## 1) Fill onboarding answers

Choose one path:

- Generic template:
  - `templates/onboarding.answers.example.yaml` → `onboarding.answers.yaml`
- Provider preset starter:
  - `templates/onboarding.presets/<provider>.yaml` → `onboarding.answers.yaml`

Then edit values for your environment.

This onboarding flow is file-based and platform-agnostic (no required scripts).

## 2) Ask the agent to apply answers

Prompt example:

> Read `onboarding.answers.yaml` and generate/update `GOALS.md`, `USER_CUSTOM.md`, and `WORKFLOW_CUSTOM.md` as additive overlays. Do not replace `SOUL.md` or `WORKFLOW_AUTO.md`.

## 3) Validate prerequisites

Before enabling autonomous features, confirm:

- OpenClaw installed and gateway healthy
- VCS provider access configured (GitHub/GitLab/Bitbucket/etc.)
- A common repository root exists (e.g., `~/Projects`)
- Required subscriptions/providers are configured (if used)
- Available tools are explicitly inventoried in onboarding answers (Gemini, Copilot, OpenCode, `gh`, local runtimes)

## 3.5) Provider bootstrap hints

Optionally copy:

`templates/onboarding.auth-bootstrap.example.yaml` -> `onboarding.auth-bootstrap.yaml`

Use it to record provider state (`available`, `configured`, `validated`) for:
- GitHub Copilot
- Google Antigravity
- OpenAI Codex
- Local runtime
- VCS provider

Reference: `docs/provider-bootstrap-hints.md`

## 4) Recommended repo root policy

Define one shared clone root in `onboarding.answers.yaml`:

- `platform.repo_workspace_root: "~/Projects"`

Store all working repositories under this root for consistent automation.

## 5) Safety defaults

- Keep risky feature flags off initially.
- Start in report-only mode for 24–48 hours.
- Enable autonomous write actions incrementally.
