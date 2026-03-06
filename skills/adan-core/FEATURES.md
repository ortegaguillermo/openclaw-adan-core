# Feature Flags

Adan Core uses **opt-in feature flags**. New capabilities are disabled by default and must be explicitly enabled.

**Canonical source:** Workspace root `FEATURES.md`

## Flag File

Create this file in your workspace root:

`~/.openclaw/workspace/adan.flags.json`

Example (see root `templates/adan.flags.example.json` for full current example):

```json
{
  "version": 1,
  "features": {
    "autofix_ci": false,
    "auto_pr_comments": false,
    "nightly_roadmap_interview": true,
    "quota_auto_rebalance": false,
    "aggressive_worker_autoapprove": false,
    "meta_skill_synthesizer": false,
    "weekly_refinery": false,
    "ops_worker_mode": false
  },
  "autonomyPolicy": {
    "enabled": true,
    "requireOwnerApprovedComment": true,
    "ownerApprovedToken": "approved",
    "allowAutoclosePR": false,
    "allowAutomergePR": false,
    "allowAutotagRelease": false,
    "logBlockedActions": true
  }
}
```

## Flag Catalog

- `autofix_ci` (default: `false`)
  - If true: agent may push CI fixes automatically.
  - Risk: autonomous code changes.

- `auto_pr_comments` (default: `false`)
  - If true: agent may post PR comments/status updates automatically.
  - Risk: noisy or premature comments.

- `nightly_roadmap_interview` (default: `true`)
  - If true: asks roadmap questions in configured time window.
  - Risk: extra notifications.

- `quota_auto_rebalance` (default: `false`)
  - If true: allows automated account/model rebalancing decisions.
  - Risk: provider/account churn.

- `aggressive_worker_autoapprove` (default: `false`)
  - If true: auto-approves routine worker prompts.
  - Risk: approving unintended actions.

- `meta_skill_synthesizer` (default: `false`)
  - If true: enables optional meta-skill scaffolding workflows.
  - Module path: `modules/skill-synthesizer/`.
  - Risk: low-quality generated scaffolds without review.

- `weekly_refinery` (default: `false`)
  - If true: enables weekly learning audit/promotion routines.
  - Governance references: `docs/v0.4-governance-proposal.md`, `templates/learnings/GOVERNANCE_LOG.md`.
  - Risk: promoting weak patterns without strong governance.

- `ops_worker_mode` (default: `false`)
  - If true: enables independent, multi-instance ops workers declared in `ops-workers/*.yaml`.
  - Module path: `modules/ops-worker/`.
  - Risk: autonomous repo operations if instance scope is too broad.

## Autonomy Policy

The `autonomyPolicy` block governs high-risk autonomous actions (issue work, PR merging, release tagging).

**Default behavior:** Safe by default. All high-risk actions disabled unless explicitly enabled.

**Structure:**

```json
{
  "autonomyPolicy": {
    "enabled": true,
    "requireOwnerApprovedComment": true,
    "ownerApprovedToken": "approved",
    "allowAutoclosePR": false,
    "allowAutomergePR": false,
    "allowAutotagRelease": false,
    "logBlockedActions": true
  }
}
```

**Field Reference:**

| Field | Type | Default | Meaning |
|-------|------|---------|---------|
| `enabled` | bool | `true` | Activate all autonomy rules. |
| `requireOwnerApprovedComment` | bool | `true` | Require explicit owner comment with token before starting issue work. |
| `ownerApprovedToken` | string | `"approved"` | Case-sensitive token that owner must post in comment. |
| `allowAutoclosePR` | bool | `false` | Allow autonomous PR closing (dangerous; keep false). |
| `allowAutomergePR` | bool | `false` | Allow autonomous PR merging (dangerous; keep false). |
| `allowAutotagRelease` | bool | `false` | Allow autonomous release tagging (very dangerous; keep false). |
| `logBlockedActions` | bool | `true` | Log all blocked decisions to audit trail. |

**Full specification and workflow integration:** See root `docs/autonomy-governance.md` and `WORKFLOW_AUTO.md`.

## Release Policy

- `main` = stable, backward-safe defaults.
- `next` = experimental.
- New features MUST ship with default `false` unless explicitly safe.
- No automatic updates: upgrades are manual via `scripts/upgrade.sh`.
