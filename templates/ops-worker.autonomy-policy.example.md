# Ops Worker Instance with Autonomy Governance

This example shows how to configure an ops-worker instance that respects the autonomy governance policy.

## Quick Summary

- Ops-worker instances **inherit** autonomy policy from global `adan.flags.json` (no instance-level overrides in current version).
- Before starting work on any issue, the worker checks for owner approval.
- If approval is not found, the worker logs the decision and skips the task (does not escalate by default).
- All autonomous decisions are logged for audit trail.

## Configuration Template

```yaml
version: 1
instance_id: my-project-ops
enabled: true
timezone: America/Chicago

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
    - autonomy_policy_violation  # Optional: escalate if autonomy rules block work
```

## Autonomy Policy Inheritance

**Current Design (v0.4):**
- Each instance inherits autonomy policy from global `adan.flags.json`.
- No instance-level overrides yet.
- Future enhancement (v0.5+): `autonomy_overrides` field for per-instance customization.

**Global Policy Location:**
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

See root `docs/autonomy-governance.md` for full specification.

## Scope Policy Guidance

The ops-worker explicitly excludes high-risk actions:
- `release_tagging` — No autonomous version bumping or release tagging.
- `auto_merge` — PRs are never merged automatically (enforce via allowAutomergePR: false).
- `destructive_repo_rewrites` — Force-push and rewrite are never allowed.

## Blocker Policy Guidance

### Default Behavior (Skip Quietly)

By default, when autonomy policy blocks work (missing approval):
- Worker logs the decision to memory
- Worker skips the issue
- Worker does NOT escalate or notify

This keeps the agent proactive without pestering for every blocked decision.

### Optional: Escalate on Blocked Work

To escalate when autonomy policy blocks an issue:
```yaml
blocker_policy:
  escalate_if:
    - autonomy_policy_violation
```

When configured, blocked issues are escalated to user with reason.

## Workflow (With Autonomy Checks)

### Issue Processing

1. **Find Issue:** Worker scans for open issues.
2. **Check Autonomy Policy:**
   - If `requireOwnerApprovedComment: true` → look for owner comment with approval token (default: "approved").
   - If approval found → proceed to step 3.
   - If not found → **log decision** and **skip** (move to next issue).
3. **Create Branch & Fix:** If authorized, create a branch and implement the fix.
4. **Open PR:** Push branch and open PR with reference to issue.
5. **Return to Main:** After PR is open, return to main branch and clean workspace.

### PR Review & Merge

1. **Monitor Reviews:** Wait for code review approvals.
2. **Check CI:** Ensure all checks pass.
3. **Check Merge Policy:**
   - If `allowAutomergePR: false` (default) → post notification "Ready for human merge", do not merge.
   - If `allowAutomergePR: true` → merge with standard commit message.
4. **Return to Main:** Pull latest, confirm merge succeeded, reset workspace.

### PR Closure

1. **Monitor PR Age:** Check if PR is stale (no activity for >N hours).
2. **Check Closure Policy:**
   - If `allowAutoclosePR: false` (default) → do not close. Post notification and wait.
   - If `allowAutoclosePR: true` → close only if CI is failing and stale.
3. **Log Decision:** Record closure or skip in memory.

## Example 1: Strict Public Repo

```yaml
version: 1
instance_id: public-repo-ops
enabled: true
timezone: America/Chicago

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
- Before starting work, requires owner comment containing "approved" (inherited from global policy).
- PRs are never merged autonomously.
- Releases are never tagged autonomously.
- All blocked issues escalate to user (due to blocker_policy).

## Example 2: Team Repo (More Relaxed)

```yaml
version: 1
instance_id: team-repo-ops
enabled: true
timezone: America/Chicago

schedule:
  mode: cron
  expr: "*/20 * * * *"  # Every 20 minutes

repos:
  - id: team-internal-project
    path: /home/dev/projects/team-internal
    default_branch: main

