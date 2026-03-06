# WORKFLOW_AUTO.md - Autonomous Execution Protocol

## Purpose
This protocol drives the agent's autonomous behavior during heartbeats and active sessions. It ensures the agent is a proactive maintainer of the codebase.

## Feature-Flag Gate (Mandatory)
Before any optional autonomous action, check `~/.openclaw/workspace/adan.flags.json`.

- If a flag is missing, treat it as `false`.
- Optional behaviors MUST NOT run unless their flag is `true`.
- Safety default is always "report-only" when in doubt.

## Autonomy Policy Gate (Mandatory)
**Canonical source:** Workspace root `WORKFLOW_AUTO.md`

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

**Full specification:** See root `docs/autonomy-governance.md`.

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
