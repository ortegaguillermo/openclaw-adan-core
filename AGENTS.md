# AGENTS.md — Authorship & Governance Guide

> **Not for distribution.** This file is for Adan Core maintainers and contributors.
> It is intentionally excluded from package installations and NPM/PyPI distributions.
> See [SKILL.md](./SKILL.md) for what is actually distributed.

## Purpose

This file documents how Adan Core itself is defined, maintained, and evolved.
It is not runtime behavior—it is guidance for the OpenClaw maintainer and contributors.

## Repository Guardianship

Adan Core lives in the `openclaw-adan-core` repository and serves as:

1. **Identity reference** — How Adan should behave, communicate, and reason.
2. **Governance scaffold** — Policies for feature flags, autonomous execution, memory management.
3. **Operational continuity** — Linked with workspace-local files (`SOUL.md`, `WORKFLOW_AUTO.md`, etc.) but never shipped with them.

## Key Files (In This Repo)

| File | Purpose | Distributed? |
|------|---------|---|
| `SOUL.md` | Persona + communication contract | ✅ Yes |
| `WORKFLOW_AUTO.md` | Autonomous execution protocol | ✅ Yes |
| `FEATURES.md` | Flag-gated capabilities | ✅ Yes |
| `ONBOARDING.md` | Setup flow | ✅ Yes |
| `templates/*` | Example configs | ✅ Yes |
| `modules/` | Skill scaffolding engines | ✅ Yes |
| `AGENTS.md` | This file (authorship guide) | ❌ No |
| `MEMORY.md` (workspace) | User's long-term memory | ❌ No (workspace-local) |
| `USER.md` (workspace) | User context | ❌ No (workspace-local) |

## What Gets Installed?

Installation/onboarding for Adan Core is file-based and documented canonically in
`README.md` (see the workspace install section) and `ONBOARDING.md`.
When a user follows that documented flow, they receive:

- SOUL.md template → becomes `~/.openclaw/workspace/SOUL.md` (user customizes)
- WORKFLOW_AUTO.md template → becomes `~/.openclaw/workspace/WORKFLOW_AUTO.md` (user customizes)
- FEATURES.md → reference copy (read-only)
- Relevant templates from `templates/*` → used during setup, then stored locally

**What they do NOT receive:**
- This `AGENTS.md` (repo guidance only)
- Workspace-local memory/user files (they create their own)

## Build & Release Validation

Before releasing Adan Core:

1. ✅ Confirm `.npmignore` and packaging rules exclude `AGENTS.md`.
2. ✅ Confirm `SKILL.md` explicitly lists excluded files.
3. ✅ Check CI/build logs that `AGENTS.md` was not packaged.
4. ✅ Document in CONTRIBUTING.md that AGENTS.md is repo-only.

See the release checklist in the `./docs/` directory for full pre-release validation.

## Contribution Guidelines

**When modifying this file:**

- This is the authorship contract and operational guide—keep it stable.
- Changes here should reflect policy decisions about Adan Core itself, not user-facing behavior.
- If you're fixing or adding a user-visible feature, update `SOUL.md` or `WORKFLOW_AUTO.md` instead.

**When adding new repo-only files:**

1. Document their purpose and exclusion here.
2. Add a comment in `.gitignore` or the build script explaining why.
3. Update CONTRIBUTING.md to note they are authorship-only.

## Connection to Workspace Config

When Adan Core is installed in a workspace:

```
~/.openclaw/workspace/
├── SOUL.md              ← From SOUL.md template (user-customized)
├── WORKFLOW_AUTO.md     ← From WORKFLOW_AUTO.md template (user-customized)
├── AGENTS.md            ← USER CREATES THIS LOCALLY (not from repo)
├── MEMORY.md            ← User's long-term memory (not from repo)
├── USER.md              ← User profile (not from repo)
└── skills/adan-core/    ← Distributed skill files (from this repo)
    ├── SOUL.md          ← Reference copy
    ├── WORKFLOW_AUTO.md ← Reference copy
    ├── FEATURES.md      ← Reference copy
    ├── ONBOARDING.md    ← Reference copy
    └── ...
```

The workspace `AGENTS.md`, `MEMORY.md`, and `USER.md` are the user's own governance files.
They are never part of the Adan Core package.

## See Also

- [CONTRIBUTING.md](./CONTRIBUTING.md) — Branching and PR rules
- [SKILL.md](./SKILL.md) — What is and is not distributed
- [ONBOARDING.md](./ONBOARDING.md) — First-time user setup
