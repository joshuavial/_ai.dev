# Test-Driven Development Protocol

This protocol outlines the standardized process for implementing features using Test-Driven Development (TDD) principles. Follow this protocol whenever implementing new code or fixing bugs to ensure proper test coverage and quality.

## TDD Cycle Overview

Every feature or bug fix implementation must follow the three-phase TDD cycle:

```
┌──────────┐     ┌──────────┐     ┌────────────┐
│   RED    │ ──▶ │  GREEN   │ ──▶ │  REFACTOR  │
│  (Fail)  │     │  (Pass)  │     │ (Improve)  │
└──────────┘     └──────────┘     └────────────┘
      ▲                                  │
      └──────────────────────────────────┘
```

## RED Phase Requirements

The RED phase establishes test criteria before any implementation:

1. **Write Tests First**
   - Create unit/integration tests that define expected behavior
   - Do NOT implement actual functionality yet
   - Focus on clear assertions and edge cases
   - Include both happy path and error condition tests

2. **Run Tests to Verify Failure**
   - Ensure tests fail for the expected reason
   - Verify error messages are meaningful
   - Tests should fail because functionality is missing, not because tests are incorrect

3. **Document RED Phase Evidence**
   - Capture test execution results with screenshots or logs
   - Store in `_ai/tdd/[issue-number]-tdd-evidence/[component]-red.[ext]`
   - Include date and test execution details

4. **RED Phase Checklist**
   - [ ] Tests define expected behavior clearly
   - [ ] Tests include edge cases and error conditions
   - [ ] Tests fail for expected reasons
   - [ ] Test failure is documented with evidence
   - [ ] No implementation code has been written yet

## GREEN Phase Requirements

The GREEN phase implements minimal code to make tests pass:

1. **Write Minimal Implementation**
   - Focus on making tests pass with simplest solution
   - Do not worry about optimization or elegance yet
   - Resist adding features or code not required by tests
   - Follow language and framework best practices

2. **Run Tests to Verify Success**
   - All tests should now pass
   - If any tests still fail, continue implementation
   - Do not proceed to refactoring until all tests pass

3. **Document GREEN Phase Evidence**
   - Capture successful test execution with screenshots or logs
   - Store in `_ai/tdd/[issue-number]-tdd-evidence/[component]-green.[ext]`
   - Include date and test execution details

4. **GREEN Phase Checklist**
   - [ ] Implementation is minimal and focused on test requirements
   - [ ] All tests pass consistently
   - [ ] No premature optimization or extra features added
   - [ ] Test success is documented with evidence

## REFACTOR Phase Requirements

The REFACTOR phase improves implementation while maintaining passing tests:

1. **Improve Implementation**
   - Refactor for readability, performance, and maintainability
   - Apply design patterns where appropriate
   - Remove code duplication and improve names
   - Adhere to project code style and standards

2. **Run Tests After Each Change**
   - Tests must continue to pass after each refactoring step
   - If tests break, revert the change or fix the issue immediately
   - Do not add new functionality during refactoring

3. **Run Complete Test Suite**
   - Before completing refactoring, run the full test suite
   - Ensure no regressions have been introduced
   - Verify the feature works with the rest of the system

4. **Document REFACTOR Phase Evidence**
   - Capture final test execution after refactoring
   - Store in `_ai/tdd/[issue-number]-tdd-evidence/[component]-refactor.[ext]`
   - Include date and test execution details

5. **REFACTOR Phase Checklist**
   - [ ] Code quality improved without changing behavior
   - [ ] All tests still pass consistently
   - [ ] No regressions introduced
   - [ ] Project coding standards applied
   - [ ] Final test execution documented with evidence

## TDD Documentation Requirements

All TDD activities must be documented in the KB state file:

1. **Progress Tracking Format**
   ```markdown
   ## TDD Progress
   
   ### [Component Name]
   - [x] RED phase - [Evidence](_ai/tdd/123-tdd-evidence/component-red.png)
   - [x] GREEN phase - [Evidence](_ai/tdd/123-tdd-evidence/component-green.png)
   - [x] REFACTOR phase - [Evidence](_ai/tdd/123-tdd-evidence/component-refactor.png)
   ```

2. **Required Documentation Elements**
   - Track RED/GREEN/REFACTOR status for each component
   - Include evidence links for each TDD phase
   - Document current test coverage for completed components
   - Flag any components that don't have complete TDD cycle
   - Update GitHub issue Current State with TDD progress

3. **TDD Evidence Requirements**
   - Every major component should have its own evidence files
   - Evidence should clearly show test results (passed/failed)
   - File naming must follow the `[component]-[phase].[ext]` pattern
   - Store all evidence in dedicated issue folder: `_ai/tdd/[issue-number]-tdd-evidence/`

## Implementation Process

Follow this process when implementing a feature with TDD:

1. **Break Down Into Components**
   - Identify discrete components to implement
   - Prioritize components by dependencies
   - Plan test coverage for each component

2. **For Each Component**
   - Complete full RED-GREEN-REFACTOR cycle
   - Document each phase with evidence
   - Update KB state file after completing each phase
   - Do not move to next component until cycle is complete

3. **Integration Testing**
   - After all components are implemented, add integration tests
   - Verify components work together correctly
   - Document any issues found during integration

4. **Final Verification**
   - Run complete test suite one final time
   - Update overall test coverage metrics
   - Ensure all TDD evidence is properly documented
   - Update GitHub issue Current State with completion status

## Example TDD Implementation

```markdown
# Implementing UserProfileComponent

## RED Phase
1. Created test: should display user name and email
2. Created test: should show edit button for owner
3. Created test: should hide edit button for non-owner
4. Ran tests: All failing as expected [screenshot link]

## GREEN Phase
1. Implemented UserProfileComponent with conditional edit button
2. Ran tests: All passing [screenshot link]

## REFACTOR Phase
1. Extracted permission check to utility function
2. Improved responsive layout
3. Added accessibility attributes
4. Ran tests: All still passing [screenshot link]
```

## Common TDD Pitfalls to Avoid

1. **Test After Coding**
   - Writing tests after implementing features defeats purpose of TDD
   - Always write tests before implementation

2. **Incomplete Test Coverage**
   - Missing edge cases or error conditions
   - Test only happy paths, missing potential bugs

3. **Overly Specific Tests**
   - Tests so tied to implementation they break with refactoring
   - Focus tests on behavior, not implementation details

4. **Missing Refactoring**
   - Skipping refactor phase after making tests pass
   - Technical debt accumulates without proper refactoring

5. **Premature Optimization**
   - Trying to optimize during GREEN phase
   - Focus first on correctness, then on performance

By following this TDD protocol, you will ensure high-quality, well-tested code with clear documentation of the development process.