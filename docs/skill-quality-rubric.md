# Skill Quality Rubric (v0.3)

Use this rubric to evaluate generated skills before trial or production adoption.

## Scoring Model

- Total score: 100
- Passing score for trial: 75+
- Passing score for production: 85+
- Any critical failure below forces rejection regardless of score

## Categories

### 1) Trigger Clarity (20)
- Description clearly states what the skill does (0-10)
- Description clearly states when to use it (0-10)

### 2) Structural Correctness (20)
- `SKILL.md` exists and frontmatter is valid (0-10)
- Skill name format is lowercase + hyphenated (0-5)
- Folder structure is coherent for intended scope (0-5)

### 3) Safety Boundaries (25)
- No hidden autonomous execution assumptions (0-10)
- Explicit human approval for risky actions (0-10)
- Feature-flag compatibility documented (0-5)

### 4) Operability (20)
- Inputs and outputs are concrete and testable (0-10)
- Instructions are deterministic enough for repeatability (0-10)

### 5) Maintainability (15)
- Scope boundaries documented (in/out) (0-5)
- Minimal, useful templates/references only (0-5)
- Clear next-step extension guidance (0-5)

## Critical Failures (Auto-Reject)

- Missing or invalid `SKILL.md` frontmatter
- Instructions that imply automatic external actions without explicit approval
- Ambiguous trigger description that can cause broad misfires
- Security-sensitive actions with no safety gating

## Decision Outcomes

- **Reject**: score < 75 or any critical failure
- **Trial Approved**: score 75-84 and no critical failures
- **Production Approved**: score >= 85 and no critical failures
