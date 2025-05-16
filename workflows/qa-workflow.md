# Quality Assurance Workflow

This document outlines the quality assurance (QA) process that should be followed after implementation is complete and before the reflection phase begins. A thorough QA process ensures that all work meets quality standards and requirements before being considered complete.

For the standardized GitHub issue structure, see [../shared/github-issue-structure.md](../shared/github-issue-structure.md).

## QA Process Overview

The QA workflow serves as a bridge between execution and reflection, providing a structured approach to verify that all work meets required standards. This process should be applied to all significant features, bug fixes, and changes before they are considered ready for production.

## When to Begin QA

QA begins when:
- All implementation tasks in the execution workflow are marked complete
- The PR has been created and reviewed by at least one developer
- All automated tests are passing in the CI/CD pipeline
- The implementation is deployed to a testing or staging environment

## QA Team Composition

Depending on the complexity and scope of the changes, QA should involve:

1. **Developer QA:**
   - The original developer performs initial verification
   - A peer developer conducts a code review and functionality check

2. **Dedicated QA:**
   - QA specialists who focus on testing and quality
   - May include manual testers and/or QA automation engineers

3. **Domain Experts:**
   - Product managers to verify business requirements
   - Designers to verify UI/UX implementation
   - Subject matter experts for specialized domain logic

4. **Stakeholders:**
   - End-users or their representatives
   - Business stakeholders for high-impact features

## QA Workflow Process

### 1. QA Preparation

Before beginning QA testing:

- **Create QA Plan:**
  - Define what needs to be tested
  - Identify who will perform each test
  - Determine test environments needed
  - Establish acceptance criteria

- **Update GitHub Issue:**
  - Update the Current State comment to "In QA"
  - Add a QA checklist to the ticket
  - Tag appropriate team members for testing

- **Prepare Test Environment:**
  - Deploy changes to the appropriate environment
  - Ensure test data is available
  - Verify that the environment is properly configured

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
- [ ] User acceptance testing (AssignedTo: @stakeholder)

**QA Notes:**
- Test environment available at: [URL]
- Test credentials: [if applicable]
- Special testing instructions: [if applicable]
```

### 2. Testing Layers

QA should proceed through multiple testing layers:

#### 2.1. Developer Verification

The original developer verifies:
- All acceptance criteria are met
- Implementation follows the technical plan
- All test cases pass
- Code has appropriate documentation
- No obvious edge cases are missed

#### 2.2. Code Quality Review

A peer developer checks:
- Code follows project standards and best practices
- No security vulnerabilities are present
- Error handling is appropriate
- Edge cases are handled properly
- No unnecessary complexity or performance issues

#### 2.3. Functional Testing

QA specialists verify:
- All features work as specified
- Integration with other components works correctly
- Error cases are handled gracefully
- UI components display and function correctly
- Data persistence works as expected

#### 2.4. Cross-Functional Testing

Specialized QA verifies:
- **Accessibility:** WCAG compliance, screen reader compatibility
- **Performance:** Load times, resource usage, handling of large data sets
- **Security:** Authorization checks, data validation, injection protection
- **Usability:** Intuitive workflows, clear error messages, responsive design
- **Compatibility:** Browser/device compatibility, API versions

#### 2.5. Business Requirements Verification

Product managers verify:
- Implementation aligns with business goals
- User experience meets expectations
- Edge cases are handled appropriately from a business perspective
- Feature works in expected user scenarios

#### 2.6. User Acceptance Testing (UAT)

When applicable, stakeholders verify:
- Feature solves the intended problem
- Workflow makes sense from an end-user perspective
- No critical issues from a user perspective

### 3. Issue Tracking and Resolution

During QA:

- **Document Issues:**
  - Create sub-tickets for issues found
  - Categorize by severity (Critical, Major, Minor, Cosmetic)
  - Include clear reproduction steps and expected behavior

- **Prioritize Fixes:**
  - Critical and major issues block completion
  - Minor issues may be addressed in the current release or backlogged
  - Cosmetic issues can be logged for future improvements

- **Verification Process:**
  - Fixes are reviewed by the person who reported the issue
  - Issues are only closed when verified as fixed

Example issue template:

```
## QA Issue: [Brief Description]

**Severity:** [Critical/Major/Minor/Cosmetic]

**Environment:** 
- Browser/Device: [e.g., Chrome 95.0, iPhone 12]
- OS: [e.g., Windows 11, iOS 15]
- URL: [where the issue was found]

**Steps to Reproduce:**
1. [Clear step-by-step instructions]
2. [Include any specific test data]
3. [Include screenshots if helpful]

