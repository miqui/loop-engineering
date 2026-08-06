# Changelog

All notable changes to the prompts in this repo are documented here.
Versions are tagged in git and published as GitHub Releases.

## [bugfix v1.0.0] - 2026-08-05

`hermes-bug-fix.md`:

- Initial versioned release. A phase-gated prompt for fixing a single
  reported bug in an existing project: Phase 0 (setup, codebase + test-runner
  survey, resume detection), Phase 1 (reproduce → root cause & blast radius →
  fix approach & scope, 3 HITL checkpoints), Phase 2 (test-first build —
  regression test written and confirmed failing before the fix, full suite
  must stay green, per-step HITL).
- Root-cause step makes no assumption that the target codebase has ADRs,
  changelogs, or any other decision-record convention — uses whatever
  history/evidence is actually available and says so explicitly when the
  originating change can't be confidently determined.
- Build phase treats a pre-existing test broken by the fix as a hard
  blocker: never edited/skipped/deleted to force the suite green; either the
  fix is wrong or updating the test is a deliberate change requiring
  explicit approval.
- Reuses ADR mechanics, idempotency, traceability, git-staging, and
  pre-write-gate rules from `hermes-project-kickoff.md` for consistency
  across prompts in this repo.

This repo's tagging/changelog scheme is now namespaced by prompt key
(`kickoff`, `bugfix`) since prompts version independently — see AGENTS.md.

## [1.3.6] - 2026-08-05

`hermes-project-kickoff.md`:

- Reformatted the Appendix (copy-paste templates): `###` Appendix heading,
  `####` sub-headings for Front-matter template / ADR skeleton / Component
  map skeleton / Plan skeleton, fenced the YAML/markdown snippets as code
  blocks, bulleted the ADR field list and component-map rules, and removed
  stray blank-line spacing. Formatting only, no behavior change.

## [1.3.5] - 2026-08-05

`hermes-project-kickoff.md`:

- Reformatted the Documentation rules section: `###` heading, bulleted
  and bolded each rule label (Front-matter / ADR shape / Idempotency /
  Traceability / Git / Pre-write gate), backtick'd field names, status
  values, and commands. Formatting only, no behavior change.

## [1.3.4] - 2026-08-05

`hermes-project-kickoff.md`:

- Reformatted Phase 3 (build): `###` phase heading, `####` sub-headings
  for Build checkpoint format / Build phase rules / Phase 3 completion,
  bulleted field labels and rules, nested Implementation summary
  sub-items, backtick'd paths and status values. Formatting only, no
  behavior change.

## [1.3.3] - 2026-08-05

`hermes-project-kickoff.md`:

- Reformatted Phase 2 (writing-plans): added a `###` phase heading and
  bolded/bulleted the Artifact label with a backtick'd path. Formatting
  only, no behavior change.

## [1.3.2] - 2026-08-05

`hermes-project-kickoff.md`:

- Reformatted Phase 1 (idea-refine) from flat run-on lines into a `###`
  phase heading, `####` Step → Checkpoint sub-headings, bulleted action
  items, and bolded HITL checkpoint / Artifacts labels. Formatting only,
  no behavior change.

## [1.3.1] - 2026-08-05

`hermes-project-kickoff.md`:

- Reformatted the Phase 0 "Setup steps" block from run-on paragraphs into a
  numbered list with nested sub-bullets (Codebase survey / Unit test check /
  CI signal check / Report). Formatting only, no behavior change.

## [1.3.0] - 2026-08-05

`hermes-project-kickoff.md`:

- Phase 0 existing-project survey now also checks CI signal presence
  (detection only — no plan step is forced by absence, unlike the
  test-runner check). Two-tier detection: first a markdown file
  documenting CI guidance (CONTRIBUTING.md, docs/development.md,
  docs/ci*.md, or a README section); if none exists, infer from CI config
  artifacts (`.github/workflows/`, `.gitlab-ci.yml`, `.circleci/config.yml`,
  `Jenkinsfile`, `azure-pipelines.yml`) and, if applicable, Bazel
  build/test files (`WORKSPACE`, `WORKSPACE.bazel`, `MODULE.bazel`,
  `BUILD.bazel`, `.bazelrc`).
- For multi-language, multi-platform, or monorepo-style projects, CI
  configs are enumerated per language/package/service rather than
  assumed to be a single pipeline; gaps are noted explicitly.

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
