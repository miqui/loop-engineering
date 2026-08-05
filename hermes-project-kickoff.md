---
title: Hermes Project Kickoff
version: 1.3.1
status: stable
date: 2026-08-05
---

Hermes Project Kickoff — idea-refine → writing-plans → docs capture
Reusable kickoff prompt. Two human-in-the-loop design phases, then persist every design decision as markdown under docs/. No code until the plan is approved. Treat me as an experienced collaborator, not a novice.


### Phase 0 — Setup & conventions
  - Operating principles (apply for the entire session)
  - Uncertainty: If you lack sufficient information to complete any step with confidence, surface the gap explicitly and ask before proceeding. Do not infer or invent unstated requirements.
  - Approval: Treat only explicit approval as a green light. If my response at any checkpoint is ambiguous ("ok", "sure", "looks good"), restate what you understood as approved and ask me to confirm before continuing. Do not proceed on implicit or unclear consent.
  - Setup steps
    1. Derive a `<slug>` from the project/idea name (kebab-case, e.g. `api-deprecation-overlay`).
    2. Determine the working mode: new project (greenfield) or a new feature in an existing project. If unclear from my request, ask before proceeding.
    3. If working mode is "existing project," survey the existing codebase before Phase 1 Step 1:
       - **Codebase survey**: read the README/top-level docs, identify language/framework/build tooling, note existing conventions (naming, testing, module boundaries), and skim any modules directly relevant to the requested feature.
       - **Unit test check**: confirm a test runner is present, wired via a script/Makefile/documented command, and actually runs locally. CI config alone is not sufficient signal for this check — do not treat CI presence as evidence tests run locally.
       - **CI signal check** (two-tier, detection only):
         1. Look for a markdown file that documents CI guidance (e.g. `CONTRIBUTING.md`, `docs/development.md`, a `docs/ci*.md`, or a dedicated CI section in the README) and treat it as the primary source of truth.
         2. If no such doc exists, infer CI setup directly by scanning for CI config artifacts (`.github/workflows/`, `.gitlab-ci.yml`, `.circleci/config.yml`, `Jenkinsfile`, `azure-pipelines.yml`, etc.), and — if applicable — Bazel build/test orchestration (`WORKSPACE`, `WORKSPACE.bazel`, `MODULE.bazel`, `BUILD.bazel`, `.bazelrc`), since large or monorepo-style projects often drive CI through Bazel targets rather than a single native CI config.
         - For multi-language, multi-platform, or monorepo-style projects, do not assume a single pipeline covers everything — enumerate CI configs per language/package/service and note any gaps (e.g. a package with no corresponding workflow).
       - **Report**: summarize the codebase, test-runner, and CI findings to me as grounding context before restating the problem in Step 1. State explicitly whether unit tests and CI are present or absent — an absence carries forward into the Phase 2 plan (see below). Do not invent constraints the survey didn't surface, and do not skip the survey to save time.
    4. Before creating any folder, check whether `docs/design/<slug>-design.md` already exists. If it does, read it along with any `docs/plans/<slug>-plan.md`:
       - Infer the last completed checkpoint from their status front-matter and, for plans, which Steps are checked off.
       - State that inferred resume point to me explicitly — phase, checkpoint, and last completed build step if applicable — and ask whether to resume from there or start fresh under a new slug.
       - Do not proceed until I choose. Never assume a resume point silently; an incorrect guess here compounds through every later phase.
    5. Ensure the docs tree exists; create any missing folders:
       - `docs/design/` — refined idea / design briefs
       - `docs/decisions/` — ADRs (one file per discrete decision)
       - `docs/plans/` — pre-build implementation plans
  - Do not write any artifact to disk until its phase's final HITL checkpoint is approved.


