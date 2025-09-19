# LLM Boot Sequence Protocol

This document defines the initialization process for LLMs working on this project. It provides a clear, ordered sequence of operations that should be performed at the start of each session to establish proper context and state awareness.

## Boot Sequence Overview

When starting a new session or after context clearing:

1. **Read CLAUDE.md** - Establish base project understanding
2. **Review state references** - Gather current project context from `_ai/`
3. **Select active task** - Confirm which task (if any) is in focus
4. **Determine active workflow** - Confirm with available context or the user
5. **Load workflow-specific instructions** - From core instructions
6. **Establish awareness** - Review the chosen task's artefacts
7. **Confirm understanding** - Summarize context with the user before acting

## 1. Read CLAUDE.md

The first step in any session is to read the project's base configuration:

```
Read CLAUDE.md
```

CLAUDE.md contains:
- Project-specific guidelines
- Development environment details
- Command reference
- Workflow system reference

This establishes foundational knowledge about the project and its structure.

Additionally, check for port configuration:
```
Read _ai/port.md  # If exists - contains project port assignments
```

## 2. Review State References

After establishing baseline understanding, gather current context by scanning the shared artifacts maintained under `_ai/`:

- `_ai/ports.md` for port assignments (when applicable)
- `_ai/prds/` for product context and other background materials referenced by tasks
- Each active task folder under `_ai/tasks/`:
  - `technical-plan.md` for objectives and approach
  - `status.md` for current progress, blockers, and next steps
  - `todos.md` for actionable checklist items
  - Optional artifacts (`qa-report.md`, `tdd-evidence/`, etc.)

If these documents do not make the active effort clear, ask the user to clarify the current focus before proceeding.

Review `_ai/tasks.md` to see the current task index. The table should list each task slug, workflow, status, timestamp, and any notes. If the index is missing or out of date, fall back to the Tasks Protocol to reconstruct it before proceeding (or, as a temporary measure, enumerate `_ai/tasks/*/` manually).

## 3. Select Active Task

After reviewing the task index:

1. **Zero Tasks Detected**
   - Ask: “No existing tasks found. Do you want to create a new task folder, work without a task, or point me to an existing path?”
   - If the user opts for a new task, follow the Tasks Protocol to create one before continuing.
2. **One Candidate Task**
   - Confirm with the user: “Should I continue with `<task-slug>` (workflow: X)?”
   - If the user declines, ask how they want to proceed (switch task, create new, or work without one).
3. **Multiple Candidate Tasks**
   - Present a numbered list derived from `_ai/tasks.md` (include workflow, status, last updated time).
   - Ask the user to choose a task, create a new one, or work without a task.
4. **Work Without Task**
   - If the user explicitly chooses to operate without a task, document that choice and continue. Remind them that capturing notes may be harder without a dedicated folder.

Always wait for explicit user confirmation before locking in the active task. If the user selects a task that is missing required artefacts (`status.md`, `todos.md`), create placeholders before proceeding.

## 4. Determine Active Workflow

Use the available state documents and recent user guidance to decide which workflow phase applies. If the materials under `_ai/` do not clearly identify the active workflow, confirm it directly with the user before continuing.

## 5. Load Workflow Instructions

Once the active workflow is identified, load the core instructions for that workflow:

```
# Read core instructions
Read _ai.bws/core-instructions.md

# Filter for relevant workflow instructions
Apply [WORKFLOW:${workflow_phase}] instructions
```

The core-instructions.md contains tagged instructions for each workflow phase.

## 6. Establish Awareness

Before taking any action, establish awareness of the current state and confirm understanding:

```
# If state docs or the user identify a specific task
if [[ -n "$active_task" ]]; then
  # Load the task plan and status
  Read _ai/tasks/${active_task}/technical-plan.md
  Read _ai/tasks/${active_task}/status.md

  # Review supporting notes (if present)
  Read _ai/tasks/${active_task}/qa-report.md
  Read _ai/tasks/${active_task}/todos.md

  # Optional: if the task references external artifacts (e.g., GitHub issues), open them for context
  if [[ -n "$linked_issue" ]]; then
    gh issue view $linked_issue
  fi
fi

# Check other running lists (e.g., shared backlog, meeting notes) if maintained separately
```

