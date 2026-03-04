# TRIGGER-GUIDELINES.md
# How to Write Clear, Unambiguous Skill Triggers

This template helps you design skill triggers that avoid false positives and collisions (issues FT-03 and FT-05).

## Trigger Types

### 1. Command (Slash Commands)
**Pattern:** `/<skill-verb> [object] [options]`

✅ **Good Examples:**
- `/fetch-github issue <owner/repo> <issue-id>`
- `/summarize-pdf <url> --lang es`
- `/run-healthcheck pve2 --full`

❌ **Bad Examples:**
- `/fetch` (too generic, collides with other fetch commands)
- `/check` (unclear what to check)
- `/help` (ambiguous, might conflict with system help)

**Rule:** Use specific action verb + object/scope.

---

### 2. Workflow (GitHub Actions, CI)
**Pattern:** Trigger only on specific events + conditions

✅ **Good Examples:**
```yaml
on:
  pull_request:
    types: [opened, synchronize]
    paths:
      - 'modules/ops-worker/**'
      - 'templates/**'
```

❌ **Bad Examples:**
```yaml
on: [push, pull_request]  # Runs on everything
```

**Rule:** Narrow by event type, path filters, labels, or branch patterns.

---

### 3. Heartbeat / Cron
**Pattern:** Scheduled tasks with side-effect guards

✅ **Good Examples:**
- Heartbeat: "Check CI status on main; alert only if red"
- Cron: "Every Monday 9 AM, audit external API quota usage"

❌ **Bad Examples:**
- Heartbeat: "Do general maintenance" (too vague)
- Cron: "Update everything" (no guard against conflicts)

**Rule:** State exactly what condition triggers action; include locks to prevent duplicate work.

---

### 4. Webhook
**Pattern:** Specific event + path or label conditions

✅ **Good Examples:**
- GitHub webhook: push to `chore/` branches only
- Slack webhook: messages in `#automation-requests` channel
- Email: subject line contains `[SKILL-REQUEST]`

❌ **Bad Examples:**
- "Any GitHub event" (fires constantly)
- "Any Slack message" (noise)

**Rule:** Use event content filtering, not just event type.

---

## Specificity Checklist

Before declaring a skill's trigger production-ready, answer:

- [ ] Does the trigger pattern match ONLY the intended use case?
- [ ] Are there other skills or commands that could fire on the same pattern?
- [ ] Does the trigger include enough context to identify the request (e.g., repo name, issue ID)?
- [ ] If async/scheduled, is there a lock to prevent duplicate runs?
- [ ] If requires external resources, are credentials/API keys validated before execution?

---

## Approval Boundary: When to Require Human Approval

Triggers that touch external systems or have side effects MUST include an approval gate in the skill's metadata:

✅ **Always require approval for:**
- Destructive operations (delete, rewrite, force push)
- External API calls with costs
- Production environment changes
- Cross-org integration
- Release tagging or deployment

❌ **Safe for autonomous execution:**
- Read-only fetch/analyze operations
- Internal workspace changes (commits, branches)
- Report generation and documentation
- Logs and monitoring queries

---

## References

- Field testing case FT-03: Trigger ambiguity caused false positives
- Field testing case FT-05: Missing approval gate for external dependency approval
- Related docs: `docs/field-testing-protocol.md`, `docs/field-testing-results-2026-02-28.md`
