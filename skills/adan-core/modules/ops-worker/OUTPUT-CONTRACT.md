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

## Per-Comment Review Disposition Metrics (New in v1.3)

When `review_policy.per_comment_processing.enabled: true`, the worker output includes these additional fields. All metrics are scoped to the current worker run and only to review comments on PRs listed in `prs_touched`.

- `review_comments_total`: Total number of review comments observed in the current run
- `review_comments_dispositioned`: Number of comments with explicit disposition (applies/does_not_apply) in the current run
- `review_comments_undispositioned`: Number of comments lacking explicit disposition (in the current run)
- `review_comments_applies`: Number of comments with "applies" disposition (in the current run)
- `review_comments_does_not_apply`: Number of comments with "does_not_apply" disposition (in the current run)

**Relationship between addressed and dispositioned:**

In per-comment mode, a review comment is considered **addressed** when it has an explicit disposition. Producers SHOULD keep `review_comments_addressed` aligned with `review_comments_dispositioned`, and consumers MUST use the per-comment metrics (not a separate notion of "addressed") when implementing quality gates.

### Quality Gate

If `review_policy.per_comment_processing.fail_quality_gate_if_undispositioned: true` and `review_comments_undispositioned > 0`:
- Set `status: blocked`
- Include blocker reason: `"Review comments lack required dispositions: {review_comments_undispositioned} pending"`
- Do not merge or advance PR until all comments are addressed (i.e., `review_comments_undispositioned == 0`)

### Example Output (per-comment mode enabled)

```json
{
  "instance_id": "adan-core-builder",
  "status": "blocked",
  "repos_checked": ["openclaw-adan-core"],
  "issues_touched": ["https://github.com/ortegaguillermo/openclaw-adan-core/issues/32"],
  "prs_touched": ["https://github.com/ortegaguillermo/openclaw-adan-core/pull/37"],
  "ci_status": "green",
  "review_comments_addressed": 3,
  "review_comments_total": 5,
  "review_comments_dispositioned": 3,
  "review_comments_undispositioned": 2,
  "review_comments_applies": 2,
  "review_comments_does_not_apply": 1,
  "model_used": "github-copilot/claude-haiku-4.5",
  "blocker": "Review comments lack required dispositions: 2 pending",
  "next_action": "Address remaining 2 review comments with explicit disposition (applies/does_not_apply + reason)."
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
