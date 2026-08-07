<img width="678" height="452" alt="image" src="https://github.com/user-attachments/assets/d8422091-61cf-4b20-8407-28b45f2aa7b8" />


## Loop Engineering

A collection of reusable, versioned prompts for running human-in-the-loop
agentic sessions with Hermes — phase-gated workflows that a human explicitly
approves at each checkpoint before the agent proceeds.

### Prompts

- `hermes-project-kickoff.md` (`kickoff`) — kicking off a new project or a
  new feature in an existing project.
- `hermes-bug-fix.md` (`bugfix`) — fixing a single reported bug in an
  existing project: reproduce → diagnose & fix approach → test-first build.

### Goal

Give agentic coding sessions a durable, reviewable structure instead of an
unstructured chat: explicit checkpoints, no artifact writes before approval,
and resumable state via versioned front-matter so an interrupted session
picks up from the file, not from chat history. Each prompt versions
independently and is fetchable at a pinned release — see `AGENTS.md` for the
process — so the same prompt can be reused consistently across projects.
