# Current Work Tracking

This document outlines the approach for tracking current work context across workflows. The current work state should be maintained in `_ai/current.md` and should be one of the first files checked during orientation.

## Purpose

The Current Work Tracking system ensures that:
- All workflows know what the user is currently working on
- Context is maintained between sessions and workflow switches
- Each level of work (PRD, issue, task, todo) is clearly identified
- Work can be resumed efficiently after interruptions

## Current Work File Structure

The `_ai/current.md` file should follow this structure:

```markdown
# Current Work Status

## Active PRD
[PRD name or null if not working on a specific PRD]

## Active Issue
[GitHub issue number/name or null if not working on a specific issue]

## Active Task
[Task name or null if not working on a specific task]

## Notes
[Any contextual notes about the current work]

## Last Updated
[Timestamp of when this file was last updated]
```

All fields except "Notes" and "Last Updated" may be set to `null` if no work is currently active at that level.

## Updating the Current Work State

The current work state should be updated:

1. When switching focus to a different PRD, issue, or task
2. When starting work on a new PRD, issue, or task
3. When completing or abandoning work at any level
4. When the user explicitly requests a context switch

### Update Process

1. Read the existing `_ai/current.md` file
2. Update the relevant sections (PRD, Issue, Task)
3. Add explanatory notes where appropriate
4. Update the timestamp
5. Confirm the changes with the user before saving

## Reading Current Work in Orientation

During the Orientation Protocol, reading the `_ai/current.md` file should be one of the first steps. This gives immediate context about what the user is currently working on before proceeding with other orientation steps.

Update the orientation checklist to include:
- What is the current PRD?
- What is the current issue?
- What is the current task?

## Example Current Work File

```markdown
# Current Work Status

## Active PRD
User Onboarding System

## Active Issue
#123 - Implement Onboarding Wizard UI

## Active Task
Create responsive step navigation component

## Notes
- The navigation component should work on mobile and desktop views
- Need to ensure it's fully accessible
- Component should integrate with the existing OnboardingContext

## Last Updated
2025-05-20T15:30:00Z
```

## Example with Null Values

```markdown
# Current Work Status

## Active PRD
null

## Active Issue
#456 - Fix login button alignment on mobile

## Active Task
null

## Notes
- This is a small fix not associated with a specific PRD
- The issue was reported by QA team on May 19

## Last Updated
2025-05-20T09:15:00Z
```

## Integration with Workflows

### Planning Workflow
- Check current work state at the beginning of planning
- Update current work state when planning a new item
- Set appropriate levels to null when planning smaller items

### Execution Workflow
- Check current work state to understand context
- Update task field when switching between tasks
- Document progress in Notes section

### Fast Track Workflow
- May only use Issue field without PRD or Task
- Update current work to reflect quick fixes

## Benefits

Maintaining a clear current work state:
- Reduces context-switching costs
- Prevents work from being lost or forgotten
- Helps multiple workflows collaborate effectively
- Provides clarity for the user about what's being worked on
- Enables quicker resumption of work after interruptions