## 7. Confirm Understanding with User

After completing the boot sequence, provide a concise summary to the user:

```
Based on my initialization:
- Project: [from CLAUDE.md]
- Ports: [from port.md if available]
- Current workflow: [workflow_phase]
- Active task: [task identifier or "None"]
- Linked references: [optional list of GitHub issues, PRDs, or docs]
- Current status: [key points from status.md]
- Next steps: [top items from todos.md]

Is this understanding correct before I proceed?
```

Wait for user confirmation before taking any actions.

## Workflow Transitions

When state documents or the user signal that a workflow transition is needed:

1. **Complete current workflow** - Finish any remaining tasks in the current workflow
2. **Acknowledge transition** - Inform the user of the pending transition and confirm alignment
3. **Load next workflow** - Read instructions for the upcoming workflow
4. **Update shared notes** - Record the new workflow phase in the relevant `_ai/` tracking documents

## Workflow Phase-Specific Boot Extensions

### Planning Phase Boot

When in planning phase, additionally:
- Read related PRD document if specified
- Review existing tasks or features with overlapping scope
- Outline how new tasks will be organized within `_ai/tasks/`

### Execution Phase Boot

When in execution phase, additionally:
- Check branch status and name
- Review technical plan
- Verify test environment
- Check TDD evidence requirements

### QA Phase Boot

When in QA phase, additionally:
- Load QA checklist templates
- Check testing requirements
- Verify implementation completeness

## Implementation Details

### State Documentation

Maintain a lightweight source of truth within `_ai/` that captures:
- Active PRD and task (if any)
- Current workflow phase and any expected transition
- Recent notes, blockers, or decisions

The format can be Markdown checklists, structured JSON, or short status notes—as long as it is easy to locate and kept up to date.

### Workflow Identification

Workflows are identified by simple string identifiers:
- `planning` - Planning phase
- `execution` - Execution phase 
- `qa` - Quality Assurance phase
- `management` - Process improvement and workflow evolution

### Example Boot Sequence

```
# 1. Read base project configuration
Read CLAUDE.md

# 2. Review state references
Read _ai/tasks.md  # Task index table
Review `_ai/tasks/wizard-navigation/status.md`
Review `_ai/tasks/wizard-navigation/todos.md`

# 3. Select active task
Ask user: "Should I continue with task 'wizard-navigation' or choose/create another?"

# 4. Confirm workflow focus
Ask user: "Which workflow should I activate?"

# 5. Load workflow instructions
Read _ai.bws/core-instructions.md
# Filter for [WORKFLOW:execution] instructions (if execution confirmed)

# 6. Establish state awareness
Read _ai/tasks/wizard-navigation/technical-plan.md
Read _ai/tasks/wizard-navigation/status.md
Review _ai/tasks/wizard-navigation/qa-report.md (when present)
Review _ai/tasks/wizard-navigation/todos.md

# 7. Confirm understanding with user
# Provide state summary and await confirmation
```

## Benefits of This Approach

This boot sequence approach offers several advantages:

1. **Clean Start Capability** - Works after context clearing
2. **Consistent State Management** - Encourages centralized notes within `_ai/`
3. **Minimal Redundancy** - Focuses on shared task documentation without duplicating external issue trackers
4. **Workflow Flexibility** - Easy to modify workflow phases
5. **Clarity for LLMs** - Explicit, ordered instructions reduce confusion
6. **User Confirmation** - Verifies correct understanding before proceeding

## Implementation Notes

1. Keep shared `_ai/` notes updated:
   - When starting a new feature
   - When completing a workflow phase
   - When transitioning between workflows
   - When changing active tasks

2. LLMs should follow this exact boot sequence after context clearing

3. Workflow-specific instructions should be tagged in core-instructions.md to allow filtering
