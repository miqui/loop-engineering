---
title: Hermes Bug Fix
version: 2.0.0
status: stable
date: 2026-08-06
---

Hermes Bug Fix — reproduce → diagnose & fix approach → test-first build
Reusable kickoff prompt for fixing a single reported bug in an existing project. One human-in-the-loop diagnosis phase, then a test-first build phase. No fix code until the root cause and approach are approved. Phase 2 autonomy is scoped by how confident the approved diagnosis is, not fixed by rule: a well-specified fix runs to completion and only stops on a defined exception; an uncertain one keeps per-step gates. Treat me as an experienced collaborator, not a novice.


### Phase 0 — Setup & conventions
- Operating principles (apply for the entire session)
  - **Uncertainty**: If you lack sufficient information to complete any step with confidence, surface the gap explicitly and ask before proceeding. Do not infer or invent unstated requirements — especially root cause. A guessed root cause presented as confirmed is worse than an explicit "I'm not sure yet."
  - **Approval**: Treat only explicit approval as a green light. If my response at any checkpoint is ambiguous ("ok", "sure", "looks good"), restate what you understood as approved and ask me to confirm before continuing. Do not proceed on implicit or unclear consent.
- Setup steps
  1. Derive a `<slug>` from the bug report (kebab-case, e.g. `login-500-on-empty-email`).
  2. This loop assumes an existing project — there is no greenfield case for a bug fix. Survey the codebase before Phase 1 Step 1:
     - **Codebase survey**: read the README/top-level docs, identify language/framework/build tooling, and skim the module(s) implicated by the bug report.
     - **Unit test check**: confirm a test runner is present, wired via a script/Makefile/documented command, and actually runs locally. If none is wired, say so explicitly — Phase 1 Step 3 must still scope a minimal regression test, and this finding determines whether that also means standing up the runner itself.
     - **Report**: summarize the codebase and test-runner findings to me as grounding context before restating the bug in Step 1. Do not invent constraints the survey didn't surface, and do not skip the survey to save time.
  3. Before creating any folder, check whether `docs/bugs/<slug>-bugfix.md` already exists. If it does, read it:
     - Infer the last completed checkpoint from its status front-matter and which Fix steps are checked off.
     - If status is `blocked`, read the `## Blocker` section and state what fired, at which step, and the file state it left behind — before asking anything else.
     - State that inferred resume point to me explicitly — checkpoint and last completed build step if applicable — and ask whether to resume from there (resolving the blocker first, if any) or start fresh under a new slug.
     - Do not proceed until I choose. Never assume a resume point silently.
  4. Ensure the docs tree exists; create any missing folders:
     - `docs/bugs/` — one file per bug: repro, root cause, fix approach, resolution
     - `docs/decisions/` — ADRs, only if the fix requires a structural/architectural decision
- Do not write any artifact to disk until Phase 1's final HITL checkpoint is approved.


### Phase 1 — reproduce & diagnose (human-in-the-loop)
Work through the three steps below in sequence. Each checkpoint is embedded at the point where it fires — stop and wait for my input before continuing to the next step. Do not batch steps or skip ahead. If I request changes at any checkpoint, revise and re-present the full output for that checkpoint before proceeding. Do not advance to the next step until I explicitly confirm the revised version.

#### Step 1 → Checkpoint 1: Reproduction
- Restate the reported symptom in your own words.
- Establish reliable reproduction steps — manual or, if a test runner is available, an automated repro.
- Note the affected environment/version/conditions (e.g. only on a specific input, platform, or data state).

**HITL checkpoint 1** — stop here. Present the symptom restatement, reproduction steps, and affected conditions. Wait for my input before proceeding to Step 2. Review focus: does this match what I actually observed? did you reproduce the real bug, or a plausible-looking but different one?

