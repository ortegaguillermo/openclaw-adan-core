# Ops Worker Module (Multi-Instance)

This module enables reusable, independent operations workers for one or more repositories.

Use this module when you want project-specific execution loops (issue -> branch -> tests -> PR -> review handling) without coupling all behavior to the main session.

## Design

- One shared engine, many instances.
- Each instance is declared as data in `ops-workers/*.yaml`.
- Instances can target mono-repo or multi-repo projects.
- Each instance has isolated locks, schedule, scope, and alert policy.

## Required Files

- Instance schema: `modules/ops-worker/templates/ops-worker.instance.schema.yaml`
- Instance example: `modules/ops-worker/templates/ops-worker.instance.example.yaml`
- Final report contract: `OUTPUT-CONTRACT.md`
- Issue status comment example template: `modules/ops-worker/templates/issue-comment.status.example.md`
- Per-comment review disposition template: `modules/ops-worker/templates/review-comment-disposition.example.md`

## Instance Extension Policy

Workspace instances (`~/.openclaw/workspace/ops-workers/*.yaml`) may include additional fields beyond the base schema for instance-specific tuning. However:

1. **All required schema fields** must be present and valid.
2. **Extension fields** (e.g., `issue_selection_policy`) should be documented in the instance file as comments or in a companion `*.schema-extensions.yaml`.
3. **Template examples** in this repo reflect only the base schema; do not add instance-specific fields to `modules/ops-worker/templates/ops-worker.instance.example.yaml`.

This separation allows:
- Base schema to remain stable and reusable across installs.
- Instances to be customized without forking the schema.
- Clear distinction between what's required everywhere vs. what's tuned locally.

## Core Capabilities

### Per-Comment Review Disposition (New in v1.3)

Ops-worker can process **individual review comments** on PRs with explicit disposition tracking, ensuring every comment receives a targeted response rather than ambiguous global summaries.

**Configuration:**

```yaml
review_policy:
  require_comment_disposition: true
  accepted_dispositions: [applies, does_not_apply]
  per_comment_processing:
    enabled: true
    resolve_threads_when_possible: true
    require_resolution_details_on_applies: true
    fail_quality_gate_if_undispositioned: true
```

**Behavior:**

- For each review comment on the PR, the worker generates an individual response **in the same thread**.
- Each response includes one of two dispositions:
  - `applies` — the comment's suggestion is within scope; implementation details and commit SHA are provided.
  - `does_not_apply` — the comment is outside scope; a technical justification and reference links are provided.
- If `resolve_threads_when_possible: true`, the worker attempts to mark the thread as resolved after responding.
- If `fail_quality_gate_if_undispositioned: true`, any comment without an explicit disposition blocks the PR and sets worker status to `blocked`.

**Response Template:**

See `modules/ops-worker/templates/review-comment-disposition.example.md` for full format.

Minimum required per response:
- **Disposition header:** `**Disposition: applies**` or `**Disposition: does_not_apply**`
- **If applies:** files affected, resolution details, commit SHA (if available)
- **If does_not_apply:** technical reason, reference links to scope docs or related issues
- **Model attribution:** `Model used: <provider/model>` footer (required)

**Output Metrics:**

The worker output contract includes these metrics when per-comment processing is enabled:

```json
{
  "review_comments_total": 6,
  "review_comments_dispositioned": 6,
  "review_comments_applies": 4,
  "review_comments_does_not_apply": 2,
  "review_threads_resolved": 6,
  "review_undispositioned": 0
}
```

**Quality Gate:**

If a PR has undispositioned comments and `fail_quality_gate_if_undispositioned: true`:
- Worker status: `blocked`
- Blocker reason: `"Review comments lack required dispositions: N pending"`
- PR cannot advance until all comments are addressed

### Start Announcement (Introduced in v1.2)

Ops-worker can automatically announce when it starts processing a GitHub issue, providing real-time visibility into task execution.

> Note: this section defines the expected behavior contract for ops-worker implementations. Runtime wiring may be provided by the host/orchestrator while preserving this contract.

**Configuration:**

```yaml
start_announcement:
  enabled: true
  channel: telegram
  target: "<TELEGRAM_CHAT_ID>"  # Replace with your Telegram chat or channel ID before enabling announcements
  timezone: America/Chicago
  include_fields:
    - issue_number
    - issue_title
    - branch
    - start_time
  # Optional custom template:
  # template: "🚀 Starting issue #{issue_number}: {issue_title} | branch: {branch}"
```

**Behavior:**

- Announcement fires immediately after the issue is selected/claimed, **before any code changes**.
- Included fields are substituted into the template or default message format.
- If announcement sending fails, a warning is logged but the issue work continues (non-blocking).
- Duplicate prevention: one announcement per issue per worker run cycle.

**Available placeholders:**
- `{issue_number}` — GitHub issue number
- `{issue_title}` — Issue title (first 100 chars by default)
- `{issue_url}` — Full GitHub issue URL
- `{branch}` — Branch name that will be created/used
- `{start_time}` — Current timestamp in configured timezone
- `{assignee}` — GitHub user assigned to the issue (if any)

**Default format (when template is omitted):**
```
🚀 Starting issue #{issue_number}: {issue_title}
Branch: {branch}
Time: {start_time}
```

## Runtime Rules

1. Treat missing flags as disabled.
2. Run only enabled instances.
3. Acquire lock before action (`.ops/<instance-id>.lock`).
4. Ensure clean workspace and fresh branch before edits.
5. Return to `main` and pull latest after each completed task.
6. Notify user only on blockers unless policy overrides.
7. Append model attribution footer to every GitHub issue status/closure comment: `Model used: <provider/model>` (fallback: `Model used: unknown`).

## Feature Flags

- `ops_worker_mode` (boolean): Enable/disable ops worker execution globally.

Default for all new installs: `false`.

Instance discovery is automatic from `~/.openclaw/workspace/ops-workers/*.yaml` files; **no separate `ops_worker_instances` flag is required**. (Older documentation may reference `ops_worker_instances` as a deprecated approach; the multi-instance workflow auto-discovers instances from YAML files.)

