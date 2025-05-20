# Fast Track Workflow

Before beginning any fast track work, follow the complete [Orientation Protocol](../shared/orientate.md) to understand your context.

This workflow should be used in conjunction with the [Task Management Guidelines](../shared/task-management.md) for managing smaller tasks and quick implementations.

This document outlines a streamlined workflow for handling small changes, individual bug fixes, and minor enhancements that don't warrant the full workflow process. The Fast Track workflow maintains quality while reducing overhead for straightforward tasks.

## When to Use Fast Track

Use this workflow when:

- Fixing a clearly defined bug with limited scope
- Making small enhancements (e.g., UI tweaks, copy changes)
- Implementing simple configuration changes
- Addressing technical debt with localized impact
- Making documentation updates

## Fast Track Process

### 1. Issue Creation

Create a GitHub issue with:

- **Clear Title**: Descriptive of the specific bug or enhancement
- **Concise Description**:
  - What needs to be fixed/changed
  - Expected behavior
  - Steps to reproduce (for bugs)
  - Screenshots/links when applicable

Example:

```
Title: Fix misaligned buttons on mobile checkout page

Description:
When viewing the checkout page on mobile devices (<768px width), 
the "Continue" and "Back" buttons are misaligned and overflow the container.

Steps to reproduce:
1. Go to checkout page
2. Resize browser to mobile width
3. Observe buttons in the footer

Expected: Buttons should be properly aligned within container
Current: Buttons overflow and appear misaligned

Screenshot: [attached]
```

### 2. Implementation

Implement the fix directly:

1. Create a branch with a descriptive name including issue number
2. Make the minimal necessary changes to fix the issue
3. Add or update tests to verify the fix
4. Ensure all existing tests pass
5. Document any non-obvious decisions in code comments

For bug fixes, follow this simple test-driven approach:
- Write a test that reproduces the bug (fails)
- Implement the fix to make the test pass
- Run all related tests to ensure no regression

**IMPORTANT:** When using Playwright for browser automation, strictly follow the [Playwright Management Guidelines](../shared/playwright-management.md). Never attempt to install browsers and always stop execution immediately if any Playwright operation fails.

### 3. Review and Merge

For fast-track items, use a simplified review process:

1. Create a pull request with a brief description:
   - What changed
   - How it fixes the issue
   - Any testing considerations
   
2. Request review from one team member
   - Reviewer focuses on correctness and potential side effects
   - Full code style review is less critical for small changes

3. After approval, merge and close the issue
   - Include "Fixes #XX" in the merge commit message
   - Verify the change works in the target environment

### 4. Quick Reflection

For fast-track items, use an abbreviated reflection:

- Add a brief comment on the closed issue noting:
  - Actual root cause (for bugs)
  - Any lessons learned
  - Suggestions to prevent similar issues

Example:

```
Quick reflection:
- Root cause: CSS media query breakpoint was incorrect (360px instead of 768px)
- Fixed by updating the breakpoint and making buttons stack vertically on mobile
- Lesson: We should use our standard breakpoint variables instead of hardcoded values
```

## Fast Track Issue Template

```
## Description
Brief, clear explanation of what needs to be done.

## For Bugs
### Steps to Reproduce
1. 
2.
3.

### Expected Behavior


### Current Behavior


### Screenshots/Logs
(If applicable)

## For Enhancements
### Current Limitation


### Proposed Change


### Expected Benefit


## Additional Context
Any other information that might be helpful.
```

## Limitations and Escalation

The Fast Track workflow is designed for simple, contained changes. Escalate to the full workflow if:

- The issue turns out to be more complex than initially thought
- The fix requires changes to multiple components
- There are significant cross-cutting concerns (security, performance)
- The change affects critical user flows or business logic

To escalate:
1. Convert the issue to use the full workflow structure
2. Add the Problem Statement and Technical Plan comments
3. Follow the standard Planning → Execution → QA → Reflection process

## Benefits of Fast Track

- **Reduced Overhead**: Minimal documentation for straightforward changes
- **Faster Turnaround**: Streamlined process for quick fixes
- **Appropriate Rigor**: Maintains quality while scaling process to task complexity
- **Developer Satisfaction**: Less friction for simple tasks

The Fast Track workflow maintains quality standards while providing a more efficient path for handling smaller issues that don't require the full workflow overhead.