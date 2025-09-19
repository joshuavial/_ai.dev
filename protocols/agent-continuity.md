# Agent Continuity Protocol

## Purpose

This protocol ensures any agent (or coder) can pause work and resume later without losing context. All continuity data lives inside the relevant task folder under `_ai/tasks/<task-slug>/`. No workflow-specific state lives elsewhere.

## Required Task Artefacts

Each active task folder must contain:

```
_ai/tasks/<task-slug>/
├── technical-plan.md   # Objectives, approach, design notes
├── status.md           # Snapshot: progress, blockers, next steps
├── todos.md            # Actionable checklist managed via TodoWrite/TodoRead
├── handoff.md          # (Optional) Detailed resume instructions / delegate notes
├── qa-report.md        # (Created by QA workflow when needed)
├── tdd-evidence/       # Optional supporting evidence
└── artifacts/          # Optional logs, screenshots, exports
```

## Continuity Responsibilities

1. **On Startup**
   - Read `technical-plan.md` for scope.
   - Read `status.md` for current progress, blockers, and active workflow.
   - Review `todos.md` for granular actions.
   - (If present) Read `handoff.md` for open questions, partial work, or delegation notes.

2. **During Work**
   - Update `todos.md` as items move in_progress → completed.
   - Keep `status.md` accurate after every meaningful milestone (tests added, component finished, blocker encountered).
   - Use `handoff.md` when you need to capture nuanced context, investigation notes, or instructions for another collaborator.

3. **Before Pausing or Switching Agents**
   - Confirm `status.md` has:
     - **Workflow** (planning/execution/qa/management)
     - **Updated timestamp**
     - Bullet summary of progress
     - Explicit blockers (or `None`)
     - Top next steps (linked to open todos)
   - Ensure `todos.md` reflects the latest plan of attack.
   - Add/refresh `handoff.md` if there is more to say than fits comfortably in `status.md` (e.g., partial code paths, log excerpts, URLs, hypotheses).

## Handoff Checklist (handoff.md template)

```markdown
# Handoff: <Task Name>

- **Updated**: 2025-01-04 15:12
- **Owner**: @agent-handoff (if relevant)

## Current Focus
- Summarize the exact step you were in the middle of

## What Was Done
- Short bullets describing completed work during this session

## What Remains
- Link to open items in `todos.md`
- Mention any files, branches, or test suites that need attention

## Blockers & Open Questions
- List blockers, data requirements, approvals needed, or hypotheses to verify

## References
- Any logs, design docs, or external resources worth bookmarking
```

## Error & Interruption Handling

If an agent stops unexpectedly (timeout, crash, auth failure):

1. Immediately append a note in `handoff.md` under `Blockers & Open Questions` describing the failure and how to resume.
2. Update `status.md` with the same blocker so future agents see it instantly.
3. If the issue is tooling-related (e.g., GitHub auth), include recovery steps (regenerate token, rerun command) in `handoff.md`.

## Parallel Work Guidance

When multiple agents touch the same task:
- Each agent appends a short entry to `handoff.md` identifying themselves and the slice they handled.
- Use individual sections or headings (e.g., `## Session – code-generator`) to avoid collisions.
- Keep `status.md` authoritative; only one agent should save changes to it per handoff to prevent overwrite conflicts.

## Parent/Orchestrator Expectations

Orchestrators should:
- Check the task folder for `status.md`, `todos.md`, and `handoff.md` before delegating work.
- After delegations complete, roll summary updates back into `status.md`.
- Avoid writing to any legacy state directories; all continuity data belongs in the task folder.

By keeping all workflow continuity data inside the task folder, agents, coders, and LLMs share a single, durable representation of progress with no out-of-band state to reconcile.
