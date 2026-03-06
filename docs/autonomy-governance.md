# Autonomy Governance Policy (v0.4+)

## Overview

Adan Core includes explicit autonomy controls that enforce strict limits on high-risk actions.
These rules are configurable via the `autonomyPolicy` block in `adan.flags.json`.

**Default behavior:** safe by default (deny-access) with explicit opt-in required.

---

## Policy Structure

### Field Reference

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

### Fields Explained

| Field | Type | Default | Meaning |
|-------|------|---------|---------|
| `enabled` | bool | `true` | Activate all autonomy rules. If `false`, this policy is completely disabled (⚠️ unsafe for public repos). |
| `requireOwnerApprovedComment` | bool | `true` | Adan requires explicit owner/maintainer comment with `ownerApprovedToken` before starting work on an issue. |
| `ownerApprovedToken` | string | `"approved"` | The exact token (case-sensitive) that owner must post in a comment to authorize work. Common values: `"approved"`, `"adan go"`, `"+1"`. |
| `allowAutoclosePR` | bool | `false` | Allow Adan to close a PR autonomously (e.g., due to stale CI). ⚠️ Dangerous; recommend `false`. |
| `allowAutomergePR` | bool | `false` | Allow Adan to merge a PR autonomously after review approval. ⚠️ Dangerous; recommend `false`. |
| `allowAutotagRelease` | bool | `false` | Allow Adan to tag and create releases autonomously. ⚠️ Very dangerous; recommend `false`. |
| `logBlockedActions` | bool | `true` | Log all blocked actions to audit trail (workspace/memory or repo issues). |

---

## Authorization Flow

### Issue Work Authorization

**Scenario:** Adan encounters an open issue marked as a bug or feature request.

1. **Check:** Is `autonomyPolicy.enabled == true`?
   - If no → skip to step 3.
   - If yes → continue.

2. **Check:** Is `requireOwnerApprovedComment == true`?
   - If no → Adan may proceed to assess and work on the issue.
   - If yes → look for owner comment containing `ownerApprovedToken` (case-sensitive).
     - **Found:** Adan may proceed with work.
     - **Not found:** Adan must NOT start work. Log and skip.

3. **Proceed or Skip:** Adan either:
   - **Proceed:** Create branch, implement, test, open PR.
   - **Skip:** Acknowledge in workspace log and move to next task.

### Pull Request Actions

**Scenario:** Adan has a PR open (self-created or under review).

#### Auto-Close (if `allowAutoclosePR == true`)
- Adan may close a PR if CI has been failing for >N hours and no human activity detected.
- **Else:** Adan must leave the PR open and notify maintainer.

#### Auto-Merge (if `allowAutomergePR == true`)
- Adan may merge a PR if:
  - All CI checks pass, AND
  - All code reviews are approved, AND
  - Branch is up-to-date with main.
- **Else:** Adan must wait for human approval to merge.

### Release Tagging (if `allowAutotagRelease == true`)
- Adan may tag a release and publish release notes **only if**:
  - All required checks pass (CI, security, documentation).
  - No breaking changes without CHANGELOG update.
  - Version follows SemVer conventions.
- **Else:** Adan must request explicit human approval before tagging.

---

## Audit & Compliance

### Blocked Action Log

When `logBlockedActions == true`, Adan records every blocked decision:

```
[2026-03-06T06:15:22Z] BLOCKED (autonomyPolicy)
  - Issue: ortegaguillermo/openclaw-adan-core#27
  - Reason: requireOwnerApprovedComment=true, ownerApprovedToken not found
  - Owner comment required: please reply with 'approved' to authorize work
  - Status: skipped (waiting for authorization)
```

### Audit Trail Location

Blocked actions are recorded in:
- **Workspace log:** `~/.openclaw/workspace/memory/YYYY-MM-DD.md`
- **GitHub:** issue comment (if policy enforcement is GitHub-aware)

### Review & Adjustment

Periodically review the blocked log to:
1. Identify patterns of blocked work.
2. Adjust policy if the token/threshold is too strict.
3. Provide feedback to maintainer on common blockers.

---