Phase 1 — idea-refine (human-in-the-loop)
Work through the three steps below in sequence. Each checkpoint is embedded at the point where it fires — stop and wait for my input before continuing to the next step. Do not batch steps or skip ahead. If I request changes at any checkpoint, revise and re-present the full output for that checkpoint before proceeding. Do not advance to the next step until I explicitly confirm the revised version.
Step 1 → Checkpoint 1: Problem framing
Restate the problem clearly.
Identify the target user / use case.
Surface assumptions explicitly.

HITL checkpoint 1 — stop here. Present the problem restatement, target user, and assumptions. Wait for my input before proceeding to Step 2. Review focus: is the problem statement accurate? are all key assumptions surfaced and correctly framed?
Step 2 → Checkpoint 2: Options & tradeoffs
Propose 2–4 plausible directions, not a huge brainstorm.
Compare tradeoffs honestly.

HITL checkpoint 2 — stop here. Present the options and tradeoff comparison. Wait for my input before proceeding to Step 3. Review focus: are all plausible directions represented? are the tradeoffs honestly stated and not loaded toward a preferred answer?
Step 3 → Checkpoint 3: Direction, component map & scope
Recommend one direction.
Sketch the component map for the recommended direction: identify top-level modules, their responsibilities, and the contracts between them.
Define MVP scope.
Define an explicit not-doing list.
Call out edge cases, failure modes, and risks early.
Identify the decisions where I should weigh in before planning.

HITL checkpoint 3 — stop here. Present the recommended direction, component map (with all TBDs enumerated and a proposed resolution for each), MVP scope, not-doing list, edge cases, and weigh-in decisions together. Wait for my explicit approval of every item before writing any artifact. Review focus: does the direction align with the approved problem framing? does the component map cover all necessary modules with concrete contracts? is the MVP scope appropriately bounded?
Artifacts (write only after checkpoint 3 is approved):
docs/design/<slug>-design.md — capture every section above: problem, target user, assumptions, the 2–4 options with honest tradeoffs, the recommended direction, component map, MVP scope, not-doing list, and edge cases / failure modes / risks.
For each discrete decision I weighed in on, write an ADR: docs/decisions/NNNN-<short-title>.md (zero-padded, incrementing, never reused).


Phase 2 — writing-plans (no code yet)
Before beginning plan authoring, summarize the approved direction and component map in three sentences and ask me to confirm that scope has not shifted since checkpoint 3. Do not proceed until I confirm. If I indicate scope has shifted, stop immediately, open a new ADR candidate capturing the scope change, and return to Phase 1 checkpoint 3 before proceeding.

Turn the validated direction into a pre-build implementation plan following the plan skeleton in the appendix. Do not omit any section; do not add sections not in the skeleton without flagging them to me first.

If the Phase 0 survey found the existing project has no unit test runner wired, the plan's first step must scope a minimal test scaffold — wire the test runner and add one passing smoke test, nothing more. Its only job is to unblock the Tests field at later build checkpoints; do not use it to backfill coverage for existing code. If unit tests are already present, no scaffold step is needed.
Artifact:
docs/plans/<slug>-plan.md — capture the full plan above, linked back to the design doc and the ADRs that justify it.

Do not code yet. Before presenting the plan for review, enumerate every remaining TBD entry in the Key decisions section. For each, provide a proposed resolution or flag it explicitly as a pre-build blocker requiring my decision. A plan may be presented with open TBDs only if each is accompanied by a concrete proposal; a plan with silent or unaddressed TBDs must not be presented or handed to a build agent.


Phase 3 — build (per-step HITL)
Do not begin Phase 3 until the plan is approved and all TBD entries in the Key decisions section are resolved and replaced with ADR references.

Work through plan steps in the order defined in the plan. After implementing each step, stop and present a build checkpoint before beginning the next step. Do not proceed until I confirm. If I request changes at any build checkpoint, revise and re-present before continuing. Do not advance to the next step until I explicitly confirm.
Build checkpoint format (after every step)
Present the following after each step:

Build checkpoint N — <step title>

Files changed: Every file touched, its change type (new / modify / delete), and the actual interface delta vs. the interface delta in the plan's file inventory. Flag any divergence explicitly.

