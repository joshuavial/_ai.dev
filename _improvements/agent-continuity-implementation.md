# Agent Continuity Protocol Implementation (Updated)

## Overview
We migrated continuity handling to live entirely inside each task folder. Agents now resume work by reading `technical-plan.md`, `status.md`, `todos.md`, and (optionally) `handoff.md` rather than relying on global state directories.

## Problem Solved
The previous design wrote workflow state into separate agent-specific directories, which drifted from the task-first model and confused cross-workflow handoffs. The new approach keeps every note, blocker, and resume instruction beside the task itself.

## Implementation Highlights

### 1. Protocol Rewrite
- **File**: `_ai.bws/protocols/agent-continuity.md`
- **Strategy**: Task-folder artefacts (`status.md`, `todos.md`, `handoff.md`)
- **Outcome**: No standalone state directories required

### 2. Agents Updated
- Workflow orchestrators now read/write continuity data in task folders
- Specialized agents (code-generator, manual-tester, tdd-enforcer, bug-investigator) log progress via `status.md` and `handoff.md`
- Protocol agents (boot, issue-sync) load context directly from task artefacts

### 3. Continuity Artefacts
Each task folder standardises the following:
- `technical-plan.md` — objectives & approach
- `status.md` — latest progress snapshot (workflow, blockers, next steps)
- `todos.md` — actionable checklist (managed via TodoWrite/TodoRead)
- `handoff.md` — optional detailed notes for future sessions
- `qa-report.md`, `tdd-evidence/`, `artifacts/` — supporting evidence when needed

### 4. Interrupt & Recovery Flow
1. On startup, agents inspect the task folder files above.
2. During work, they keep `status.md` and `todos.md` accurate.
3. Before pausing, they add any deep context to `handoff.md`.
4. Evidence lives in `tdd-evidence/`, `bug-investigation/`, or other task-local directories and is linked from the handoff.

## Benefits
- **Single Source of Truth**: Every detail about a task is in one place.
- **Zero Drift**: No hidden files outside the task folder hierarchy.
- **Clear Handoffs**: Agents expect `status.md` + `handoff.md` for resume instructions.
- **Workflow-Friendly**: Planning, execution, QA, and management all reference the same artefacts.

## Follow-Up Ideas
- Automate checks that ensure `status.md` timestamps are fresh before commits.
- Provide templates/snippets for `handoff.md` updates to keep style consistent.
- Consider a lightweight dashboard summarising `status.md` files for quick project overview.
