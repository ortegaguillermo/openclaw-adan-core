# Contributing

Adan Core uses a strict linked-branch workflow.

## Repository Scope

This repository defines **Adan Core itself**—the persona, protocols, and governance for autonomous behavior in OpenClaw.

**Do not confuse:**
- **Repository files** (SOUL.md, WORKFLOW_AUTO.md, etc.) — distributed to users as templates
- **Authorship files** (AGENTS.md) — guidance for maintainers and contributors (repo-only, not distributed)
- **Workspace files** (MEMORY.md, USER.md, adan.flags.json) — created and maintained by end users (never in this repo)

See [AGENTS.md](./AGENTS.md) for authorship governance and build validation rules.

## Branching Policy (required)

- Never push feature work directly to `main`.
- Always create a branch from `main`:
  - `feature/<topic>` for features
  - `fix/<topic>` for fixes
  - `docs/<topic>` for docs
  - `chore/<topic>` for maintenance
- Open a Pull Request to `main` for every change.

## Linked Branches

For multi-step work, chain branches to preserve context and review flow:

1. `feature/step-1` from `main`
2. `feature/step-2` from `feature/step-1`
3. `feature/step-3` from `feature/step-2`

Each PR should clearly reference the previous PR in its description.

## PR Rules

- Keep PRs small and scoped.
- Include:
  - objective,
  - files changed,
  - risk level,
  - rollback notes.
- Do not merge without review approval.

## Adan Alignment Rule

If a change bypasses branch/PR governance, it is not Adan-compliant.
