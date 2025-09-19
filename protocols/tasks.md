# Task Management Across Workflows

This document outlines the standardized approach for managing work items across all workflows. It defines the hierarchy of work units, appropriate tracking methods, and responsibilities for different workflow phases.

## Work Item Hierarchy

The project uses a consistent hierarchy to organize work:

### 1. PRD (Product Requirements Document)
- High-level document defining a feature area or product component
- Provides long-term context for clusters of tasks
- Maintained under `_ai/prds/`
- Example: "User Onboarding System PRD"

### 2. Task (Primary Work Unit)
- Significant unit of work planned and executed within `_ai/tasks/`
- Non-trivial effort requiring multiple steps and verification
- Tied to a specific goal or acceptance criteria
- May optionally reference GitHub issues or other external trackers
- Example: "Create responsive onboarding step navigation"

### 3. Todo
- Small, actionable item within a task
- Tracked using the internal `TodoWrite`/`TodoRead` tools
- Typically takes minutes to an hour to complete
- Example: "Add hover states to navigation buttons"

> GitHub issues remain available for collaboration or status sharing, but `_ai/tasks/` is the authoritative source of truth for day-to-day progress.

## Tracking Mechanisms

### PRD Tracking
- Store in `_ai/prds/` directory as markdown files
- Use YAML frontmatter for metadata (status, key contacts, related docs)
- Update status as core supporting tasks are completed

### Task Tracking
- Create a dedicated folder in `_ai/tasks/` for each active task
- Use consistent, descriptive slugs (e.g., `onboarding-step-navigation`)
- Maintain the standard file set:
  - `technical-plan.md` — objectives, scope, acceptance criteria
  - `status.md` — concise progress snapshot, blockers, next steps
  - `todos.md` — actionable checklist maintained with TodoWrite/TodoRead
  - `qa-report.md` — QA findings (added during QA workflow)
  - `handoff.md` — optional detailed notes for future sessions or collaborators
- Add supporting artifacts as needed (`tdd-evidence/`, `artifacts/`, `implementation.md`, etc.)
- Reference external trackers (GitHub issues, design docs) inside the task folder only when necessary
- Keep `_ai/tasks.md` (task index) up to date with the task slug, workflow, status, and last-updated timestamp

### Task Index (`_ai/tasks.md`)
- Store a single Markdown table that inventories every active and recently completed task
- Recommended columns: `Task`, `Workflow`, `Status`, `Updated`, `Notes`
- Example:
  ```markdown
  | Task                         | Workflow  | Status         | Updated           | Notes                     |
  | ---------------------------- | --------- | -------------- | ----------------- | ------------------------- |
  | onboarding-step-navigation   | execution | in progress    | 2025-01-04 15:12  | waiting on design assets  |
  | onboarding-data-storage      | planning  | ready for impl | 2025-01-02 09:45  | pending security review   |
  ```
- When a task is closed, either move it to a "Completed" section or mark the Status column accordingly so future agents have historical context

### Todo Tracking
- Use internal TodoWrite/TodoRead tools to maintain the task's `todos.md`
- Create todos at the beginning of implementing each task
- Mark todos as in_progress and completed as you work
- Keep todos small and actionable

## Task File Template

```markdown
# Task: [Short Title]

## References (Optional)
- GitHub Issue: #[number] - [title]
- PRD: [link or filename]

## Description
[Detailed description of the task]

## Acceptance Criteria
- [Criterion 1]
- [Criterion 2]
- [Criterion 3]

## Implementation Notes
- [Any technical notes, approaches, or decisions]
- [References to relevant code or documentation]

## Status Snapshot
- **Workflow**: planning | execution | qa | management
- **Updated**: [yyyy-mm-dd HH:MM]
- **Progress**: [Short bullet summary]
- **Next Steps**: [Top 2-3 todos]
- **Blockers**: [Any current blockers, or "None" if none exist]
```

### Status.md Example

```markdown
# Status: onboarding-step-navigation

- **Workflow**: execution
- **Updated**: 2025-01-04 15:12
- **Progress**:
  - RED/GREEN complete for navigation state manager (coverage 92%)
  - Refactored button group to use shared `PrimaryButton`
  - Added analytics event for "next" clicks
- **Next Steps**:
  - [ ] Wire countdown timer UI
  - [ ] Update integration test `onboarding-flow.spec.ts`
- **Blockers**: Waiting on final copy for tooltip content from design (@alex)
```

## Responsibilities by Workflow Phase

### Planning Workflow

The Planning phase is responsible for structuring and organizing work:

1. **PRD Alignment**
   - Analyze PRD requirements and current goals
   - Identify the discrete tasks needed to satisfy the requirements
   - Confirm scope and exclusions with stakeholders

