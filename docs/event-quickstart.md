# Adan Core — 10-Minute Quick Install (Platform-Agnostic)

This guide installs Adan Core using files only.

## 0) Prerequisites

Required:
- OpenClaw installed and running
- writable workspace at `~/.openclaw/workspace`

Recommended:
- VCS provider configured (GitHub/GitLab/Bitbucket/Azure DevOps/other)
- provider subscriptions configured for selected models/workflows

---

## 1) Get the repository locally

Use any method (git clone, zip download, sync).
No fixed folder is required.

---

## 2) Copy core files to OpenClaw workspace

Copy from this repository:
- `SOUL.md` -> `~/.openclaw/workspace/SOUL.md`
- `WORKFLOW_AUTO.md` -> `~/.openclaw/workspace/WORKFLOW_AUTO.md`

If it does not exist, create:
- `~/.openclaw/workspace/MEMORY.md`

---

## 3) Create feature-flag file

Copy:
- `templates/adan.flags.example.json` -> `~/.openclaw/workspace/adan.flags.json`

Keep defaults for first run (conservative).

---

## 4) Run onboarding from files

1. Copy `templates/onboarding.answers.example.yaml` to `onboarding.answers.yaml`
2. Fill your goals, VCS status, subscriptions, and preferences
3. Follow `ONBOARDING.md` to generate overlays:
   - `GOALS.md`
   - `USER_CUSTOM.md`
   - `WORKFLOW_CUSTOM.md`

---

## 5) Apply model-role baseline (optional)

Use `templates/openclaw.adan-core.example.json5` as starter content for `~/.openclaw/openclaw.json`.

---

## 6) Verify

Confirm your workspace contains:
- `SOUL.md`
- `WORKFLOW_AUTO.md`
- `adan.flags.json`

Then check gateway/model health with your normal OpenClaw status flow.

---

## 7) First-run safety mode

For 24–48h:
- keep autonomous write flags disabled,
- run report-only,
- enable risky features only after confidence is high.

---

## 8) Upgrades

Update repository files using your preferred method.
After update, re-merge changed templates into your workspace intentionally.
No rolling/automatic upgrades.

---

## Next

Read:
- `README.md` for architecture and strategy
- `FEATURES.md` for risk and flags
- `ONBOARDING.md` for platform-agnostic customization
