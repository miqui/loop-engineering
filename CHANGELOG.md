# Changelog

All notable changes to the prompts in this repo are documented here.
Versions are tagged in git and published as GitHub Releases.

## [1.2.1] - 2026-08-05

`hermes-project-kickoff.md`:

- Clarified the Phase 0 test-presence check from v1.2.0: CI config alone is
  no longer accepted as evidence a test runner exists — it must actually
  run locally. Treating CI as its own separate signal is deferred to a
  future version.

## [1.2.0] - 2026-08-05

`hermes-project-kickoff.md`:

- Phase 0 existing-project survey now also checks whether a unit test
  runner is present and wired, and states that finding explicitly.
- Phase 2: if the survey found no unit tests, the plan's first step must
  scope a minimal test scaffold (runner wired + one smoke test) — just
  enough to unblock the Tests field at later build checkpoints, not a
  backfill of coverage. No scaffold step is required if tests already exist.

## [1.1.0] - 2026-08-05

`hermes-project-kickoff.md`:

- Phase 0 now distinguishes greenfield vs. existing-project working mode and
  requires a codebase survey (README, tooling, conventions, related modules)
  before Phase 1 framing when working in an existing project.
- Phase 0 resume detection: on finding an existing design/plan doc, infer the
  last completed checkpoint from status front-matter and checked-off plan
  steps, and state it explicitly before asking whether to resume or restart.
- Plan skeleton's Steps section uses checkboxes as the durable record of
  build progress; Build phase rules now require checking off each step in
  the plan doc once its checkpoint is confirmed, so an interrupted build can
  be resumed from the file instead of chat history.

## [1.0.0] - 2026-08-05

Initial versioned release of `hermes-project-kickoff.md`.

- Phase 0 (setup & conventions), Phase 1 (idea-refine, 3 HITL checkpoints),
  Phase 2 (writing-plans), and Phase 3 (per-step build HITL).
- ADR, design doc, and plan front-matter/skeletons in the appendix.
- Idempotency, traceability, and git-staging rules.
