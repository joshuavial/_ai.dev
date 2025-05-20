# Task Management Across Workflows

This document outlines the standardized approach for managing work items across all workflows. It defines the hierarchy of work units, appropriate tracking methods, and responsibilities for different workflow phases.

## Work Item Hierarchy

The project uses a consistent hierarchy to organize work:

### 1. PRD (Product Requirements Document)
- High-level document defining a feature area or product component
- Contains multiple issues/tickets that together implement the feature
- Typically takes weeks to months to complete all associated issues
- Example: "User Onboarding System PRD"

### 2. Issue/Ticket
- Substantial piece of work within a PRD
- Tracked in GitHub issues with the standardized format
- Typically takes days to complete
- Self-contained but related to other issues in the PRD
- Example: "Implement Onboarding Wizard UI Component"

### 3. Task
- Significant unit of work within an issue
- Non-trivial effort requiring multiple steps
- Tracked in _ai/tasks/ directory as individual markdown files
- Typically takes hours to complete
- Example: "Create responsive step navigation component"

### 4. Todo
- Small, actionable item within a task
- Tracked using the internal TodoWrite/TodoRead tools
- Typically takes minutes to an hour to complete
- Example: "Add hover states to navigation buttons"

## Tracking Mechanisms

### PRD Tracking
- Store in _ai/prds/ directory as markdown files
- Use YAML frontmatter for metadata (status, related issues)
- Update status as issues are completed

### Issue Tracking
- Use GitHub issues with standardized format
- Update third comment (Current State) regularly
- Link to related tasks in _ai/tasks/

### Task Tracking
- Create markdown files in _ai/tasks/
- Use consistent naming: `issue-number-short-description.md`
- Include sections for description, acceptance criteria, notes, and progress
- Update progress section as work advances

### Todo Tracking
- Use internal TodoWrite/TodoRead tools
- Create todos at the beginning of implementing each task
- Mark todos as in_progress and completed as you work
- Keep todos small and actionable

## Task File Template

```markdown
# Task: [Short Title]

## Related Issue
Issue #[number] - [title]

## Description
[Detailed description of the task]

## Acceptance Criteria
- [Criterion 1]
- [Criterion 2]
- [Criterion 3]

## Implementation Notes
- [Any technical notes, approaches, or decisions]
- [References to relevant code or documentation]

## Progress
- [yyyy-mm-dd] Started task
- [yyyy-mm-dd] [Progress update]
- [yyyy-mm-dd] Completed task

## Blockers
- [Any current blockers, or "None" if none exist]
```

## Responsibilities by Workflow Phase

### Planning Workflow

The Planning phase is responsible for structuring and organizing work:

1. **PRD Breakdown**
   - Analyze PRD requirements
   - Break down into logical issues
   - Ensure issues are properly sized (not too large/small)
   - Create GitHub issues for each identified work unit

2. **Issue Breakdown**
   - Break down each issue into tasks
   - Create task files in _ai/tasks/ directory
   - Define clear acceptance criteria for each task
   - Ensure tasks are substantial but manageable

3. **Task Organization**
   - Prioritize tasks within issues
   - Identify dependencies between tasks
   - Document any required sequencing in the issue's technical plan

### Execution Workflow

The Execution phase is responsible for implementing and tracking work:

1. **Task Implementation**
   - Reference task files from _ai/tasks/ directory
   - Break down tasks into todos using TodoWrite
   - Update task file with progress notes
   - Follow TDD cycle for implementation

2. **Progress Tracking**
   - Mark todos as in_progress → completed as you work
   - Update task file progress section regularly
   - Update issue Current State with overall progress
   - Document any blockers encountered

3. **Task Completion**
   - Verify all acceptance criteria are met
   - Mark all related todos as completed
   - Update task file with completion notes
   - Update issue Current State to reflect task completion

### Managing New Requests During Implementation

When new task requests come in during a workflow:

1. **Assess Impact**
   - Determine if it's within current scope or represents new work
   - Evaluate priority against current tasks

2. **Small Related Requests**
   - If small and directly related to current work, add as a todo
   - Mark with special tag for traceability

3. **Substantial New Requests**
   - Create a new task file in _ai/tasks/
   - Link to current issue if related
   - Discuss prioritization with user

4. **Critical Interruptions**
   - For urgent work that interrupts current flow:
   - Document current progress in detail
   - Create checkpoint in task file
   - Handle urgent request
   - Return to original task and continue from checkpoint

## Size Guidelines

To maintain consistency in work breakdown:

### Issue Size
- Should take 1-5 days to complete
- Contains 3-10 substantial tasks
- Has clear, testable acceptance criteria
- Too large: Requires multiple developers or > 1 week
- Too small: Can be completed in < 2 hours

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

**Issues**:
- #123 Implement onboarding wizard UI
- #124 Create onboarding data storage
- #125 Integration with user accounts

**Tasks for Issue #123**:
- 123-wizard-navigation-component.md
- 123-step-content-components.md
- 123-progress-tracking.md
- 123-responsive-layouts.md

**Todos for Task "123-wizard-navigation-component.md"**:
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
   - Always link between hierarchy levels (PRD → Issue → Task)
   - Reference specific tasks in commit messages
   - Update parent items when child items are completed

5. **Handle Scope Changes Appropriately**
   - For small scope changes, add todos
   - For significant scope changes, create new tasks
   - For major scope changes, discuss creating new issues
   
## Implementation in Existing Workflows

This task management approach should be integrated into all existing workflows:

1. **Planning Workflow**
   - Add task breakdown steps 
   - Include task file creation in the planning outputs
   - Ensure Technical Plan includes task structure

2. **Execution Workflow**
   - Reference tasks at the beginning of implementation
   - Break tasks into todos using TodoWrite
   - Update task files alongside Current State comments

3. **Fast Track Workflow**
   - For small changes, may skip task level and use todos directly
   - For slightly larger fast track items, create lightweight task files

4. **QA Workflow**
   - Verify all tasks meet acceptance criteria
   - Ensure task files are properly updated to reflect completion

5. **Reflection Workflow**
   - Review effectiveness of task breakdown
   - Identify improvements for future task management