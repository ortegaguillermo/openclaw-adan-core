# Feature Flags

Adan Core uses **opt-in feature flags**. New capabilities are disabled by default and must be explicitly enabled.

## Flag File

Create this file in your workspace root:

`~/.openclaw/workspace/adan.flags.json`

Example:

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

**Fields:**

- `enabled` (default: `true`)
  - If true: autonomy rules are enforced.
  - If false: all restrictions are disabled (⚠️ unsafe).

- `requireOwnerApprovedComment` (default: `true`)
  - If true: agent MUST find owner comment with `ownerApprovedToken` before starting work on an issue.
  - If false: agent may work without explicit approval (not recommended).

- `ownerApprovedToken` (default: `"approved"`)
  - The exact token (case-sensitive) that owner/maintainer must post in a comment.
  - Example: `"approved"`, `"go"`, `"adan proceed"`.

- `allowAutoclosePR` (default: `false`)
  - If true: agent may close stale PRs after timeout.
  - If false (recommended): PRs remain open for human decision.

- `allowAutomergePR` (default: `false`)
  - If true: agent may merge PRs after all checks pass.
  - If false (recommended): PRs require human merge.

- `allowAutotagRelease` (default: `false`)
  - If true: agent may tag and create releases autonomously.
  - If false (recommended): releases require human approval.

- `logBlockedActions` (default: `true`)
  - If true: all blocked actions are logged to workspace memory.
  - Enables audit trail and historical review.

**Full specification:** [autonomy-governance.md](docs/autonomy-governance.md)

**Usage in ops-worker:** Instance YAML files may override autonomy policy with `autonomy_overrides` block. See [ops-worker.autonomy-policy.example.md](templates/ops-worker.autonomy-policy.example.md).

## Release Policy

- `main` = stable, backward-safe defaults.
- `next` = experimental.
- New features MUST ship with default `false` unless explicitly safe.
- No automatic updates: upgrades are manual via `scripts/upgrade.sh`.
