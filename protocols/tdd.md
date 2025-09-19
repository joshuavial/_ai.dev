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

3. **Track RED Phase**
   - Run tests and confirm they fail for the expected reasons
   - Note which tests were written and what functionality they verify
   - Focus on behavior coverage rather than collecting evidence

4. **RED Phase Checklist**
   - [ ] Tests define expected behavior clearly
   - [ ] Tests include edge cases and error conditions
   - [ ] Tests fail for expected reasons
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

3. **Track GREEN Phase**
   - Verify tests now pass with minimal implementation
   - Note the implementation approach taken
   - Ensure implementation focuses only on making tests pass

4. **GREEN Phase Checklist**
   - [ ] Implementation is minimal and focused on test requirements
   - [ ] All tests pass consistently
   - [ ] No premature optimization or extra features added

## REFACTOR Phase Requirements

The REFACTOR phase improves implementation while maintaining passing tests:

1. **Improve Implementation**
   - Refactor for readability, performance, and maintainability
   - Apply design patterns where appropriate
   - Remove code duplication and improve names
   - Adhere to project code style and standards
   - **CRITICAL**: Always modify files in place - NEVER create new versions with suffixes like .refactored or .enhanced

2. **Run Tests After Each Change**
   - Tests must continue to pass after each refactoring step
   - If tests break, revert the change or fix the issue immediately
   - Do not add new functionality during refactoring

3. **Run Complete Test Suite**
   - Before completing refactoring, run the full test suite
   - Ensure no regressions have been introduced
   - Verify the feature works with the rest of the system

4. **Measure Test Coverage**
   - Run code coverage tools to assess coverage percentage
   - Identify any gaps in test coverage
   - Document the final coverage metrics for the component

5. **File Management Requirements**
   - Always refactor the original file directly in place
   - Never create alternate versions with different filenames
   - Use version control (git) to track changes, not separate files
   - Maintain a single source of truth for each component

6. **REFACTOR Phase Checklist**
   - [ ] Code quality improved without changing behavior
   - [ ] All tests still pass consistently
   - [ ] No regressions introduced
   - [ ] Project coding standards applied
   - [ ] Test coverage metrics documented
   - [ ] Original files modified directly (no .refactored/.enhanced versions)

## TDD Documentation Requirements

Track TDD progress inside the task folder (e.g., `technical-plan.md` Current State section or `tdd-evidence/` notes):

1. **Progress Tracking Format**
   ```markdown
   ## TDD Progress
   
   ### [Component Name]
   - [x] RED phase - Tests written for feature X, Y, Z
   - [x] GREEN phase - Implementation complete
   - [x] REFACTOR phase - Coverage: 85%
   ```

2. **Required Documentation Elements**
   - Track RED/GREEN/REFACTOR status for each component
   - List what functionality each test covers
   - Document current test coverage percentage for completed components
   - Flag any components that don't have complete TDD cycle
   - Update `status.md` with TDD progress

3. **Coverage Reporting**
   - Document test coverage percentages in task notes
   - Focus on meaningful coverage (not just line coverage)
   - Identify any critical areas that need additional testing
   - Note any technical limitations in coverage reporting

## Implementation Process

Follow this process when implementing a feature with TDD:

1. **Break Down Into Components**
   - Identify discrete components to implement
   - Prioritize components by dependencies
   - Plan test coverage for each component

2. **For Each Component**
   - Complete full RED-GREEN-REFACTOR cycle
   - Track progress in the task's Current State section
   - Record coverage metrics after refactoring
   - Do not move to next component until cycle is complete

3. **Integration Testing**
   - After all components are implemented, add integration tests
   - Verify components work together correctly
   - Document any issues found during integration

4. **Final Verification**
   - Run complete test suite one final time
   - Update overall test coverage metrics
   - Ensure coverage meets project standards
   - Update the task's Current State with completion status

## Example TDD Implementation

```markdown
# Implementing UserProfileComponent

## RED Phase
1. Created test: should display user name and email
2. Created test: should show edit button for owner
3. Created test: should hide edit button for non-owner
4. Ran tests: All failing as expected

## GREEN Phase
1. Implemented UserProfileComponent with conditional edit button
2. Ran tests: All passing

## REFACTOR Phase
1. Extracted permission check to utility function
2. Improved responsive layout
3. Added accessibility attributes
4. Ran tests: All still passing
5. Current coverage: Component - 92%, Utils - 87%
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

By following this TDD protocol, you will ensure high-quality, well-tested code with measurable test coverage.
