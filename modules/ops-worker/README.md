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
- Review comment disposition example template: `modules/ops-worker/templates/review-comment-disposition.example.md`

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

### Start Announcement (New in v1.2)

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

## Per-Comment Review Disposition (New in v1.3)

Ops-worker can be configured to require explicit disposition (applies/does_not_apply) for every review comment on a PR, ensuring comprehensive engagement with reviewer feedback.

> Note: this section defines the expected behavior contract for ops-worker implementations. Runtime wiring may be provided by the host/orchestrator while preserving this contract.

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

- When `per_comment_processing.enabled: true`, the worker processes each review comment individually
- For every review comment, a response is posted in that same thread with explicit disposition
- Each response must include:
  - **Disposition:** `applies` or `does_not_apply` (required)
  - **Reason:** Brief technical justification (required for both)
  - **Resolution details:** When disposition is `applies`, include affected files, change summary, and commit SHA (if `require_resolution_details_on_applies: true`)
- If `resolve_threads_when_possible: true`, threads are marked resolved after responding and implementing changes
- If any comment lacks disposition and `fail_quality_gate_if_undispositioned: true`, the worker blocks PR advancement with `status: blocked`

**Response Template:**

See `modules/ops-worker/templates/review-comment-disposition.example.md` for complete examples of both disposition types.

**Quality Gate:**

The quality gate fails if:
- `fail_quality_gate_if_undispositioned: true` (config), AND
- One or more review comments on PRs listed in `prs_touched` lack an explicit disposition

Output will include:
- `review_comments_total` — Total review comments observed
- `review_comments_dispositioned` — Comments with explicit disposition
- `review_comments_undispositioned` — Comments lacking disposition (blocks if > 0 and policy requires)
- `review_comments_applies` — Count of "applies" dispositions
- `review_comments_does_not_apply` — Count of "does_not_apply" dispositions

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

