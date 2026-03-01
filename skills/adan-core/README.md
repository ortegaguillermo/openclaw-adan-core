# Drop-in Skill Bundle: adan-core

This folder is a workspace-ready bundle for OpenClaw users who expect a `skills/` layout.

## Install

1. Copy this folder into your OpenClaw skills directory:
   - `~/.openclaw/workspace/skills/adan-core`

2. In your workspace root, ensure these files exist (copy from this bundle if missing):
   - `SOUL.md`
   - `WORKFLOW_AUTO.md`
   - `MEMORY.md`

3. Initialize feature flags:
   - copy `templates/adan.flags.example.json` to `~/.openclaw/workspace/adan.flags.json`

4. Optional: initialize ops worker instances:
   - copy `templates/ops-workers/*.yaml` to `~/.openclaw/workspace/ops-workers/`

5. Follow onboarding:
   - `ONBOARDING.md`

## Notes

- This bundle mirrors root project files for compatibility with users who install from `skills/`.
- Includes optional modules:
  - `modules/skill-synthesizer/*`
  - `modules/ops-worker/*` (multi-instance ops workers)
- Source-of-truth development remains at repository root.
