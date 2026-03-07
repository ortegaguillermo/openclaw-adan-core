# Review Comment Disposition Response Template

When `review_policy.per_comment_processing.enabled: true`, respond to each PR review comment with an explicit disposition in its own thread.

## Example: "applies" Disposition

```markdown
### Disposition: applies

**Comment:** Add validation for empty repository names before proceeding to clone step.

**Change Summary:**
- File: `src/clone-handler.ts`
- Lines: 45-52
- Change: Added `if (!repoName) throw new Error("Repo name is required")` before clone initialization
- Commit: a7f3e9d (latest on this branch)

**Resolution:** Repository name validation is now enforced. Empty names will raise a clear error message.
```

## Example: "does_not_apply" Disposition

```markdown
### Disposition: does_not_apply

**Comment:** Refactor this into a separate microservice to improve scalability.

**Reason:**
Current architecture is sufficient for the stated project scope. The design decision to keep clone handling in-process was documented in [MEMORY.md](<link-to-MEMORY.md-in-this-repo>) (section: "Architecture Constraints v1"). 

If multi-service architecture becomes a requirement, see related issue [#456](<link-to-issue-456-or-related-ticket>) for the multi-threading roadmap.

**Resolution:** Not applicable for this phase. Documented for future consideration.
```

## Required Fields (per disposition)

**For "applies" responses:**
- File(s) affected (with line numbers if possible)
- Brief description of the change
- Commit SHA or "pending in next commit" note
- Confirmation that the change resolves the comment

**For "does_not_apply" responses:**
- Clear technical reasoning why it doesn't apply to this PR
- Reference to relevant design decisions or constraints (if applicable)
- Link to related issues/docs when helpful for future context

## Thread Resolution

If `review_policy.per_comment_processing.resolve_threads_when_possible: true`:
- After posting your disposition response, if the GitHub API supports it, mark the thread as resolved
- If the API doesn't support resolution, the disposition response counts as addressing the comment for quality gate purposes
