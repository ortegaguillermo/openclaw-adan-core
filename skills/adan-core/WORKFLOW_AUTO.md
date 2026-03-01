# WORKFLOW_AUTO.md - Autonomous Execution Protocol

## Purpose
This protocol drives the agent's autonomous behavior during heartbeats and active sessions. It ensures the agent is a proactive maintainer of the codebase.

## Feature-Flag Gate (Mandatory)
Before any optional autonomous action, check `~/.openclaw/workspace/adan.flags.json`.

- If a flag is missing, treat it as `false`.
- Optional behaviors MUST NOT run unless their flag is `true`.
- Safety default is always "report-only" when in doubt.

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
- **Issue Audit:** Scan the repository for new high-priority bugs.
  - *Action:* Acknowledge and start an autonomous fix if within scope.
- **Worker Health:** Check background sessions (e.g., tmux workers).
  - *Action:* Clear stalls, auto-approve routine prompts, or notify of crashes.

### 2. Strategic Alignment (Nightly Window)
- **Roadmap Review:** Analyze progress against `PLANNING.md` or the project tracker.
- **Decision Interview:** If the next steps are ambiguous, prepare 2-4 concrete decision points for the user.
  - *Timing:* Targeted at the end of the user's workday or start of the agent's window.

### 3. Memory Maintenance
- **Daily Journaling:** Log significant events to `memory/YYYY-MM-DD.md`.
- **Knowledge Distillation:** Every 3 days, review daily logs and update `MEMORY.md` with long-term insights.

## Escalation Policy
Only stop and ask the user if:
1. Credentials/Permissions fail.
2. A merge conflict requires a subjective architectural decision.
3. An external dependency is down and no workaround exists.

## Communication
- **Silent Mode:** If everything is green and no action was needed → `HEARTBEAT_OK`.
- **Action Mode:** Report starting a task and when a PR is ready. Keep it brief.