Plan fidelity: Did this step implement exactly what the plan specified? If it deviated — different files, different interface, different approach — describe the deviation and open an ADR candidate for it.

TBDs surfaced: Any new unknowns discovered during implementation. Each is an immediate blocker — stop, open an ADR candidate, and await my decision before continuing. Do not work around a TBD silently.

Tests: What was tested and whether it passes. A step is not complete until its acceptance criteria from the plan are met.

Next: <step N+1 title, or "implementation complete" if this was the last step>

Review focus: do the changed files match the planned inventory? does the interface delta match what the plan specified? are acceptance criteria met for this step?
Build phase rules
Do not begin step N+1 until I explicitly confirm step N is complete.
Once step N is confirmed complete, check it off in docs/plans/<slug>-plan.md's Steps section before starting step N+1. This file is the source of truth for resuming an interrupted build — do not rely on chat history to know which steps are done.
Never work around a problem silently — surface every unexpected constraint, missing dependency, or wrong assumption before continuing.
If implementation reveals a design flaw (wrong component boundary, missing contract, incorrect interface assumption), stop immediately and ask whether to resolve it inline with a new ADR or return to Phase 2. If returning to Phase 2 for a second time on the same flaw, escalate to Phase 1 instead — treat the flaw as a new constraint on the direction decision and work through Steps 1–3 before rewriting the plan.
If three or more steps have deviated from the plan — where a deviation means different files touched, different interface exposed, or different approach taken than the plan specified — stop and propose a plan revision before continuing.
For each ADR candidate opened during Phase 3, present it for my approval at the next build checkpoint. Once I approve, assign it the next available NNNN number, update its status to accepted, write it to docs/decisions/NNNN-<short-title>.md, and add a link in the plan's Key decisions section.
Phase 3 completion
When the final step is confirmed complete, update docs/plans/<slug>-plan.md using the idempotency rule (append, do not overwrite):

Update front-matter status to implemented and date to today's date. If any acceptance criteria are outstanding, do not set status to implemented — leave it as accepted, flag every outstanding item explicitly, and do not stage until I acknowledge them.
Append an ## Implementation summary section containing:
Date completed
Steps completed (N of N)
Deviations from plan: each deviation and the ADR number that covers it, or "none"
ADRs opened in Phase 3: all, with final accepted NNNN references
Acceptance criteria: confirm all Tests / validation criteria from the plan passed, or note any outstanding items


Documentation rules (apply to every artifact)
Front-matter on each file: title, status (draft | proposed | accepted | superseded | implemented), date, owner, slug, related (links to sibling docs / ADRs). Use implemented on plan docs only, once Phase 3 completion is confirmed.
ADR shape: Context → Decision → Status → Consequences → Alternatives considered.
Idempotency: if a target file already exists, do not overwrite silently — append a dated revision/changelog entry and update status + date. Never destroy a prior decision; supersede it and link the replacement. When appending a revision, open with a two-sentence summary of what changed and why before the revised content.
Traceability: design doc links its ADRs; plan links the design doc; each ADR back-links to the plan step it drives.
Git: Before any git add, verify git rev-parse --is-inside-work-tree returns true. If the check fails, skip all git operations, note that staging was skipped, and remind me to initialize a repo. If the check passes, stage new/changed docs and stop. Propose a conventional commit message (docs(<slug>): …) but do not commit or push unless I say so.
Pre-write gate: confirm the exact target paths with me before the first write of each phase.


Appendix — copy-paste templates
Front-matter
---

title: <human-readable title>

status: draft        # draft | proposed | accepted | superseded | implemented (plan docs only)

date: <YYYY-MM-DD>

owner: miqui

slug: <slug>

related: []          # paths to sibling design / ADR / plan files

---
ADR skeleton (docs/decisions/NNNN-<short-title>.md)
---

title: <decision title>

status: proposed

date: <YYYY-MM-DD>

owner: miqui

