# Review Comment Disposition Response Template

This template demonstrates how ops-worker should respond to individual review comments when `per_comment_processing.enabled: true`.

## Example: Comment Applies

```markdown
**Disposition: applies**

**Files affected:**
- `docs/some-file.md` (lines 10-25)
- `modules/ops-worker/README.md` (section: "Per-Comment Processing")

**Resolution:**
This comment addresses an edge case in error handling. Changes have been implemented to capture and document retry logic when API rate limits are encountered.

- Updated module initialization to validate poll intervals before scheduling
- Added validation test case covering the specific scenario mentioned
- Commit: `abc123d` (latest push to this PR)

**Status:** Ready for re-review.

Model used: google-antigravity/claude-opus-4-6-thinking
```

## Example: Comment Does Not Apply

```markdown
**Disposition: does_not_apply**

**Reason:**
This suggestion applies to concurrent execution patterns, but the current implementation is single-threaded by design. The feature scope (PR #123) explicitly excludes multi-threaded handling; that will be addressed in a follow-up issue (#456).

**Reference:** 
- Design decision: [MEMORY.md](https://...) - "Single-threaded v1"
- Related issue: [#456](https://...) - Multi-threading roadmap

**Status:** Comment noted; scope clarification added to PR description.

Model used: google-antigravity/claude-opus-4-6-thinking
```

## Template Fields

Every per-comment response must include:

1. **Disposition Header** (required):
   - `**Disposition: applies**` or `**Disposition: does_not_apply**`

2. **If `applies` (required fields)**:
   - `**Files affected:**` — list each file and line ranges touched
   - `**Resolution:**` — brief narrative of what was changed and why
   - Specific commit SHA (if already committed) or "pending in next push"
   - Any validation/test references

3. **If `does_not_apply` (required fields)**:
   - `**Reason:**` — clear, technical explanation of why the comment doesn't apply to this scope
   - **Reference links** — to relevant docs, related issues, or design decisions if applicable

4. **Status Line** (optional but recommended):
   - `**Status:** Ready for re-review.` or `**Status:** Noted; scope clarified.`

5. **Model Attribution** (required):
   - `Model used: <provider/model>` at the end of the comment

## Thread Resolution

When posting the disposition response:
- If the platform API supports it, mark the thread as resolved after posting
- If not supported, include a note: `Thread marked as resolved (by ops-worker)`
- The worker's output contract must report: `review_threads_resolved` count

## Quality Gate

If `review_policy.per_comment_processing.fail_quality_gate_if_undispositioned: true`:
- The worker MUST process **every** review comment before marking PR as "review handled"
- Any unaddressed comment causes the PR to enter a blocked state
- Blocked PRs are flagged in the worker's output (see OUTPUT-CONTRACT.md)
