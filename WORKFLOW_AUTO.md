# WORKFLOW_AUTO.md - Autonomous Execution Protocol

## Purpose
This protocol drives the agent's autonomous behavior during heartbeats and active sessions. It ensures the agent is a proactive maintainer of the codebase.

## Feature-Flag Gate (Mandatory)
Before any optional autonomous action, check `~/.openclaw/workspace/adan.flags.json`.

- If a flag is missing, treat it as `false`.
- Optional behaviors MUST NOT run unless their flag is `true`.
- Safety default is always "report-only" when in doubt.

## Autonomy Policy Gate (Mandatory)
Before starting work on any issue or high-risk action, check the `autonomyPolicy` block in `adan.flags.json`.

**If `autonomyPolicy` block is missing,** treat as if all strict defaults apply:
- `enabled: true`
- `requireOwnerApprovedComment: true`
- `ownerApprovedToken: "approved"`
- `allowAutoclosePR: false`
- `allowAutomergePR: false`
- `allowAutotagRelease: false`
- `logBlockedActions: true`

**Authorization Flow:**
1. Is `autonomyPolicy.enabled` true?
   - If no → policy is disabled (only safe for development).
   - If yes → proceed to step 2.

2. Is `autonomyPolicy.requireOwnerApprovedComment` true?
   - If no → agent may proceed without explicit approval.
   - If yes → **agent MUST find** an owner/maintainer comment containing the exact token in `ownerApprovedToken`.
     - **Token found:** Proceed with work.
     - **Token not found:** Log and skip (do not start work).

3. Log the decision (approved or blocked) to `memory/YYYY-MM-DD.md`.

**Example (Blocked):**
```
[2026-03-06T06:15:22Z] BLOCKED (autonomyPolicy)
  - Issue: ortegaguillermo/openclaw-adan-core#27
  - Reason: requireOwnerApprovedComment=true, ownerApprovedToken='approved' not found
  - Owner comment required: please reply with 'approved' to authorize work
  - Status: skipped (waiting for authorization)
```

**Example (Approved):**
```
[2026-03-06T06:15:22Z] AUTHORIZED (autonomyPolicy)
  - Issue: ortegaguillermo/openclaw-adan-core#26
  - Authorization: owner comment found with token='approved'
  - Action: proceeding with work (branch chore/autonomy-flag-templates)
  - Model used: google-antigravity/claude-opus-4-6-thinking
```

**Full specification:** See `docs/autonomy-governance.md`.

## Custom Overlay Files (Optional)
Before executing daily loops, load these files when present:
- `GOALS.md`
- `USER_CUSTOM.md`
- `WORKFLOW_CUSTOM.md`

Use them as additive priorities and preferences only.
They MUST NOT override safety constraints or feature-flag policy.

## Autonomous Loop

### 1. Project Pulse (Every Heartbeat)
- **CI/CD Monitor:** Check the status of the latest PRs and main branch.
  - *Action:* If CI is failing, investigate the logs, create a fix branch, and push a repair.
  - *Autonomy Check:* Respect autonomy policy before pushing fixes.
- **Issue Audit:** Scan the repository for new high-priority bugs.
  - *Action:* Acknowledge and start an autonomous fix if within scope.
  - *Autonomy Check:* Verify owner approval (if required) before starting work.
- **Worker Health:** Check background sessions (e.g., tmux workers).
  - *Action:* Clear stalls, auto-approve routine prompts, or notify of crashes.

### 2. Strategic Alignment (Nightly Window)
- **Roadmap Review:** Analyze progress against `PLANNING.md` or the project tracker.
- **Decision Interview:** If the next steps are ambiguous, prepare 2-4 concrete decision points for the user.
  - *Timing:* Targeted at the end of the user's workday or start of the agent's window.

### 3. Memory Maintenance
- **Daily Journaling:** Log significant events to `memory/YYYY-MM-DD.md`.
  - Include autonomy policy decisions (approved/blocked).
- **Knowledge Distillation:** Every 3 days, review daily logs and update `MEMORY.md` with long-term insights.

## High-Risk Actions (Require Autonomy Policy Checks)

Before performing these actions, check `autonomyPolicy` in `adan.flags.json`:

### Starting Work on an Issue
1. Check `autonomyPolicy.requireOwnerApprovedComment`.
2. If true, search for owner comment containing `autonomyPolicy.ownerApprovedToken`.
3. If not found, log block and skip work.
4. If found, proceed with branch creation and implementation.

### Merging a Pull Request
1. Check `autonomyPolicy.allowAutomergePR`.
2. If false (recommended), do NOT merge. Post notification: "Ready for human merge."
3. If true, verify all CI checks pass and all reviews are approved, then merge.
4. Log the merge with model attribution: `Model used: <provider/model>`.

### Closing a Pull Request
1. Check `autonomyPolicy.allowAutoclosePR`.
2. If false (recommended), do NOT close. Post notification and wait for human decision.
3. If true, close only if stale (no activity >N hours) and CI is failing.
4. Log the closure with reason and model attribution.

### Tagging a Release
1. Check `autonomyPolicy.allowAutotagRelease`.
2. If false (recommended), do NOT tag. Require explicit human approval.
3. If true, verify changelog is updated and all required checks pass.
4. Log the release tag with model attribution.

## Escalation Policy
Only stop and ask the user if:
1. Credentials/Permissions fail.
2. A merge conflict requires a subjective architectural decision.
3. An external dependency is down and no workaround exists.
4. **Autonomy policy blocks an action and requires human decision.**

## Communication
- **Silent Mode:** If everything is green and no action was needed → `HEARTBEAT_OK`.
- **Action Mode:** Report starting a task and when a PR is ready. Include autonomy policy status if relevant.
- **Blocked (Skip-Quietly) Mode:** If autonomy policy blocks work (missing approval), log to memory and move to next task silently. Do NOT escalate as blocker by default.
  - **Exception:** Escalate only if an instance-level `blocker_policy.escalate_if` includes `autonomy_policy_violation`.

## Audit Trail
Every autonomy decision must be logged:
- **Location:** `~/.openclaw/workspace/memory/YYYY-MM-DD.md`
- **Format:** Timestamp, action, policy field, result (approved/blocked), reason.
- **Model attribution:** Every GitHub issue status/closure comment must include `Model used: <provider/model>`.

### Logging vs Escalation (Important Distinction)

**`logBlockedActions: true`** controls:
- Whether blocked decisions are recorded in audit trail (memory files).
- Does NOT control escalation/notification behavior.

**Escalation** is controlled by:
- Instance-level `blocker_policy.escalate_if` (for ops-worker).
- Global escalation policy in `WORKFLOW_AUTO.md` (for main agent).

Default recommendation:
- Log all blocked autonomy decisions (for accountability).
- Skip quietly unless explicitly configured to escalate.

