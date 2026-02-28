# Implementation Roadmap

This roadmap translates the strategic vision into safe, incremental releases for `openclaw-adan-core`.

## Principles

1. **Safety-first defaults**: risky automation is disabled by default.
2. **Feature-flagged growth**: new capabilities are opt-in only.
3. **Platform-agnostic onboarding**: no required shell dependency.
4. **Portability**: support global and repo-local installs.
5. **Human governance**: high-risk actions require explicit approval.

---

## v0.2 — Foundation Hardening (current cycle)

### Objectives
- Finalize onboarding and prerequisites.
- Add repo-root and VCS-provider neutrality.
- Keep install path options clear (`global` vs `repo-local`).

### Deliverables
- `ONBOARDING.md` + answers template + checklist
- Feature-flag policy documented and enforced
- Common repository root convention (`~/Projects` by default)

### Exit Criteria
- New users can install in <=10 minutes.
- No autonomous write behavior enabled by default.

---

## v0.3 — Meta-Skill (skill-synthesizer) MVP

### Objectives
- Add an optional skill that scaffolds new skills safely.
- Focus on deterministic outputs (templates, structure, checklists).

### Scope In
- Generate SKILL skeletons and standard folder structure
- Generate docs/checklists from structured input
- Validate required fields and naming conventions

### Scope Out (deferred)
- Unbounded autonomous code generation
- Auto-merge or auto-publish of generated skills

### Feature Flag
- `meta_skill_synthesizer` (default: `false`)

### Exit Criteria
- Synthesizer generates valid, reviewable skill scaffolds
- Human review remains mandatory before adoption

---

## v0.4 — Weekly Refinery Loop (Governed)

### Objectives
- Introduce a weekly learning audit and promotion flow.

### Deliverables
- `.learnings/` structure + triage templates
- Weekly review protocol
- Promotion states: `pending -> validated -> promoted`

### Feature Flag
- `weekly_refinery` (default: `false`)

### Governance
- Promotion into core files requires explicit human approval
- Risky promotions blocked without approval record

---

## Security Gates Across All Versions

- Feature flag required for optional autonomous behavior
- High-risk actions are human-in-the-loop
- No implicit provider/account reconfiguration
- Report-only fallback when uncertainty exists

---

## Suggested Milestone Cadence

- **Week 1:** v0.2 polish and docs completion
- **Week 2:** v0.3 synthesizer MVP
- **Week 3:** v0.4 refinery loop + governance tests
- **Week 4:** stabilization and examples