**Expected Behavior:**
[What should happen]

**Actual Behavior:**
[What actually happens]

**Additional Context:**
[Any other relevant information]
```

### 4. Regression Testing

After fixes are implemented:

- Run automated regression tests
- Perform manual verification of related functionality
- Check that fixes don't introduce new issues
- Verify that previously working features still function correctly

### 5. QA Sign-off

When all testing is complete:

- **Final QA Review:**
  - All test cases have been executed
  - Critical and major issues have been resolved
  - Any deferred issues are documented and approved for deferral

- **Documentation Update:**
  - Update user documentation if needed
  - Ensure API documentation is current
  - Check that release notes are accurate

- **Update Status:**
  - Update the Current State comment to reflect QA completion
  - Document any known issues that were accepted
  - Prepare for release if applicable

Example QA completion update:

```
## Current State

**Status:** QA Complete

**QA Results:**
- [x] Developer verification - Passed
- [x] Peer developer review - Passed with minor comments
- [x] Functional testing - Passed
- [x] Accessibility testing - Passed WCAG AA
- [x] Performance testing - Passed (response time < 200ms)
- [x] Product manager verification - Approved
- [x] User acceptance testing - Approved

**Known Issues:**
- Minor: Button alignment on mobile devices (PROJ-789)
- Cosmetic: Icon size inconsistency in dark mode (PROJ-790)

**Release Approval:**
Approved for production by @product-manager on [DATE]
```

## QA Best Practices

### Test Case Development

- **Cover Core Functionality:**
  - Happy path scenarios
  - Edge cases and boundary conditions
  - Error handling and recovery

- **Use Checklists:**
  - Standard functionality checklist
  - Feature-specific test cases
  - Non-functional requirements (performance, security)

- **Automate When Possible:**
  - Unit tests for core logic
  - Integration tests for component interaction
  - End-to-end tests for critical user flows

### QA Environment Management

- **Environment Parity:**
  - Test environments should mirror production
  - Database schemas should match production
  - Feature flags should be configured properly

- **Test Data Management:**
  - Use consistent test data sets
  - Reset data between tests when needed
  - Ensure sensitive data is not used in tests

### QA Documentation

- **Test Plans:**
  - Document test strategy
  - List test cases and expected results
  - Define test data requirements

- **QA Reports:**
  - Summarize test coverage
  - Document issues found and resolved
  - Provide recommendations for improvements

## QA for Different Types of Changes

### Feature QA

For new features:
- Verify against acceptance criteria
- Test integration with existing features
- Check for conflicts with other functionality
- Verify documentation is complete

### Bug Fix QA

For bug fixes:
- Verify the bug is actually fixed
- Check for regression in related functionality
- Ensure fix addresses root cause, not just symptoms
- Add regression tests to prevent future occurrences

### Performance Improvement QA

For performance changes:
- Measure before and after metrics
- Test with production-like data volumes
- Check impact on related functionality
- Verify there are no new bottlenecks

### UI/UX Change QA

For UI changes:
- Verify design implementation accuracy
- Test on various screen sizes and devices
- Check accessibility compliance
- Verify consistent user experience

## QA for AI-Assisted Implementation

When work has been implemented with AI assistance:

1. **Algorithm Verification:**
   - Verify the logic and algorithms are correct
   - Check for edge cases the AI might have missed
   - Ensure code is idiomatic and follows project conventions

2. **Integration Review:**
   - Pay extra attention to how AI-generated code integrates with existing code
   - Verify dependencies are correctly managed
   - Check that naming conventions are consistent

3. **Documentation Check:**
   - Ensure AI-generated documentation is accurate
   - Verify that code comments are helpful and accurate
   - Check that any generated tests are meaningful

## QA Completion and Transition to Reflection

Once QA is complete:

1. **Final Status Update:**
   - Mark all QA tasks as complete in the ticket
   - Update the Current State to reflect QA completion
   - Document any known issues or limitations

2. **Prepare for Reflection:**
   - Gather QA metrics and insights
   - Document lessons learned from testing
   - Prepare recommendations for future improvements

3. **Release Coordination:**
   - Communicate completion to release managers
   - Ensure deployment plans are in place
   - Update stakeholders on status

4. **Transition to Reflection:**
   - Schedule reflection meeting
   - Share QA results with the team
   - Begin reflection process

The QA process bridges implementation and reflection, ensuring that only quality-verified work is considered complete. Completing this workflow signals that the feature is ready for the reflection phase, where the team can evaluate the entire development process and identify improvement opportunities.

See [reflection-workflow.md](reflection-workflow.md) for the next phase.
