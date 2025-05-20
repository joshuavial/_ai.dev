# Quality Assurance Workflow

This document outlines the quality assurance (QA) process that should be followed after implementation is complete and before the reflection phase begins.

For the standardized GitHub issue structure, see [../shared/github-issue-structure.md](../shared/github-issue-structure.md).

## When to Begin QA

QA begins when:
- All implementation tasks in the execution workflow are complete
- The PR has been created and reviewed by at least one developer
- All automated tests are passing in the CI/CD pipeline
- The implementation is deployed to a testing environment

## QA Process

### 1. QA Preparation

- **Create QA Plan:** Define scope, testers, environments, and acceptance criteria
- **Update GitHub Issue:** Update state to "In QA" and add QA checklist
- **Prepare Test Environment:** Deploy changes and ensure proper configuration

Example QA section in Current State comment:

```
## Current State

**Status:** In QA

**QA Checklist:**
- [ ] Developer verification (AssignedTo: @original-dev)
- [ ] Peer developer review (AssignedTo: @peer-dev)
- [ ] Functional testing (AssignedTo: @qa-specialist)
- [ ] Accessibility testing (AssignedTo: @accessibility-specialist)
- [ ] Performance testing (AssignedTo: @performance-engineer)
- [ ] Product manager verification (AssignedTo: @product-manager)

**QA Notes:**
- Test environment: [URL]
- Test credentials: [if applicable]
- Special instructions: [if applicable]
```

### 2. Testing Layers

Proceed through these testing layers:

#### 2.1. Developer Verification
- Verify acceptance criteria are met
- Confirm implementation follows technical plan
- Check all test cases pass

#### 2.2. Code Quality Review
- Verify code follows project standards
- Check for security vulnerabilities
- Ensure proper error handling

#### 2.3. Functional Testing
- Verify features work as specified
- Test integration with other components
- Confirm error cases are handled gracefully

#### 2.4. Cross-Functional Testing
- **Accessibility:** WCAG compliance
- **Performance:** Load times, resource usage
- **Security:** Authorization, data validation
- **Usability:** Intuitive workflows, clear errors
- **Compatibility:** Browser/device compatibility

#### 2.5. Business Requirements Verification
- Verify implementation aligns with business goals
- Confirm user experience meets expectations

### 3. Issue Tracking and Resolution

- **Document Issues:** Create tickets categorized by severity
- **Prioritize Fixes:** Critical/major issues block completion
- **Verification Process:** Issues closed only when verified

Example issue template:

```
## QA Issue: [Brief Description]

**Severity:** [Critical/Major/Minor/Cosmetic]

**Environment:** 
- Browser/Device: [e.g., Chrome 95.0]
- OS: [e.g., Windows 11]
- URL: [where the issue was found]

**Steps to Reproduce:**
1. [Clear step-by-step instructions]

**Expected Behavior:**
[What should happen]

**Actual Behavior:**
[What actually happens]
```

### 4. Regression Testing

- Run automated regression tests
- Verify related functionality
- Ensure fixes don't introduce new issues

### 5. QA Sign-off

- **Final QA Review:** Verify all test cases executed and issues resolved
- **Documentation Update:** Update user/API documentation if needed
- **Update Status:** Mark QA as complete and document any accepted issues

Example QA completion update:

```
## Current State

**Status:** QA Complete

**QA Results:**
- [x] Developer verification - Passed
- [x] Peer developer review - Passed
- [x] Functional testing - Passed
- [x] Accessibility testing - Passed
- [x] Performance testing - Passed
- [x] Product manager verification - Approved

**Known Issues:**
- Minor: Button alignment on mobile (PROJ-789)

**Release Approval:**
Approved for production by @product-manager on [DATE]
```

## QA for Different Change Types

### Feature QA
- Verify against acceptance criteria
- Test integration with existing features
- Check for conflicts with other functionality

### Bug Fix QA
- Verify the bug is fixed
- Check for regression
- Add regression tests

### Performance Improvement QA
- Measure before/after metrics
- Test with production-like data volumes

### UI/UX Change QA
- Verify design implementation accuracy
- Test on various screen sizes
- Check accessibility compliance

## QA for AI-Assisted Implementation

1. **Algorithm Verification:**
   - Verify logic and algorithms
   - Check for edge cases
   - Ensure code follows project conventions

2. **Integration Review:**
   - Verify integration with existing code
   - Check dependency management
   - Confirm naming convention consistency

## QA Completion and Transition

Once QA is complete:

1. **Final Status Update:** Mark QA tasks complete and document any known issues
2. **Prepare for Reflection:** Gather QA metrics and lessons learned
3. **Transition to Reflection:** Begin reflection process

See [reflection-workflow.md](reflection-workflow.md) for the next phase.