# Execution Workflow

Before beginning any implementation work, follow the complete [Orientation Protocol](../shared/orientate.md) to understand your context.

This workflow should be used in conjunction with the [Task Management Guidelines](../shared/task-management.md) for implementing and tracking tasks.

This document outlines the recommended workflow for executing tickets, with a focus on structured implementation and continuous improvement.

For the standardized GitHub issue structure, see [../shared/github-issue-structure.md](../shared/github-issue-structure.md).

## Implementation Process

### 1. Branch Creation

- Always use ticket ID in branch name (e.g., `2-stripe-xero-integration` for issue #2)
- Reference the issue in commit messages

### 2. Test-Driven Development (TDD)

You MUST follow the TDD cycle for implementation. This is a strict requirement, not a suggestion. Each cycle must be documented with evidence of test execution.

1. **RED Phase (Write Failing Tests):**
   - Start by writing a test that defines the expected behavior before any implementation
   - Make it specific and focused on a single functionality
   - Run the test to confirm it fails (red phase)
   - REQUIRED: Take a screenshot or save logs showing the failing test
   - Document the failing test in the Current State comment
   - Consider both the happy path and edge cases

2. **GREEN Phase (Implement Minimal Solution):**
   - Write just enough code to make the test pass - no more, no less
   - Focus on functionality, not optimization
   - Run the test to confirm it passes (green phase)
   - REQUIRED: Take a screenshot or save logs showing the passing test
   - Document the passing test in the Current State comment
   - Do not proceed to refactoring until tests pass

3. **REFACTOR Phase:**
   - Improve the implementation without changing behavior
   - Remove duplication and improve code clarity
   - Run tests after each significant refactoring step
   - REQUIRED: Take a screenshot or save logs showing tests still pass after refactoring
   - Document the refactoring in the Current State comment
   - Consider performance optimizations if needed
   - CRITICAL: You MUST run the complete test suite after completing all refactoring to ensure no regressions were introduced

4. **Verification Requirements:**
   - You MUST have evidence of all three phases for each component
   - Each phase must be documented in the Current State comment
   - You CANNOT skip any phase of the cycle
   - You CANNOT implement code without first writing failing tests
   - You MUST run tests after each phase and document the results
   - You MUST run the complete test suite after finishing ALL implementation tasks
   - CRITICAL: Never consider a task complete until all tests pass successfully

5. **Repeat:**
   - Move to the next piece of functionality
   - Continue the red-green-refactor cycle with full documentation

Example TDD workflow with required documentation for a new feature:

```
# For a user export feature:

## Component: ExportService PDF Formatter

### Step 1: RED - Write failing test for export service
- Write test that expects the service to format user data as PDF
- Run test suite with `npm test`
- VERIFY: Test fails with expected error message
- DOCUMENT: Screenshot showing failing test in terminal
- UPDATE CURRENT STATE: Mark "Tests written (RED ✓)" with link to evidence

### Step 2: GREEN - Implement minimum viable export service
- Create basic service with PDF formatter functionality
- Run test suite with `npm test`
- VERIFY: Test now passes
- DOCUMENT: Screenshot showing passing test in terminal
- UPDATE CURRENT STATE: Mark "Implementation complete (GREEN ✓)" with link to evidence

### Step 3: REFACTOR - Improve export service
- Improve code organization
- Extract reusable components
- Run test suite with `npm test` after each significant change
- VERIFY: Tests still pass after refactoring
- DOCUMENT: Screenshot of passing tests and code coverage
- UPDATE CURRENT STATE: Mark "Code refactored (REFACTOR ✓)" with link to evidence

## Component: API Endpoint

### Step 4: RED - Write failing test for API endpoint
- Write test that expects the API to return exported user data
- Run test suite with `npm test`
- VERIFY: Test fails with expected error message
- DOCUMENT: Screenshot showing failing test in terminal
- UPDATE CURRENT STATE: Mark "Tests written (RED ✓)" with link to evidence

### Step 5: GREEN - Implement the API endpoint
- Create minimal implementation to make the test pass
- Run test suite with `npm test`
- VERIFY: Test now passes
- DOCUMENT: Screenshot showing passing test in terminal
- UPDATE CURRENT STATE: Mark "Implementation complete (GREEN ✓)" with link to evidence

### Step 6: REFACTOR - Improve API endpoint
- Refactor for error handling and edge cases
- Run test suite with `npm test` after changes
- VERIFY: Tests still pass after refactoring
- DOCUMENT: Screenshot of passing tests and code coverage
- UPDATE CURRENT STATE: Mark "Code refactored (REFACTOR ✓)" with link to evidence

(Continue cycle for each component with documentation of all three phases)
```

### 3. Implementation Flow

Follow this sequence for most efficient implementation:

1. **Core Domain Logic First:**
   - Begin with core business logic and domain models
   - Focus on the fundamental behavior before UI concerns

2. **Infrastructure and Integration:**
   - Implement data persistence
   - Add API endpoints and integrations
   - Connect to external services

3. **User Interface Components:**
   - Build UI components
   - Integrate with back-end services
   - Implement user interactions
   - **IMPORTANT**: When using Playwright for browser automation, strictly follow the [Playwright Management Guidelines](../shared/playwright-management.md)

4. **Cross-Cutting Concerns:**
   - Add error handling
   - Implement logging
   - Apply security measures
   - Address performance considerations

**CRITICAL REQUIREMENT**: When using Playwright, you MUST immediately stop execution if any Playwright operation fails. Never attempt to install browsers as they are already installed. See [Playwright Management Guidelines](../shared/playwright-management.md) for complete details.

### 4. TDD Verification and State Tracking

#### Retrieving GitHub Issues

For detailed instructions on accessing GitHub issues and their comments, refer to the [GitHub Issue Structure document](../shared/github-issue-structure.md#accessing-github-issues).

**IMPORTANT:** Always use GitHub CLI commands (like `gh issue view`) to access GitHub issues. Never attempt to locate GitHub issues by searching local directories.

#### Required TDD Evidence

You MUST collect and document the following evidence for each component:

1. **RED Phase Evidence:**
   - Screenshot or log output showing test failure
   - Commit with failing test code
   - Documentation of specific test cases written

2. **GREEN Phase Evidence:**
   - Screenshot or log output showing test passing
   - Commit with implementation code
   - Documentation of implementation approach

3. **REFACTOR Phase Evidence:**
   - Screenshot or log output showing tests passing after refactoring
   - Commit with refactored code
   - Documentation of improvements made

#### Updating the Current State

Throughout implementation, continuously update the third comment in the GitHub issue with explicit TDD verification:

- Maintain a "Test Status" section tracking RED/GREEN/REFACTOR for each component
- Include links to evidence (screenshots/logs) for each TDD phase
- Document current test coverage for completed components
- Update the status after completing each phase of the TDD cycle
- Check off completed tasks as they are finished
- Add newly discovered tasks if they emerge during implementation
- Document blockers and challenges as they appear
- Update the next steps section regularly
- Add relevant notes about discoveries or changes to the approach

Example state tracking updates with explicit TDD documentation:

```
## Current State

**Status:** In Progress

**Progress:**
- [x] Initial planning complete
- [x] Technical approach defined
- [x] Branch created: feature/PROJ-123-user-profile-export

**Test Status:**
- **ExportService PDF formatter:**
  - [x] Tests written (RED ✓) - [Link to screenshot/log showing failing tests]
  - [x] Implementation complete (GREEN ✓) - [Link to screenshot/log showing passing tests]
  - [x] Code refactored (REFACTOR ✓) - [Link to screenshot/log showing tests passing after refactoring]
  - Current coverage: 92%

- **ExportService CSV formatter:**
  - [x] Tests written (RED ✓) - [Link to screenshot/log showing failing tests]
  - [ ] Implementation complete (GREEN ⬤)
  - [ ] Code refactored (REFACTOR ⬤)
  - Current coverage: N/A

- **UserProfileController export endpoint:**
  - [ ] Tests written (RED ⬤)
  - [ ] Implementation complete (GREEN ⬤)
  - [ ] Code refactored (REFACTOR ⬤)
  - Current coverage: N/A

**Blockers:**
- Need design review for UI components
- Awaiting API permissions from DevOps team

**Next Steps:**
1. Complete implementation of CSV formatter to pass tests
2. Document test results with screenshots
3. Begin test writing for API endpoint

**Notes:**
- Discovered performance issue with large data sets - implementing chunking approach
- Added additional test cases for exotic character handling in CSV export
- Current test suite run time: 3.45s
```

### 5. Continuous Improvement

During implementation:

- Note any discoveries that differ from the technical plan
- Update the technical plan comment if implementation details change
- Document any new improvement opportunities discovered during implementation
- Create separate tickets for improvements beyond the scope of the current work

### 6. PR Creation and Review

- MANDATORY: Run the complete test suite before creating the PR
- Create PR with reference to ticket ID
- Ensure PR description references the Technical Plan
- Include evidence of TDD compliance in the PR description
- Verify and document that all tests are passing with screenshots/logs
- Include test coverage reports in the PR
- Address review comments promptly
- Run tests after addressing review comments to verify no regressions
- Update the Current State comment to reflect PR review status
- If any tests fail during review, follow the TDD cycle to fix them

### 7. Completion

- Verify all checklist items are complete
- MANDATORY: Run the complete test suite to ensure ALL tests pass before considering the task complete
- Document evidence of passing tests with screenshots or logs
- Update documentation if needed
- Do a final update to the Current State comment with completion status
- Close ticket with reference to the PR
- Proceed to the QA workflow for final quality verification

## TDD Implementation Examples

### Example 1: API Endpoint TDD Approach

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
// In UserProfileController.js
export const exportUserProfile = async (req, res) => {
  const { id } = req.params;
  const { format } = req.query;
  
  const userData = await userService.getUserById(id);
  const exportedData = await exportService.formatUserData(userData, format);
  
  res.setHeader('Content-Type', format === 'pdf' ? 'application/pdf' : 'text/csv');
  return res.send(exportedData);
};

// Step 3: Refactor for better organization, error handling, etc.
```

### Example 2: UI Component TDD with React Testing Library

```javascript
// Step 1: Write failing test for ExportButton component
test('ExportButton shows format options when clicked', () => {
  // Arrange
  render(<ExportButton userId="123" />);
  
  // Act
  const button = screen.getByRole('button', { name: /export/i });
  fireEvent.click(button);
  
  // Assert
  expect(screen.getByText('PDF')).toBeInTheDocument();
  expect(screen.getByText('CSV')).toBeInTheDocument();
});

// Step 2: Implement minimum component to pass test
function ExportButton({ userId }) {
  const [showOptions, setShowOptions] = useState(false);
  
  return (
    <div>
      <button onClick={() => setShowOptions(true)}>Export</button>
      {showOptions && (
        <div>
          <span>PDF</span>
          <span>CSV</span>
        </div>
      )}
    </div>
  );
}

// Step 3: Refactor for better styling, accessibility, etc.
```

## Working with Sub-Tickets

When implementing a feature that has been broken down into sub-tickets:

1. **Coordinated Implementation:**
   - Start with the parent ticket's technical plan as your overall guide
   - Focus implementation on the specific scope defined in the sub-ticket
   - Update progress in both the sub-ticket and parent ticket's Current State

2. **Integration Points:**
   - Pay special attention to integration points between sub-ticket implementations
   - Document any interface contracts or shared dependencies clearly
   - Use consistent naming and patterns across all sub-ticket implementations

3. **Completion Process:**
   - Mark sub-tickets as complete individually
   - Update the parent ticket's Current State to reflect progress
   - Only close the parent ticket when all sub-tickets are complete and integrated

## AI Implementation Guidelines

When working with AI tools to implement features:

1. **Clear Context:** Provide full PRD links and ticket details
2. **GitHub Issue Access:** When referencing GitHub issues, always use the GitHub CLI commands described in the [GitHub Issue Structure document](../shared/github-issue-structure.md#accessing-github-issues)
3. **Chunked Tasks:** Break down implementation into small, manageable steps
4. **Sequential Progression:** Complete one logical component before moving to the next
5. **Test-First Approach:** Define expected behavior before implementation
6. **Iterative Review:** Check work at logical milestones, not just at completion
7. **API Documentation:** Provide clear API specs and examples when relevant
8. **Visual Guidance:** Include mockups or diagrams for UI components
9. **Error Handling:** Explicitly define expected error scenarios
10. **Reference Examples:** Point to similar existing implementations when available
11. **Status Updates:** Request progress updates at logical checkpoints

Following this structured approach ensures thorough implementation and reduces back-and-forth cycles.

For information on quality verification, see [qa-workflow.md](qa-workflow.md).