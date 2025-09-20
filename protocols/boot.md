# LLM Boot Sequence Protocol

Use this sequence whenever a session starts or context is reset. Follow the steps in order and keep the workflow scope tight.

## Required Sequence

1. **Identify runtime adapter**
   - Detect the active provider
   - Read `_ai.dev/adapters/<adapter>/README.md` and, if useful, `agents.md` for shortcuts.

2. **Load core instructions**
   - Read `_ai.dev/core-instructions.md`.
   - Note critical directives (no duplicate files, obey workflow phases, update task artefacts).

3. **Confirm workflow phase**
   - Use user guidance or repository context to identify the active workflow.
   - If the workflow is unclear, ask the user before proceeding.
   - Note the confirmed workflow so it can be recorded in `status.md` once the task is opened.

4. **Load workflow instructions**
   - Read `_ai.dev/workflows/<phase>.md`.
   - Load any referenced protocols (e.g., `_ai.dev/protocols/tdd.md`) before inspecting tasks.

5. **Determine task handling**
   - If the user explicitly asks you to "just do it" or the scope is a quick fix, continue without selecting/creating a task.
   - If the work requires ongoing tracking or the user identifies a task, plan to use/create the appropriate task folder.
   - When unsure whether to operate ad hoc or through a task, ask the user to clarify before moving on.

6. **Gather state references**
   - Skip this step entirely when step 5 confirmed ad hoc work with no task.
   - *Delivery workflows (planning / execution / qa):* Read `_ai/tasks.md` only. Do **not** inspect individual task folders yet.
   - *Management workflow:* Read `_ai.dev/tasks.md` and `_ai.dev/CHANGELOG.md`. Skip `_ai.dev/tasks/<slug>/` folders until a task is confirmed.
   - Use these references to identify candidate tasks or outstanding work.

7. **Select active task**
   - If step 5 determined no task is required, skip this step (and step 8) and proceed to step 9.
   - Zero candidates → ask whether to create a task, continue without one, or supply a path.
   - One candidate → confirm with the user.
   - Multiple candidates → present a numbered list and let the user choose.
   - Ensure `status.md` and `todos.md` exist; create minimal placeholders if missing.

8. **Establish state awareness**
   - Skip this step when operating without a task per step 5.
   - After a task is confirmed, read its artefacts (`technical-plan.md`, `status.md`, `todos.md`, optional `handoff.md`, `qa-report.md`).
   - Run `git status -sb` and `git diff --stat` (followed by targeted `git diff` as needed) to understand any pre-existing modifications before making changes.
   - When resuming or inheriting in-flight work, capture the current branch (`git rev-parse --abbrev-ref HEAD`) and compare against `main` (e.g., `git diff main...HEAD --stat`) to gauge outstanding scope.
   - Capture progress, blockers, and next steps; record the confirmed workflow phase and update `status.md` / `todos.md` when stale.

9. **Confirm understanding with the user**
   - Summarize current context and intended actions.
   - Ask clarifying questions where needed.
   - Wait for explicit user approval before executing work.

## Continuity Requirements

- Keep progress snapshots in `status.md`, actionable items in `todos.md`, and detailed notes in `handoff.md`.
- Update these artefacts after meaningful milestones or before handoff.
- Management efforts must never touch files outside `_ai.dev`; delivery efforts must leave `_ai.dev` unchanged unless instructed.

## Quick Commands

- Workflows: `workflow planning`, `workflow execution`, `workflow qa`, `workflow management`
- Protocols: `protocol boot`, `protocol tdd`, `protocol tasks`, `protocol playwright`, `protocol sub-agents`
- Orchestrators: `/plan`, `/execute`, `/qa`, `/manage`

Follow this checklist every time you boot.