## Configuration Examples

### Example 1: Strict Public Repo (Recommended Default)

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

**Use case:** Public repositories with broad contributor base. Every work item requires explicit owner blessing.

### Example 2: Moderately Trusted Team Repo

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

**Use case:** Private team repository. Same as Example 1 (safer is better).

### Example 3: Aggressive Autonomous Mode (Danger Zone)

```json
{
  "autonomyPolicy": {
    "enabled": false
  }
}
```

**⚠️ Use case:** Only in development/sandbox environments with no real data.
**Risk:** Adan may autonomously merge code, close PRs, and tag releases without human review.

---

## Integration with WORKFLOW_AUTO.md

The `WORKFLOW_AUTO.md` protocol is enhanced with autonomy checks:

### Before Starting Issue Work
```markdown
1. Read `autonomyPolicy` from flags.
2. If `requireOwnerApprovedComment == true`:
   - Search issue comments for owner/maintainer mention.
   - Match exact token: `ownerApprovedToken`.
   - Proceed only if match found.
3. Log decision to workspace memory.
```

### Before Merging PR
```markdown
1. If `allowAutomergePR == false` (recommended):
   - Do NOT merge. Notify maintainer.
   - Wait for human approval.
2. If `allowAutomergePR == true`:
   - Verify all checks pass.
   - Merge with standard commit message.
   - Log action to audit trail.
```

---

## Testing & Validation

### Test Case 1: Authorization Required

**Setup:**
- `requireOwnerApprovedComment: true`
- `ownerApprovedToken: "approved"`
- Issue opened without owner comment.

**Expected:** Adan logs block and skips work.

### Test Case 2: Authorization Granted

**Setup:**
- Same as Test Case 1.
- Owner posts comment: "approved"

**Expected:** Adan proceeds with work.

### Test Case 3: Wrong Token

**Setup:**
- `ownerApprovedToken: "approved"`
- Owner posts comment: "looks good!"

**Expected:** Adan logs block (token mismatch) and skips work.

### Test Case 4: Auto-Merge Blocked

**Setup:**
- `allowAutomergePR: false`
- PR is open with all checks passing.

**Expected:** Adan does not merge; notifies maintainer.

---

## Migration & Rollout

### Step 1: Deploy Policy Definition
- Update `adan.flags.example.json` with `autonomyPolicy` block.
- No behavior change yet (feature is off by default).

### Step 2: Add Policy Enforcement
- Update `WORKFLOW_AUTO.md` with authorization checks.
- Existing agents remain unaffected (feature gate off).

### Step 3: Trial in Controlled Repo
- Enable in staging/private repository.
- Monitor blocked actions for 24-48 hours.
- Adjust `ownerApprovedToken` based on feedback.

### Step 4: Rollout to Production
- Document configuration in `ONBOARDING.md`.
- Provide template in workspace setup.
- Recommend strict defaults for public repos.

---

## FAQ

**Q: What if I forget to post the approval comment?**  
A: Adan will skip the work and log it. Check `memory/YYYY-MM-DD.md` for blocked items. Post the approval comment and Adan will pick it up on the next cron tick.

**Q: Can I use a shorter approval token?**  
A: Yes. Change `ownerApprovedToken` to something shorter like `"go"` or `"ok"`. Tokens are case-sensitive.

**Q: What if my PR is failing CI and I want Adan to auto-close it?**  
A: Set `allowAutoclosePR: true` and Adan will close stale PRs after a timeout. Recommended only for development repos.

**Q: Is the policy checked on every cron tick?**  
A: Yes. Adan checks the policy on every operation. You can change it at runtime (e.g., relax it after vetting a new issue).

**Q: What happens if `enabled: false`?**  
A: All autonomy rules are disabled. Adan will work without restrictions. ⚠️ Only use in sandboxed environments.

---

## References

- [WORKFLOW_AUTO.md](../WORKFLOW_AUTO.md) — Core autonomous protocol
- [ONBOARDING.md](../ONBOARDING.md) — Initial setup and flags
- [v0.4-governance-proposal.md](./v0.4-governance-proposal.md) — Governance strategy