#### Step 2 → Checkpoint 2: Root cause
- Trace the symptom to its root cause — the specific code path or logic error responsible, not just where the error surfaces.
- Classify it: regression (prior working behavior existed) or pre-existing defect. Use whatever evidence the codebase actually offers — `git blame`/`log`/bisect, code comments, tests — but do not assume any particular convention (ADRs, changelogs, linked tickets) exists; this loop may run against any codebase, most of which won't share this repo's own documentation habits. If the classification or the originating change can't be confidently determined from what's available, say so explicitly rather than guessing or inferring a plausible-sounding history.
- Define blast radius: other call sites or places in the codebase with the same faulty pattern, even if not yet reported as broken.

**HITL checkpoint 2** — stop here. Present the root cause, regression classification, and blast radius. Wait for my input before proceeding to Step 3. Review focus: is this the actual root cause or a symptom one layer removed? is the blast radius realistic, not padded or understated?

#### Step 3 → Checkpoint 3: Fix approach & scope
- Recommend a fix approach. Default to one recommendation with reasoning — bug fixes usually have one correct fix, not a menu of options. If genuinely competing approaches exist (e.g. a narrow patch vs. a deeper structural fix), present them with tradeoffs instead of picking silently.
- Define the regression test that will prove the fix works: what it asserts, and confirmation it currently fails against the unfixed code.
- Define an explicit not-doing list — unrelated cleanup, refactors, or adjacent fixes that are out of scope for this change, even if noticed along the way.
- Call out any risk of side effects the fix could introduce.
- List the ordered fix steps (usually short: add failing regression test → implement fix → verify + check blast-radius sites).
- Propose an **autonomy tier** for Phase 2: `exception-based` (run the fix steps to completion, one consolidated checkpoint at the end, stopping early only if an exception trigger fires) or `per-step` (a build checkpoint after every step, as before). Derive the proposal from what Step 2/3 actually found — don't default to optimism:
  - Root cause confidently determined, single blast-radius site, no competing approaches presented, no ADR required → `exception-based` is defensible.
  - Regression classification or originating change couldn't be confidently determined, multiple blast-radius sites, competing approaches had to be presented, or an ADR is required → stay `per-step`.
  - If signals are mixed, default to `per-step` — a wrong autonomy call costs more than a redundant checkpoint.
- State the **exception triggers** that stop Phase 2 immediately regardless of tier (these are fixed, not proposed): a new TBD/unknown surfaces, the implementation deviates from this approved approach, a pre-existing test breaks because of the fix, or the fix doesn't resolve the reproduction case.

**HITL checkpoint 3** — stop here. Present the fix approach, regression test definition, scope, risks, fix steps, and proposed autonomy tier with justification together. Wait for my explicit approval of every item before writing any artifact. Review focus: does the approach address the confirmed root cause, not just the symptom? is the regression test specific enough to fail without the fix and pass with it? is scope tight? is the proposed autonomy tier justified by what Phase 1 actually found, or is it optimistic about an uncertain diagnosis?

**Artifacts** (write only after checkpoint 3 is approved):
- `docs/bugs/<slug>-bugfix.md` — capture every section above: symptom, repro steps, environment, root cause, regression classification, blast radius, fix approach, regression test definition, not-doing list, risks, autonomy tier with justification, and fix steps. This doc is the full spec Phase 2 executes against — write it as if the agent running Phase 2 has no memory of this conversation, because it might not: a resumed session or a separate execution run only ever sees the file.
- If the fix requires a structural/architectural decision, write an ADR: `docs/decisions/NNNN-<short-title>.md` (zero-padded, incrementing, never reused).


### Phase 2 — build (test-first, tier-scoped autonomy)
Do not begin Phase 2 until Phase 1 checkpoint 3 is approved, including the autonomy tier.

Work through the fix steps in the order defined in `docs/bugs/<slug>-bugfix.md`. The regression test step always comes first and must be confirmed failing against the unfixed code before the fix step begins — this proves the test actually catches the bug rather than passing trivially. Mechanical verification (does the test fail pre-fix, pass post-fix, does the full suite stay green) is something you confirm yourself and log — it does not require stopping for approval on its own. Only an exception trigger or a per-step tier requires stopping.

#### Exception triggers — stop immediately, regardless of tier
- A new TBD/unknown surfaces during implementation.
- The implementation deviates from the approved fix approach.
- A pre-existing test breaks because of this fix.
- The fix doesn't resolve the reproduction case.

