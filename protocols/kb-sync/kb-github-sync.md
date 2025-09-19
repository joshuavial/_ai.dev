# Knowledge Base & GitHub Issue Synchronization

This document outlines a streamlined approach to minimize GitHub issue content while maximizing knowledge base documentation, with reliable synchronization between the two.

## Core Principles

1. **Knowledge Base as Source of Truth**: Store comprehensive documentation in the KB
2. **GitHub Issues as Work Pointers**: Keep issues minimal with links to KB docs
3. **Bidirectional Synchronization**: Maintain consistency between KB and GitHub
4. **Branch-Independent Documentation**: Ensure planning is accessible across branches

## Simplified GitHub Issue Structure

### New GitHub Issue Format

Each GitHub issue should contain only:

1. **Issue Title**: Clear, descriptive name including issue number
2. **Issue Description**:
   - One-line summary of the task
   - Link to complete documentation in KB
   - Key stakeholders and dependencies
   - Labels for categorization

3. **Current State Comment**:
   - Status indicator (Planning/In Progress/Review/Complete)
   - Link to current state document in KB
   - Most recent update timestamp
   - Current blockers (if any)

Example GitHub issue:

```markdown
Title: #123 Implement User Onboarding Wizard

Description:
Create an interactive onboarding wizard to guide new users through profile setup.
📝 Full Documentation: _ai/issues/123-user-onboarding-wizard.md
Dependencies: #120, #121
Stakeholders: @product-manager, @design-lead

Labels: feature, frontend, user-experience
```

Current State Comment:
```markdown
## Current State

**Status**: In Progress
**Task Status**: _ai/tasks/onboarding-wizard-navigation/status.md
**Last Updated**: 2023-08-15 14:30
**Blockers**: Waiting for design assets from @design-lead

Use `gh issue view 123 --comments --repo owner/repo` for complete history
```

## Knowledge Base Structure

### 1. Issue Documentation

Store comprehensive documentation in KB:

```
_ai/
  issues/
    123-user-onboarding-wizard.md  # Complete planning documentation
  tasks/
    onboarding-wizard-navigation/
      technical-plan.md
      status.md                    # Concise progress snapshot
      todos.md
    onboarding-step-content/
      technical-plan.md
      status.md
      todos.md
  tdd/
    123-tdd-evidence/              # TDD evidence folder
      navigation-component-red.png
      navigation-component-green.png
      navigation-component-refactor.png
```

### 2. Documentation File Structure

Each issue documentation file (`_ai/issues/123-user-onboarding-wizard.md`) contains:

```markdown
# User Onboarding Wizard

## Problem Statement
[Complete problem description, acceptance criteria, etc.]

## Technical Plan
[Complete technical approach, component breakdown, etc.]

## Testing Strategy
[Complete test strategy]

## Related
- GitHub Issue: #123
- PRD: _ai/prds/user-onboarding.md
- Tasks:
  - _ai/tasks/onboarding-wizard-navigation/
  - _ai/tasks/onboarding-step-content/
```

Each task status file (`_ai/tasks/onboarding-wizard-navigation/status.md`) contains:

```markdown
# Status: User Onboarding Wizard

**Updated**: 2025-01-04 14:30
**Progress**:
- Planning complete
- Navigation component implemented
  - RED/ GREEN/ REFACTOR evidence in `_ai/tdd/123-tdd-evidence/`
- Branch: feature/onboarding-wizard-navigation

**Next Steps**:
- [ ] Implement step content components
- [ ] Run integration tests

**Blockers**: Waiting for design assets from @design-lead

## Blockers
- Waiting for design assets from @design-lead

## Notes
- Found potential performance issue with animation library
- Created ticket #145 for accessibility improvements
```

## Synchronization Process

### 1. GitHub → KB Synchronization

When a new GitHub issue is created:

1. **Create KB Files**:
   - Generate `_ai/issues/[number]-[name].md`
   - Ensure `_ai/tasks/[task-slug]/status.md` exists for live updates
   - Create task files as needed in `_ai/tasks/`

2. **Update KB from Issue**:
   - Extract issue description and convert to problem statement
   - Extract technical planning if provided
   - Create links to GitHub issue

### 2. KB → GitHub Synchronization

When KB documentation is updated:

1. **Update Issue Summary**:
   - Generate concise summary from KB documentation
   - Update GitHub issue description with link to KB doc
   - Update Current State comment with latest status

2. **Attach Evidence**:
   - Add links to TDD evidence in KB
   - Update progress status in GitHub issue

### 3. Automation Scripts

Create standard scripts for synchronization:

1. **kb-to-github.sh**:
   ```bash
   # Usage: kb-to-github.sh [issue-number]
   # Updates GitHub issue from KB documentation
   ```

2. **github-to-kb.sh**:
   ```bash
   # Usage: github-to-kb.sh [issue-number]
   # Creates or updates KB files from GitHub issue
   ```

3. **sync-all.sh**:
   ```bash
   # Synchronizes all active issues in both directions
   ```

## Branch Navigation and History

### 1. Branch-Independent Documentation

With documentation stored in the KB:

1. All planning and status documents are available across branches
2. Switching branches doesn't lose planning context
3. New team members can access complete documentation regardless of branch

### 2. Capturing History

For historical reference:

1. KB documents include timestamps for each update
2. GitHub issue comments maintain discussion history
3. Current state file tracks progress over time

### 3. Branching and Merging Strategy

When managing multiple branches:

1. KB documents should be merged as part of PR process
2. Conflicts in KB documents should be resolved during code review
3. Use `.gitattributes` to handle KB document merges appropriately

## Implementation

### 1. Templates

Create standard templates for:
- KB issue documentation
- KB current state tracking
- GitHub issue format
- GitHub current state comment

### 2. Tooling

Develop lightweight tools to:
- Generate KB files from GitHub issues
- Update GitHub issues from KB files
- Validate synchronization status
- Report on inconsistencies

### 3. Transition Strategy

To migrate from current approach:
1. Start with new issues using the simplified format
2. Gradually migrate existing issues as they're updated
3. Create KB documentation for active issues first
4. Document synchronization best practices

## Advantages

This approach provides several key advantages:

1. **Complete Documentation in Codebase**:
   - Documentation lives with code and is version controlled
   - Available across branches and clones
   - Accessible without GitHub API access

2. **Reduced GitHub Content**:
   - Issues are concise and focused
   - Less information lost in comment history
   - Faster to navigate and understand

3. **Planning Persistence**:
   - Planning documents accessible regardless of branch
   - No loss of context when switching branches
   - Easy to reference across multiple issues

4. **Better LLM Context Management**:
   - Structured documentation is easier for LLMs to process
   - Documentation format optimized for context windows
   - Reduced redundancy saves tokens
