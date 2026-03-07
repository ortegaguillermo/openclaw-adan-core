# Ops Worker Output Contract

Every worker run must return a compact, machine-readable summary block.

## Required Fields

- `instance_id`: string
- `status`: `idle` | `working` | `blocked` | `completed`
- `repos_checked`: array of repo ids
- `issues_touched`: array of issue URLs
- `prs_touched`: array of PR URLs
- `ci_status`: `green` | `failing` | `mixed` | `unknown`
- `review_comments_total`: number (new in v1.3)
- `review_comments_addressed`: number
- `review_comments_dispositioned`: number (new in v1.3)
- `review_comments_applies`: number (new in v1.3)
- `review_comments_does_not_apply`: number (new in v1.3)
- `review_threads_resolved`: number (new in v1.3)
- `model_used`: string (`provider/model`; fallback: `unknown`)
- `blocker`: string (`none` if not blocked)
- `next_action`: short sentence

## Review Comment Disposition Tracking (New in v1.3)

These fields are always present in output. When `review_policy.require_comment_disposition: false`, set the related metrics to `0`.

- **`review_comments_total`**: Total number of review comments/threads on all PRs touched
- **`review_comments_addressed`**: Comments that received a response (old metric, kept for backward compatibility)
- **`review_comments_dispositioned`**: Comments with explicit disposition response (`applies` or `does_not_apply`)
- **`review_comments_applies`**: Count of comments with `applies` disposition
- **`review_comments_does_not_apply`**: Count of comments with `does_not_apply` disposition
- **`review_threads_resolved`**: Threads marked resolved via GitHub API (when `resolve_threads_when_possible: true`)

**Quality Gate:** When `review_policy.require_comment_disposition: true`, the worker fails if `review_comments_dispositioned < review_comments_total`. When `review_policy.require_comment_disposition: false`, this quality gate is disabled.

## Human Summary (Spanish)

Include one short Spanish summary only when:
- a blocker exists, or
- policy asks for progress output.

Otherwise, no user-facing output is required.


## GitHub Issue Comment Footer Requirement

For every GitHub issue status/closure comment posted by the worker, append:

`Model used: <provider/model>`

If model identity is unavailable, use:

`Model used: unknown`

### Example (status comment)

```text
Status: Documentation updates are in progress and PR #123 is open for review.
All 4 review comments addressed with dispositions: 2 applies, 2 does_not_apply.
Model used: google-antigravity/claude-opus-4-6-thinking
```
