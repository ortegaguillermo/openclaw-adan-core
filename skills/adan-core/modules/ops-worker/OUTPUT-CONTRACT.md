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
- `blocker`: string (`none` if not blocked)
- `next_action`: short sentence

## Human Summary (Spanish)

Include one short Spanish summary only when:
- a blocker exists, or
- policy asks for progress output.

Otherwise, no user-facing output is required.