slug: <slug>

related: []

---

## Context

What forces are at play; what made this a decision worth recording.

## Decision

The choice, stated in one or two sentences.

## Status

proposed | accepted | superseded by NNNN

## Consequences

What becomes easier, harder, or constrained as a result.

## Alternatives considered

The options that were weighed and why they lost.
Component map skeleton (embedded in docs/design/<slug>-design.md)
## Component map

| Module | Responsibility | Contracts / interfaces |

|--------|---------------|------------------------|

| `<module-name>` | <one-line description of what it owns> | <what it exposes or consumes: API surface, event, queue, schema, etc.> |

Rules for the component map:

One row per top-level module — do not list sub-components here.
"Contracts / interfaces" must be concrete: a REST endpoint, a Go interface, a Kafka topic name, a database schema, a shared type — not a vague description like "communicates with."
If a contract does not yet have a name, flag it as TBD — decision needed so it surfaces as a checkpoint item, not a silent assumption.
Every TBD — decision needed entry automatically becomes a named agenda item at checkpoint 3. Hermes must enumerate every TBD, propose a concrete resolution for each, and receive explicit approval on each one before the direction is finalized. Unresolved TBDs block artifact writing.
The component map is presented at HITL checkpoint 3 alongside the direction recommendation. Both must be confirmed before artifact writing begins.

### Plan skeleton (`docs/plans/<slug>-plan.md`)

```yaml

---

title: <human-readable title>

status: draft

date: <YYYY-MM-DD>

owner: miqui

slug: <slug>

related:

  - docs/design/<slug>-design.md

  - docs/decisions/NNNN-<short-title>.md   # one line per driving ADR

---

## Overview

One paragraph: what gets built, why, and which approved direction this

implements. Link to the design doc and every ADR that governs a plan decision.

## Technical constraints

- Runtime / language: <e.g. Go 1.22, Python 3.12, Node 22 — be specific>

- Key dependencies: <package@version, external API URL, schema location>

- Banned approaches: <anything ruled out in design or by an ADR>

## Steps

Ordered implementation steps. Each step names the module(s) it touches and

cites any governing ADR. Checkboxes are the source of truth for resuming an

interrupted build — check a step off only once its build checkpoint is confirmed.

1. [ ] **<Step title>** — `<module>` — <one-sentence description>

   - ADR: [NNNN](../decisions/NNNN-short.md) or `TBD`

## File inventory

| File path | Module | Change type | Interface delta |

|-----------|--------|-------------|-----------------|

| `<path/to/file>` | `<module>` | new / modify / delete | <before→after signature — e.g. `+ POST /items → {id, name}` or `+ Fetch(ctx, id string) (Item, error)` or `UserSchema: + role field` — not "adds new method"> |

## Key decisions

Decisions that must be confirmed or made during implementation. Link to an

ADR if already recorded; mark `TBD` if still open (a TBD here is a build

blocker — resolve before coding begins).

- [ ] <Decision statement> → [ADR-NNNN](../decisions/NNNN-short.md) or `TBD`

> **TBD rule:** every `TBD` entry must be presented with a proposed

> resolution at plan review and receive explicit approval before any build

> agent begins work. Convert approved resolutions to ADR references before

> handing this plan to a build agent.

## Edge cases

- **<scenario>**: <how it is handled or why it is explicitly deferred>

## Tests / validation

- **Unit:** <what gets unit-tested and at what threshold>

- **Integration:** <what gets integration-tested>

- **Acceptance:** <observable definition of done — behaviour, not coverage>

## Risk register

| Risk | Likelihood | Impact | Mitigation |

|------|-----------|--------|------------|

| <risk description> | low / med / high | low / med / high | <concrete mitigation step> |

> **Mitigation rule:** name a specific mechanism — a retry limit, a circuit

> breaker, a validation schema, a feature flag, an alerting threshold.

> "Monitor carefully," "test thoroughly," and "handle gracefully" are not

> mitigations.