When a trigger fires: stop work immediately. Do not try alternate patches blindly, do not edit/skip/delete a failing pre-existing test to force the suite green, do not fold in unrelated fixes to route around the problem. Update the bug doc's front-matter `status` to `blocked` and append a `## Blocker` section: which trigger fired, at which step, and the current state of files touched. Treat this as a durable handoff, not a live wait — the run ends there for a human (or a resumed session, possibly a different one — see Phase 0 step 3) to pick up. Two triggers have a specific resolution path once picked back up: a fix that doesn't resolve repro or a deviation means returning to Phase 1 Step 2 to re-examine the root cause; a pre-existing test broken by the fix means either the fix is wrong (same path) or the test itself encoded the buggy behavior, in which case updating it is a deliberate, in-scope change requiring explicit approval before the test is touched.

#### If tier = `per-step`
After each step, stop and present a build checkpoint before beginning the next step. Do not proceed until I confirm. Do not begin step N+1 until I explicitly confirm step N is complete.

**Build checkpoint N — <step title>**
- **Files changed**: Every file touched and its change type (new / modify / delete).
- **Fix fidelity**: Did this step implement exactly what the approved approach specified?
- **Tests**: For the regression-test step, confirm it fails against the unfixed code. For the fix step, confirm the regression test now passes and the full local test suite still passes exactly as before.
- **Next**: <step N+1 title, or "fix complete" if this was the last step>

#### If tier = `exception-based`
Run the fix steps to completion without stopping between them, self-verifying and logging each step's mechanical checks as you go. Present one consolidated build checkpoint once the fix is complete (or once a trigger fires, whichever comes first):

**Build checkpoint — fix complete**
- **Files changed**: Every file touched across all steps and its change type.
- **Fix fidelity**: Did the implementation match the approved approach across every step? (If yes throughout, say so — this is what earned the consolidated checkpoint.)
- **Tests**: Regression test confirmed failing pre-fix, passing post-fix; full local test suite still passes exactly as before.
- **Next**: "fix complete"

Review focus (both tiers): does the regression test actually exercise the reported symptom? does the fix resolve it without touching anything outside the approved scope? if `exception-based`, did anything happen mid-build that should have fired a trigger but didn't?

#### Build phase rules
- Once a step (`per-step`) or the whole fix (`exception-based`) is confirmed complete, check it off in `docs/bugs/<slug>-bugfix.md`'s Fix steps section — this file is the source of truth for resuming an interrupted fix.
- No unrelated changes: if you notice adjacent cleanup, refactors, or other bugs while fixing this one, do not fold them in. Flag them to me as follow-up candidates and leave them out of this change.
- Never work around a problem silently — surface every unexpected constraint or wrong assumption via a trigger rather than absorbing it into the fix.

#### Phase 2 completion
When the fix is confirmed complete, update `docs/bugs/<slug>-bugfix.md` using the idempotency rule (append, do not overwrite):
- Update front-matter status to `resolved` and date to today's date.
- Append a `## Resolution summary` section containing:
  - Date resolved
  - Regression test added (path)
  - Root cause (one-sentence recap)
  - Fix commit(s)
  - Blast-radius sites checked, and outcome for each
  - Follow-up items flagged but intentionally not done in this change


### Documentation rules (apply to every artifact)
- **Front-matter** on each file: `title`, `status` (`draft` | `proposed` | `accepted` | `superseded` | `resolved` | `blocked`), `date`, `owner`, `slug`, `related` (links to sibling docs / ADRs). Use `resolved` on bug docs only, once Phase 2 completion is confirmed. Use `blocked` on bug docs only, when a Phase 2 exception trigger fires; clear it back to `draft` once the blocker is resolved and work resumes.
- **ADR shape**: Context → Decision → Status → Consequences → Alternatives considered.
- **Idempotency**: if a target file already exists, do not overwrite silently — append a dated revision/changelog entry and update status + date. Never destroy a prior decision; supersede it and link the replacement. When appending a revision, open with a two-sentence summary of what changed and why before the revised content.
- **Traceability**: the bug doc back-links any ADR it spawned; the ADR back-links the bug doc that drove it.
- **Git**: Before any `git add`, verify `git rev-parse --is-inside-work-tree` returns true. If the check fails, skip all git operations, note that staging was skipped, and remind me to initialize a repo. If the check passes, stage new/changed docs and stop. Propose a conventional commit message (`fix(<slug>): …`) but do not commit or push unless I say so.
- **Pre-write gate**: confirm the exact target paths with me before the first write of each phase.


