# Ops Worker Instance with Autonomy Policy

This example shows how to configure an ops-worker instance that respects the autonomy governance policy.

## Configuration Template

```yaml
version: 1
instance_id: my-project-ops
enabled: true
timezone: America/Chicago

# Instance-level autonomy overrides (optional)
# If omitted, inherits from global adan.flags.json autonomyPolicy
autonomy_overrides:
  requireOwnerApprovedComment: true
  ownerApprovedToken: "approved"
  allowAutomergePR: false
  allowAutoclosePR: false

schedule:
  mode: cron
  expr: "*/20 * * * *"  # Every 20 minutes

repos:
  - id: my-project
    path: /home/user/projects/my-project
    default_branch: main

branch_policy:
  prefix: chore/
  sync_from_default_before_edit: true
  return_to_default_on_finish: true

review_policy:
  require_comment_disposition: true
  accepted_dispositions: [applies, does_not_apply]

alert_policy:
  routine_updates: never
  notify_on_blocker: always

scope_policy:
  include:
    - docs
    - templates
    - module_consistency
  exclude:
    - release_tagging
    - auto_merge
    - destructive_repo_rewrites

blocker_policy:
  escalate_if:
    - credentials_or_permissions
    - subjective_architecture_decision
    - external_dependency_outage
    - autonomy_policy_violation
```

## Key Sections

### `autonomy_overrides` (Optional)

Instance-level overrides for autonomy policy. If not specified, the instance inherits from the global `adan.flags.json`.

**Fields:**
- `requireOwnerApprovedComment`: bool — Require explicit owner/maintainer approval to start work.
- `ownerApprovedToken`: string — The exact token (case-sensitive) that owner must post.
- `allowAutomergePR`: bool — Allow automatic PR merging after approval.
- `allowAutoclosePR`: bool — Allow automatic PR closing after timeout.

### `scope_policy.exclude`

The ops-worker explicitly excludes:
- `release_tagging` — No autonomous version bumping or release tagging.
- `auto_merge` — PRs are never merged automatically.
- `destructive_repo_rewrites` — Force-push and rewrite are never allowed.

### `blocker_policy.escalate_if`

Added `autonomy_policy_violation` — If the autonomy rules block an action, escalate as a blocker.

## Workflow (With Autonomy Checks)

### Issue Processing

1. **Find Issue:** Worker scans for open issues.
2. **Check Autonomy Policy:**
   - If `requireOwnerApprovedComment: true` → look for owner comment with approval token.
   - If approval found → proceed to work.
   - If not found → log and skip (do not escalate as blocker; wait for next tick).
3. **Create Branch & Fix:** If authorized, create a branch and implement the fix.
4. **Open PR:** Push branch and open PR with reference to issue.
5. **Return to Main:** After PR is open, return to main branch and clean workspace.

### PR Review & Merge

1. **Monitor Reviews:** Wait for code review approvals.
2. **Check CI:** Ensure all checks pass.
3. **Check Merge Policy:**
   - If `allowAutomergePR: false` (default) → post notification, do not merge.
   - If `allowAutomergePR: true` → merge with standard commit message.
4. **Return to Main:** Pull latest, confirm merge succeeded, reset workspace.

## Example: Strict Public Repo

```yaml
version: 1
instance_id: public-repo-ops
enabled: true

autonomy_overrides:
  requireOwnerApprovedComment: true
  ownerApprovedToken: "approved"
  allowAutomergePR: false
  allowAutoclosePR: false

schedule:
  mode: cron
  expr: "*/30 * * * *"  # Every 30 minutes

repos:
  - id: open-source-project
    path: /home/dev/projects/open-source
    default_branch: main

scope_policy:
  include:
    - docs
    - templates
    - module_consistency
  exclude:
    - release_tagging
    - auto_merge
    - destructive_repo_rewrites

blocker_policy:
  escalate_if:
    - credentials_or_permissions
    - subjective_architecture_decision
    - autonomy_policy_violation
```

**Behavior:**
- Worker scans for issues every 30 minutes.
- Before starting work, requires owner comment containing "approved".
- PRs are never merged autonomously.
- Releases are never tagged autonomously.
- All blocked actions are logged.

## Example: Private Team Repo (Relaxed)

```yaml
version: 1
instance_id: team-repo-ops
enabled: true

autonomy_overrides:
  requireOwnerApprovedComment: true
  ownerApprovedToken: "go"
  allowAutomergePR: false
  allowAutoclosePR: false

schedule:
  mode: cron
  expr: "*/15 * * * *"  # Every 15 minutes

repos:
  - id: internal-platform
    path: /home/dev/projects/internal-platform
    default_branch: main

scope_policy:
  include:
    - docs
    - templates
    - code
    - tests
  exclude:
    - release_tagging
    - auto_merge

blocker_policy:
  escalate_if:
    - credentials_or_permissions
    - autonomy_policy_violation
```

**Behavior:**
- Approval token is shorter: "go" instead of "approved".
- Worker checks more frequently (every 15 minutes).
- Broader scope (includes code and tests).
- Still requires explicit approval before starting work.

## Audit Output

After each run, the ops-worker produces a compliance summary:

```json
{
  "instance_id": "my-project-ops",
  "status": "completed",
  "repos_checked": ["my-project"],
  "issues_touched": ["https://github.com/user/repo/issues/42"],
  "prs_touched": ["https://github.com/user/repo/pull/85"],
  "autonomy_policy_enforced": {
    "requireOwnerApprovedComment": true,
    "authorization_checks": 2,
    "authorizations_passed": 1,
    "authorizations_blocked": 1
  },
  "ci_status": "green",
  "review_comments_addressed": 3,
  "blocker": "none",
  "model_used": "google-antigravity/claude-opus-4-6-thinking"
}
```

## Testing Autonomy Policy in Ops-Worker

### Test Case 1: Issue Without Approval

**Scenario:**
- Issue #100 is open.
- No owner comment with approval token.
- Worker tick runs.

**Expected:**
- Issue is logged as "pending authorization".
- Worker does NOT create a branch.
- Log entry: `[BLOCKED] Issue #100: requireOwnerApprovedComment=true, token not found`.

### Test Case 2: Issue With Approval

**Scenario:**
- Issue #101 is open.
- Owner posts comment: "approved"
- Worker tick runs.

**Expected:**
- Worker creates branch and starts work.
- PR is opened with reference to issue.
- Notification posted to issue.

### Test Case 3: PR Review & Merge Policy

**Scenario:**
- Worker-created PR has all approvals.
- `allowAutomergePR: false`.
- Merge occurs.

**Expected:**
- PR is NOT merged by worker.
- Comment posted: "PR is ready for human merge. Please merge when ready."
- Worker waits for human action.

---

## References

- [autonomy-governance.md](./autonomy-governance.md) — Full autonomy policy specification
- [ops-worker-multi-instance.md](./ops-worker-multi-instance.md) — Multi-instance ops-worker guide
- [WORKFLOW_AUTO.md](../WORKFLOW_AUTO.md) — Core autonomous protocol
