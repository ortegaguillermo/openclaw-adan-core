# Ops Worker Output Contract

Every worker run must return a compact, machine-readable summary block.

## Required Fields

- `instance_id`: string
- `status`: `idle` | `working` | `blocked` | `completed`
- `repos_checked`: array of repo ids
- `issues_touched`: array of issue URLs
- `prs_touched`: array of PR URLs
- `ci_status`: `green` | `failing` | `mixed` | `unknown`
- `review_comments_addressed`: number
- `model_used`: string (`provider/model`; fallback: `unknown`)
- `blocker`: string (`none` if not blocked)
- `next_action`: short sentence

## Per-Comment Review Disposition Metrics

When `review_policy.per_comment_processing.enabled: true`, include these additional fields:

- `review_comments_total`: total review comments received on all PRs
- `review_comments_dispositioned`: count of comments with explicit disposition (applies/does_not_apply)
- `review_comments_applies`: count with "applies" disposition
- `review_comments_does_not_apply`: count with "does_not_apply" disposition
- `review_threads_resolved`: count of threads marked as resolved (when API supports it)
- `review_undispositioned`: count of comments lacking disposition (should be 0 if `fail_quality_gate_if_undispositioned: true`)

### Quality Gate

If `review_policy.per_comment_processing.fail_quality_gate_if_undispositioned: true` and `review_undispositioned > 0`:
- Set `status: blocked`
- Include blocker reason: `"Review comments lack required dispositions: {review_undispositioned} pending"`
- Do not merge or advance PR until all comments are addressed

### Example Output Block (with per-comment metrics)

```json
{
  "instance_id": "tracksmith",
  "status": "completed",
  "repos_checked": ["rollerderby-client", "rollerderby-api"],
  "issues_touched": ["https://github.com/ortegaguillermo/rollerderby-client/issues/42"],
  "prs_touched": ["https://github.com/ortegaguillermo/rollerderby-client/pull/89"],
  "ci_status": "green",
  "review_comments_total": 6,
  "review_comments_dispositioned": 6,
  "review_comments_applies": 4,
  "review_comments_does_not_apply": 2,
  "review_threads_resolved": 6,
  "review_undispositioned": 0,
  "review_comments_addressed": 6,
  "model_used": "google-antigravity/claude-opus-4-6-thinking",
  "blocker": "none",
  "next_action": "PR ready for merge pending final sign-off."
}
```

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
Model used: google-antigravity/claude-opus-4-6-thinking
```

### Example (per-comment disposition response)

See `modules/ops-worker/templates/review-comment-disposition.example.md` for the full per-comment template.

Each comment response must include:
- Explicit disposition (`applies` or `does_not_apply`)
- Resolution details (files, change summary, commit SHA if applies)
- Technical justification if does_not_apply
- Model attribution footer
