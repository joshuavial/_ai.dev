# Execution Workflow

This document outlines the process for executing tickets with a focus on structured implementation using Test-Driven Development (TDD).

## Implementation Process

### 1. Branch Creation

- Use ticket ID in branch name (e.g., `2-stripe-xero-integration` for issue #2)
- Reference the issue in commit messages

### 2. Test-Driven Development (TDD)

Follow the TDD cycle for all implementation:

1. **RED Phase (Write Failing Tests):**
   - Write tests that define expected behavior before implementation
   - Run tests to confirm they fail
   - Document the failing tests in the Current State comment

2. **GREEN Phase (Implement Minimal Solution):**
   - Write just enough code to make tests pass
   - Run tests to confirm they pass
   - Document the passing tests in the Current State comment

3. **REFACTOR Phase:**
   - Improve implementation without changing behavior
   - Run tests after each significant refactoring
   - Document the refactoring in the Current State comment
   - Run the complete test suite to ensure no regressions

Example TDD workflow:

```
## Component: ExportService PDF Formatter

### Step 1: RED - Write failing test for export service
- Write test that expects the service to format user data as PDF
- Run test suite with `npm test`
- VERIFY: Test fails with expected error message
- UPDATE CURRENT STATE: Mark "Tests written (RED ✓)"

### Step 2: GREEN - Implement minimum viable export service
- Create basic service with PDF formatter functionality
- Run test suite with `npm test`
- VERIFY: Test now passes
- UPDATE CURRENT STATE: Mark "Implementation complete (GREEN ✓)"

### Step 3: REFACTOR - Improve export service
- Improve code organization
- Extract reusable components
- Run test suite with `npm test` after each significant change
- VERIFY: Tests still pass after refactoring
- UPDATE CURRENT STATE: Mark "Code refactored (REFACTOR ✓)"
```

### 3. Implementation Flow

Follow this sequence for efficient implementation:

1. **Core Domain Logic First:** Begin with business logic and models
2. **Infrastructure and Integration:** Add data persistence, API endpoints
3. **User Interface Components:** Build UI and user interactions
4. **Cross-Cutting Concerns:** Add error handling, logging, security

### 4. State Tracking

Continuously update the Current State comment in the GitHub issue:

- Maintain a "Test Status" section tracking RED/GREEN/REFACTOR for each component
- Document current test coverage for completed components
- Update the status after completing each phase
- Check off completed tasks
- Document blockers and challenges
- Update the next steps section regularly

Example state tracking:

```
## Current State

**Status:** In Progress

**Progress:**
- [x] Initial planning complete
- [x] Technical approach defined
- [x] Branch created: feature/PROJ-123-user-profile-export

**Test Status:**
- **ExportService PDF formatter:**
  - [x] Tests written (RED ✓)
  - [x] Implementation complete (GREEN ✓)
  - [x] Code refactored (REFACTOR ✓)
  - Current coverage: 92%

- **ExportService CSV formatter:**
  - [x] Tests written (RED ✓)
  - [ ] Implementation complete (GREEN ⬤)
  - [ ] Code refactored (REFACTOR ⬤)
  - Current coverage: N/A

**Blockers:**
- Need design review for UI components

**Next Steps:**
1. Complete implementation of CSV formatter
2. Begin test writing for API endpoint
```

### 5. Continuous Improvement

During implementation:

- Note discoveries that differ from the technical plan
- Update the technical plan if implementation details change
- Document new improvement opportunities
- Create separate tickets for out-of-scope improvements

### 6. PR Creation and Review

- Run the complete test suite before creating the PR
- Create PR with reference to ticket ID
- Include evidence of TDD compliance in the PR description
- Verify and document that all tests are passing
- Include test coverage reports
- Address review comments promptly
- Run tests after addressing review comments

### 7. Completion

- Verify all checklist items are complete
- Run the complete test suite to ensure ALL tests pass
- Update documentation if needed
- Do a final update to the Current State comment
- Close ticket with reference to the PR
- Proceed to the QA workflow

## TDD Implementation Examples

### Example 1: API Endpoint

```javascript
// Step 1: Write a failing test
test('GET /api/users/:id/export should return PDF of user profile', async () => {
  // Arrange
  const userId = '123';
  const format = 'pdf';
  
  // Act
  const response = await request(app)
    .get(`/api/users/${userId}/export?format=${format}`)
    .set('Authorization', `Bearer ${validToken}`);
  
  // Assert
  expect(response.status).toBe(200);
  expect(response.headers['content-type']).toContain('application/pdf');
  expect(response.body).toBeTruthy();
});

// Step 2: Implement minimum code to pass
export const exportUserProfile = async (req, res) => {
  const { id } = req.params;
  const { format } = req.query;
  
  const userData = await userService.getUserById(id);
  const exportedData = await exportService.formatUserData(userData, format);
  
  res.setHeader('Content-Type', format === 'pdf' ? 'application/pdf' : 'text/csv');
  return res.send(exportedData);
};

// Step 3: Refactor for better organization and error handling
```

## Working with Sub-Tickets

When implementing features broken down into sub-tickets:

1. Use the parent ticket's technical plan as your overall guide
2. Focus implementation on the specific sub-ticket scope
3. Pay attention to integration points between sub-ticket implementations
4. Mark sub-tickets as complete individually
5. Only close the parent ticket when all sub-tickets are complete

## Next Steps

After completing execution:

1. Ensure all implementation is complete
2. Run the full test suite
3. Verify all items in the technical plan are addressed
4. Proceed to the QA workflow

See [qa-workflow.md](qa-workflow.md) for the next phase.