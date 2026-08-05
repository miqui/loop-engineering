# Changelog

All notable changes to the prompts in this repo are documented here.
Versions are tagged in git and published as GitHub Releases.

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
