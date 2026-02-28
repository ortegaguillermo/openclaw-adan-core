# REVIEW-CHECKLIST

## Structural Validation
- [ ] `SKILL.md` exists
- [ ] YAML frontmatter includes only `name` and `description`
- [ ] Skill name is lowercase + hyphenated
- [ ] Description clearly states trigger conditions

## Safety Validation
- [ ] No auto-execution assumptions
- [ ] No auto-publish assumptions
- [ ] External actions require explicit human approval
- [ ] Feature-flag expectations are documented

## Quality Validation
- [ ] Capabilities are concrete and testable
- [ ] References/templates are minimal and useful
- [ ] Scope is clear (what is in / out)
- [ ] Output follows `OUTPUT-CONTRACT.md`

## Rubric Scoring (required)
Reference: `docs/skill-quality-rubric.md`
- Total score: ____ / 100
- Critical failures present: yes / no

## Adoption Decision
- [ ] Approved for trial (>=75, no critical failures)
- [ ] Approved for production (>=85, no critical failures)
- [ ] Rejected (with reasons)
