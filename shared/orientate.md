# Orientation Protocol

This document outlines the required orientation process for all workflow roles. Before beginning any work, you must follow this protocol to ensure you understand the full context and scope of your task.

## Purpose

The Orientation Protocol ensures that:
- You understand what you're working on at multiple levels of abstraction
- You have the complete context needed for your specific role
- You can locate yourself within the larger development workflow
- You know what's already been done and what remains to be done
- You can resume work efficiently after interruptions or across sessions

## Required Orientation Steps

### 1. Current Work Identification

**ALWAYS start by checking the current work state:**

```
Read the file: _ai/current.md
```

This file follows the structure defined in [Current Work Tracking](./current-work-tracking.md) and contains:
- Active PRD (or null)
- Active Issue (or null)
- Active Task (or null)
- Contextual notes about the current work

Understanding the current work context is critical before proceeding with other orientation steps.

### 2. Branch Identification

**Next, identify which branch you're working in:**

```bash
git branch --show-current
```

The branch name typically follows the pattern `[issue-number]-[description]` or `feature/[description]`. Extract the issue number from the branch name.

### 3. GitHub Issue Review

**Use the issue number to locate and read the GitHub issue:**

```bash
gh issue view [issue-number]
```

From the issue:
- Read the main issue description to understand the high-level goal
- Identify any linked PRDs or related documents
- Determine which workflow phase is currently active (Planning, Execution, QA, or Fast Track)
- Review the Current State comment to understand progress

### 4. PRD Orientation

**Locate and read the relevant PRD:**

- Look for PRD links in the GitHub issue
- Check the `_ai/prds/` directory for related documents
- Understand where this issue fits in the larger product feature

Review the PRD to:
- Understand the broader context and purpose
- Identify related issues or dependencies
- Recognize user/business goals for this work

### 5. Task Orientation

**Identify your current task within the overall execution plan:**

- Check the `_ai/tasks/` directory for task files related to your issue
- Review the technical plan in the GitHub issue
- Determine which specific task you're working on

For each task:
- Review its description and acceptance criteria
- Check its current progress and notes
- Understand its relationship to other tasks

### 6. Todo Orientation

**Review current todos for your immediate next actions:**

```
Use TodoRead tool to view current todos
```

- Identify which todos are in progress
- Note which todos are pending
- Recognize any blockers or dependencies

## Role-Specific Orientation Requirements

### Planner Role

The Planner must orient at multiple levels:

#### PRD Level Planning
- Read and understand the complete PRD
- Identify all stakeholders and their needs
- Break down the PRD into logical issues
- Define acceptance criteria for each issue
- Create GitHub issues with appropriate structure

#### Issue Level Planning
- Analyze the specific GitHub issue requirements
- Create a detailed technical plan
- Break down the issue into specific tasks
- Define acceptance criteria for each task
- Create task files in `_ai/tasks/` directory

#### Task Level Planning
- Review the specific task requirements
- Break down the task into granular todos
- Use TodoWrite to create a structured todo list
- Define the implementation approach
- Identify any dependencies or prerequisites

### Executor Role

The Executor must orient to understand implementation context:

- Review the technical plan thoroughly
- Understand which tasks are assigned/in progress
- Review current todo items and their status
- Check for any blockers or dependencies
- Understand the TDD cycle requirements for this work
- Verify any prerequisites are met before starting work

### Fast Track Role

The Fast Track role requires focused orientation:

- Understand the specific bug or enhancement
- Review any related code areas
- Check for similar issues or past fixes
- Verify repro steps for bugs
- Understand the scope limitations of fast track work

## Orientation After Interruptions

When resuming work after an interruption:

1. Re-read the GitHub issue Current State
2. Check TodoRead for current todo status
3. Review the task file for latest progress notes
4. Run `git status` to check for uncommitted changes
5. Review any recent commits related to the issue

## Multi-Level Context Switching

When you need to operate at different levels of the hierarchy:

1. **Explicitly state which level you're addressing**
   - "I'm now operating at the PRD level..."
   - "I'm now focusing on task-level planning..."
   
2. **Complete orientation at the new level**
   - Perform the orientation steps for the new context
   - Document your current state at the previous level
   
3. **Document context switches**
   - Note in the GitHub issue when you switch contexts
   - Record why the switch was necessary
   - Document what was completed at the previous level

## Example Orientation Process

```
# Starting work on a new branch

# 1. Identify the branch
git branch --show-current
> 123-implement-user-onboarding

# 2. Review the GitHub issue
gh issue view 123
> Issue #123: Implement User Onboarding Wizard
> ...
> [Current State section indicates Planning phase is complete, Execution phase started]
> References PRD: User Onboarding System

# 3. Review the PRD
cat _ai/prds/user-onboarding-system.md
> [PRD content reviewed]

# 4. Identify current task
ls _ai/tasks/*123*
> _ai/tasks/123-wizard-navigation-component.md
> _ai/tasks/123-step-content-components.md
> _ai/tasks/123-progress-tracking.md

cat _ai/tasks/123-wizard-navigation-component.md
> [Task is in progress with 50% completion]

# 5. Review todos
TodoRead
> [Todos show 3 completed, 2 in progress, 5 pending]

# Ready to continue work on the wizard navigation component task
```

## Orientation Checklist

Before beginning work, verify you can answer all of these questions:

- [ ] What is the current work state (PRD/Issue/Task)?
- [ ] Does the current work align with the branch I'm in?
- [ ] What branch am I working in?
- [ ] What issue number am I addressing?
- [ ] What is the overall goal of this issue?
- [ ] Which PRD does this work relate to?
- [ ] What is the broader purpose of this feature?
- [ ] Which workflow phase am I in?
- [ ] What task am I currently working on?
- [ ] What has been completed so far?
- [ ] What is the next immediate action?
- [ ] Are there any blockers or dependencies?
- [ ] Are there any specific notes in the current work file?

## Confirmation of Understanding

**CRITICAL**: After completing the orientation process, you MUST:

1. **Summarize your understanding** of the current state and the work to be done
2. **Present this summary to the user** for confirmation
3. **Ask specific questions** about anything that is unclear
4. **Wait for explicit confirmation** before beginning any work

Example:
```
Based on my orientation, I understand that:
- I'm working on branch 123-user-onboarding (Issue #123)
- This issue is part of the User Onboarding System PRD
- I'm in the Execution phase of the workflow
- The current task is implementing the wizard navigation component
- 3 todos are completed, 2 are in progress, and 5 are pending
- The next step is to implement the step indicator display

Is this understanding correct? Do you have any additional context or directions before I begin?
```

Only proceed with work after the user has confirmed your understanding is correct.