scope_policy:
  include:
    - docs
    - templates
    - chore
  exclude:
    - release_tagging
    - auto_merge
    - destructive_repo_rewrites

blocker_policy:
  escalate_if:
    - credentials_or_permissions
    - external_dependency_outage
```

**Behavior:**
- Worker scans every 20 minutes.
- Same autonomy policy as global (inherited).
- Blocked issues are NOT escalated (skip quietly).
- Team members can review and approve work in PRs.

## Testing Autonomy Policy

### Test Case 1: Missing Owner Approval

**Setup:**
- Open issue without owner comment.
- autonomyPolicy: `requireOwnerApprovedComment: true`

**Expected Behavior:**
- Worker scans issue.
- Worker checks for owner approval comment.
- Approval token NOT found.
- Worker logs decision: "BLOCKED (autonomyPolicy) - waiting for owner approval".
- Worker skips issue and moves to next task.
- No PR is created.

### Test Case 2: Owner Approval Present

**Setup:**
- Open issue with owner comment: "approved" (or configured token).
- autonomyPolicy: `requireOwnerApprovedComment: true`

**Expected Behavior:**
- Worker scans issue.
- Worker checks for owner approval comment.
- Approval token found.
- Worker logs decision: "AUTHORIZED (autonomyPolicy) - owner approval found".
- Worker proceeds: creates branch, implements fix, opens PR.
- PR is opened with reference to original issue.

### Test Case 3: Merge Policy (allowAutomergePR: false)

**Setup:**
- PR from ops-worker is open.
- All CI checks pass.
- All code reviews approved.
- autonomyPolicy: `allowAutomergePR: false`

**Expected Behavior:**
- Worker monitors PR.
- Worker detects: all checks pass + all reviews approved.
- Worker checks merge policy: `allowAutomergePR: false`.
- Merge does NOT occur.
- Worker posts comment: "Ready for human merge review."
- Worker waits for human action.

### Test Case 4: Autonomy Escalation

**Setup:**
- Issue processing blocked due to missing approval.
- blocker_policy: `escalate_if: [autonomy_policy_violation]`

**Expected Behavior:**
- Worker logs blocked decision.
- Worker checks blocker_policy.
- escalate_if includes `autonomy_policy_violation`.
- Worker escalates to user: "Issue #123 blocked - waiting for owner approval."

## Audit Trail Format

When `logBlockedActions: true`, each decision is recorded:

```
[2026-03-06T06:30:15Z] AUTHORIZED (autonomyPolicy)
  - Instance: adan-core-builder
  - Issue: ortegaguillermo/openclaw-adan-core#27
  - Authorization: owner comment found with token='approved'
  - Action: proceeding with work (branch chore/autonomy-flag-fix)
  - Model used: github-copilot/claude-haiku-4.5

[2026-03-06T06:45:22Z] BLOCKED (autonomyPolicy)
  - Instance: my-project-ops
  - Issue: ortegaguillermo/some-repo#42
  - Reason: requireOwnerApprovedComment=true, token='approved' not found
  - Owner comment required: please reply with 'approved' to authorize work
  - Status: skipped (waiting for authorization)
```

## Future Enhancements (v0.5+)

### Proposed: Instance-Level Autonomy Overrides

Future versions may support per-instance autonomy customization:

```yaml
version: 1
instance_id: future-ops
enabled: true

# Proposed for v0.5+
# autonomy_overrides:
#   require_owner_approved_comment: false  # relax for internal team
#   allow_auto_merge_pr: false             # strict no-auto-merge
```

This feature is **not yet available**. For now, all instances inherit global policy.

## Full Reference

- **Autonomy Governance Spec:** Root `docs/autonomy-governance.md`
- **Workflow Automation Protocol:** Root `WORKFLOW_AUTO.md`
- **Flags File Location:** Root `~/.openclaw/workspace/adan.flags.json`
- **Schema:** `modules/ops-worker/templates/ops-worker.instance.schema.yaml`
