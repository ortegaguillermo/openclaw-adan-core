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