2. **Task Definition**
   - Create task folders in `_ai/tasks/`
   - Capture acceptance criteria and completion signals in `technical-plan.md`
   - Draft initial `status.md` with known context, open questions, and dependencies
   - Note any relevant references (PRDs, GitHub issues, design docs)
   - Add a new row to `_ai/tasks.md` (or update an existing one) to reflect the task's workflow, status, and timestamp

3. **Task Organization**
   - Prioritize tasks based on impact and dependencies
   - Document sequencing expectations directly in the task plans
   - Ensure tasks are substantial but manageable (hours-scale)

### Execution Workflow

The Execution phase is responsible for implementing and tracking work:

1. **Task Implementation**
   - Reference the task folder continuously during implementation
   - Break down work into `todos.md` items using TodoWrite
   - Update `status.md` with succinct progress notes
   - Follow the TDD cycle for implementation

2. **Progress Tracking**
   - Mark todos as in_progress → completed as you work
   - Refresh `status.md` regularly with progress, blockers, and next steps
   - Document any blockers encountered

3. **Task Completion**
   - Verify all acceptance criteria are met
   - Mark all related todos as completed
   - Update `status.md` with completion notes and final status
    - Update `_ai/tasks.md` to reflect the final status (e.g., mark as completed or archive)

### Managing New Requests During Implementation

When new task requests come in during a workflow:

1. **Assess Impact**
   - Determine if it's within current scope or represents new work
   - Evaluate priority against current tasks

2. **Small Related Requests**
   - If small and directly related to current work, add as a todo
   - Mark with special tag for traceability

3. **Substantial New Requests**
   - Create a new task folder in `_ai/tasks/`
   - Copy or link relevant context (PRDs, GitHub issues, etc.) into the folder
   - Discuss prioritization with user

4. **Critical Interruptions**
   - For urgent work that interrupts current flow:
   - Document current progress in detail
   - Create checkpoint in task file
   - Handle urgent request
   - Return to original task and continue from checkpoint

## Size Guidelines

To maintain consistency in work breakdown:

### Task Size
- Should take 1-8 hours to complete
- Contains 5-15 todos
- Focuses on a single component or functionality
- Too large: Spans multiple components or > 1 day
- Too small: Can be completed in < 30 minutes (should be a todo instead)

### Todo Size
- Should take 5-60 minutes to complete
- Represents a single, atomic action
- Has a clear completion state
- Too large: Multiple distinct steps or > 1 hour
- Too small: Trivial change taking < 5 minutes (can be grouped with related todos)

## Examples

### Example Hierarchy

**PRD**: User Onboarding System

**Tasks**:
- onboarding-wizard-navigation
- onboarding-step-content
- onboarding-progress-tracking
- onboarding-responsive-layouts

**Todos for Task `onboarding-wizard-navigation`**:
- Create basic navigation container component
- Implement next/previous buttons
- Add step indicator display
- Implement step validation
- Add animations for transitions
- Write unit tests for navigation
- Add accessibility attributes
- Implement keyboard navigation

## Best Practices

1. **Keep Work Units Discrete**
   - Each task should be completable independently
   - Minimize dependencies between tasks
   - Define clear boundaries between tasks

2. **Document Progress Continuously**
   - Update task files as you work
   - Keep todos current and accurate
   - Provide enough detail for someone else to continue your work

3. **Checkpoint Regularly**
   - At logical stopping points, update all tracking mechanisms
   - Document the current state thoroughly
   - Ensure partially-completed work is well-documented

4. **Maintain Traceability**
   - Link task folders back to their parent PRD (and optional external trackers)
   - Reference specific tasks in commit messages
   - Update parent documents when child tasks are completed

5. **Handle Scope Changes Appropriately**
   - For small scope changes, add todos
   - For significant scope changes, create new tasks
   - For major scope changes, coordinate any necessary updates to external trackers (e.g., GitHub issues)
   
## Implementation in Existing Workflows

This task management approach should be integrated into all existing workflows:

1. **Planning Workflow**
   - Add task breakdown steps 
   - Include task file creation in the planning outputs
   - Ensure Technical Plan includes task structure

2. **Execution Workflow**
   - Reference tasks at the beginning of implementation
   - Break tasks into todos using TodoWrite
   - Update task files (`status.md`, `handoff.md`, `todos.md`) as progress is made


3. **QA Workflow**
   - Verify all tasks meet acceptance criteria
   - Ensure task files are properly updated to reflect completion

4. **Management Workflow**
   - Review effectiveness of task breakdown
   - Identify improvements for future task management processes
