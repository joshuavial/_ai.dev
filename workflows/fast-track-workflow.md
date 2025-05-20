# Fast Track Workflow

This streamlined workflow is designed for small bug fixes and minor enhancements that don't require the full planning, execution, QA, and reflection processes.

## When to Use Fast Track

Use this workflow when:
- Fixing a simple bug with a clear cause
- Making minor UI adjustments
- Adding small features with minimal complexity
- Implementing straightforward performance improvements
- Making documentation updates

Do NOT use this workflow for:
- Complex features spanning multiple components
- Changes requiring extensive testing
- High-risk modifications to core systems
- Changes affecting multiple services or APIs
- Features requiring extensive planning

## Fast Track Process

### 1. Issue Review and Analysis

- Verify the issue is appropriate for fast track
- Understand the root cause or requirements
- Estimate complexity and effort (should be small)
- Identify the specific files that need to be modified
- Consider potential side effects

### 2. Rapid Implementation

- Create a feature branch with the issue number
- Follow TDD principles but with simplified documentation:
  - Write minimal tests to verify the fix/feature
  - Implement the simplest solution that works
  - Refactor as needed
- Run relevant tests to ensure no regressions
- Update documentation if necessary

### 3. Streamlined Verification

- Run all affected tests
- Perform manual testing of the fix/feature
- Verify edge cases as appropriate
- Run linting and formatting tools
- Ensure code meets project standards

### 4. Quick PR and Review

- Create a concise PR with clear description
- Reference the issue number
- Include test results and verification steps
- Request review from appropriate team members
- Address review comments promptly

### 5. Mini-Reflection

After completion, document a brief reflection in the issue:
- What went well
- Any challenges encountered
- Lessons learned
- Suggestions for similar issues

## Example Fast Track Issue

```
## Issue: Fix button alignment on profile page (#123)

**Description:**
The "Save" button on the profile page is misaligned on mobile devices.

**Fast Track Analysis:**
- Root cause: Missing responsive class in button container
- Files to modify: client-app/components/ProfileForm.tsx
- Complexity: Low
- Estimated time: 30 minutes
- Risk: Low (isolated UI fix)

**Implementation:**
- [x] Added test for button alignment
- [x] Added missing responsive class
- [x] Verified fix on multiple device sizes
- [x] Ran linting and tests

**PR:** #124

**Mini-Reflection:**
- This was a simple CSS fix that took less time than expected
- Similar alignment issues might exist on other forms
- Created issue #125 to check for similar problems on other pages
```

## Fast Track Guidelines

1. **Keep it small:** If implementation exceeds 2 hours, consider using the full workflow
2. **Maintain quality:** Fast track doesn't mean skipping tests or code quality
3. **Be focused:** Address only the specific issue at hand
4. **Document clearly:** Make sure your changes and reasoning are well-documented
5. **Watch for scope creep:** If requirements expand during implementation, consider switching to the full workflow

## When to Escalate

If during fast track implementation you discover:
- The issue is more complex than initially thought
- The fix requires changes to multiple components
- There are significant test gaps
- The risk is higher than anticipated

Then:
1. Stop fast track implementation
2. Document your findings
3. Request to switch to the full workflow