### Appendix — copy-paste templates

#### Front-matter template

```yaml
---
title: <human-readable title>
status: draft        # draft | proposed | accepted | superseded | resolved | blocked (resolved/blocked: bug docs only)
date: <YYYY-MM-DD>
owner: miqui
slug: <slug>
related: []          # paths to sibling bug docs / ADRs
---
```

#### ADR skeleton (`docs/decisions/NNNN-<short-title>.md`)

```yaml
---
title: <decision title>
status: proposed
date: <YYYY-MM-DD>
owner: miqui
slug: <slug>
related: []
---
```

- **Context**: what forces are at play; what made this a decision worth recording.
- **Decision**: the choice, stated in one or two sentences.
- **Status**: `proposed` | `accepted` | `superseded by NNNN`
- **Consequences**: what becomes easier, harder, or constrained as a result.
- **Alternatives considered**: the options that were weighed and why they lost.

#### Bug doc skeleton (`docs/bugs/<slug>-bugfix.md`)

```yaml
---
title: <human-readable bug title>
status: draft
date: <YYYY-MM-DD>
owner: miqui
slug: <slug>
related:
  - docs/decisions/NNNN-<short-title>.md   # only if a structural decision was needed
---

## Symptom
What was reported, in the reporter's own terms.

## Reproduction steps
Ordered, concrete steps to trigger the bug. Note whether they're manual or
automated (path to the automated repro if one exists).

## Environment
Version/platform/data conditions the bug is confirmed under; note if it's
conditional (only on certain inputs, only in certain environments).

## Root cause
The specific code path or logic error responsible — not just where the
error surfaces.

## Regression classification
`regression` (prior working behavior existed — name the change if found) or
`pre-existing defect`.

## Blast radius
Other call sites or places in the codebase with the same faulty pattern,
even if not yet reported as broken.

## Fix approach
The chosen approach and why. If competing approaches were considered, name
them and why this one won.

## Regression test
What it asserts, and confirmation it fails against the unfixed code.

## Scope / not-doing
Anything explicitly out of scope for this change — cleanup, refactors, or
adjacent bugs noticed along the way.

## Risks
Side effects the fix could introduce.

## Autonomy tier
`exception-based` or `per-step`, and the justification — what Phase 1 found
that supports running Phase 2 to completion unattended, or what's
uncertain enough to keep per-step gates. Written for an execution run with
no memory of this conversation; don't rely on tone or context to imply
confidence — state it.

Exception triggers (fixed, apply regardless of tier): a new TBD/unknown
surfaces, the implementation deviates from this approved approach, a
pre-existing test breaks because of the fix, or the fix doesn't resolve
the reproduction case. Any of these flips `status` to `blocked` and halts
the build — see the Blocker template below.

## Fix steps
Ordered implementation steps. Checkboxes are the source of truth for
resuming an interrupted fix — check a step off only once its build
checkpoint is confirmed.

1. [ ] **Add failing regression test** — `<module>` — confirms repro, fails pre-fix
2. [ ] **Implement fix** — `<module>` — <one-sentence description>
3. [ ] **Verify blast radius** — confirm no regressions at other identified sites

<!-- Appended if an exception trigger fires (status → blocked):
## Blocker
- Trigger fired:
- At step:
- File state:
- Resolution path: <return to Phase 1 Step 2 | test update needs approval | other>
-->

<!-- Appended on Phase 2 completion:
## Resolution summary
- Date resolved:
- Regression test added:
- Root cause (recap):
- Fix commit(s):
- Blast-radius sites checked:
- Follow-up items flagged but not done:
-->
```
