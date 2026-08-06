# AGENTS.md

Guidance for any agent (human or AI) making changes in this repo.

## What this repo is

A collection of reusable, versioned prompts for running human-in-the-loop
agentic sessions with Hermes:

- `hermes-project-kickoff.md` (`kickoff`) — a phase-gated prompt for kicking
  off a new project or a new feature in an existing project.
- `hermes-bug-fix.md` (`bugfix`) — a phase-gated prompt for fixing a single
  reported bug in an existing project: reproduce → diagnose & fix approach →
  test-first build.

## Versioning a prompt file

Each prompt file carries its own version in YAML front-matter
(`title`, `version`, `status`, `date`), independent of the other prompts in
this repo. Bump it whenever the *behavior* of the prompt changes —
wording-only cleanups that don't change what Hermes does are not required
to bump.

Because multiple prompts version independently, tags and changelog entries
are namespaced by a short prompt key (`kickoff`, `bugfix`, ...) so a bare
version number is never ambiguous about which file it belongs to.

Go-forward process for a change to a prompt file:

1. Edit the prompt file, then bump its front-matter `version` (semver) and
   `date`.
   - Patch (`x.y.Z`): typo/formatting fixes, no behavior change.
   - Minor (`x.Y.0`): new steps, new rules, new sections — additive, backward
     compatible with sessions already in flight.
   - Major (`X.0.0`): restructures phases/checkpoints in a way that would
     break resuming a session started under the previous version.
2. Add a `## [<prompt-key> vx.y.z] - YYYY-MM-DD` entry to `CHANGELOG.md`,
   above the previous entry, describing what changed and why (not just
   what). `CHANGELOG.md` is shared across all prompts in this repo.
3. Commit the prompt file + changelog together.
4. Tag the commit `<prompt-key>-vX.Y.Z` and push the tag.
5. `gh release create <prompt-key>-vX.Y.Z --notes-file <scoped-notes>` with
   release notes scoped to just that version's changelog entry.

A pinned version is fetchable at:
`raw.githubusercontent.com/miqui/loop-engineering/<prompt-key>-vX.Y.Z/<file>.md`

Note: releases tagged bare `vX.Y.Z` (no prompt-key prefix, up to `v1.3.6`)
predate this scheme and refer to `hermes-project-kickoff.md`. They are left
as-is; the next kickoff release picks up the `kickoff-` prefix.

## Editing conventions for prompt files

These prompts are consumed by an LLM, not compiled — keep changes legible
to both a human skimming the file and Hermes executing it:

- Preserve the existing HITL discipline: explicit checkpoints, "ambiguous
  response ≠ approval," no artifact writes before the relevant checkpoint is
  approved. Don't loosen these without calling it out in the changelog as a
  behavior change.
- Keep front-matter/skeleton templates in the appendix in sync with any rule
  that references them (e.g. a new "Steps" convention must be reflected in
  both the rule text and the skeleton itself).
- Don't add new artifact types, folders, or phases without updating the
  Documentation rules / traceability section — undocumented artifacts break
  the idempotency and resume-detection rules that depend on predictable
  front-matter.

## Do not

- Do not tag/release a version whose front-matter version doesn't match the
  tag.
- Do not silently rewrite a past CHANGELOG entry — append a correction
  instead, consistent with the idempotency rule the prompts themselves use.
