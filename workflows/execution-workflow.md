# Execution Workflow

This document outlines the recommended workflow for executing tickets, with a focus on structured implementation and continuous improvement.

For the standardized GitHub issue structure, see [../shared/github-issue-structure.md](../shared/github-issue-structure.md).

## Implementation Process

### 1. Branch Creation

- Always use ticket ID in branch name (e.g., `2-stripe-xero-integration` for issue #2)
- Reference the issue in commit messages

### 2. Test-Driven Development (TDD)

Follow the TDD cycle for implementation:

1. **Write a Failing Test:**
   - Start by writing a test that defines the expected behavior
   - Make it specific and focused on a single functionality
   - Run the test to confirm it fails (red phase)
   - Consider both the happy path and edge cases

2. **Implement the Minimal Solution:**
   - Write just enough code to make the test pass
   - Focus on functionality, not optimization
   - Run the test to confirm it passes (green phase)

3. **Refactor:**
   - Improve the implementation without changing behavior
   - Remove duplication and improve code clarity
   - Ensure tests still pass after refactoring
   - Consider performance optimizations if needed

4. **Repeat:**
   - Move to the next piece of functionality
   - Continue the red-green-refactor cycle

Example TDD workflow for a new feature:

```
# For a user export feature:

## Step 1: Write failing test for export service
- Write test that expects the service to format user data as PDF
- Run test to verify it fails appropriately

## Step 2: Implement minimum viable export service
- Create basic service with PDF formatter functionality
- Run test to verify it now passes

## Step 3: Refactor export service
- Improve code organization
- Extract reusable components
- Ensure tests still pass

## Step 4: Write failing test for API endpoint
- Write test that expects the API to return exported user data
- Run test to verify it fails

## Step 5: Implement the API endpoint
- Create minimal implementation to make the test pass
- Ensure previous tests still pass

(Continue cycle for each component)
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

4. **Cross-Cutting Concerns:**
   - Add error handling
   - Implement logging
   - Apply security measures
   - Address performance considerations

### 4. Continuous State Tracking

#### Retrieving GitHub Issues

For detailed instructions on accessing GitHub issues and their comments, refer to the [GitHub Issue Structure document](../shared/github-issue-structure.md#accessing-github-issues).

**IMPORTANT:** Always use GitHub CLI commands (like `gh issue view`) to access GitHub issues. Never attempt to locate GitHub issues by searching local directories.

#### Updating the Current State

Throughout implementation, continuously update the third comment in the GitHub issue:

- Keep the Current State comment up to date with implementation progress
- Check off completed tasks as they are finished
- Add newly discovered tasks if they emerge during implementation
- Document blockers and challenges as they appear
- Update the next steps section regularly
- Add relevant notes about discoveries or changes to the approach

Example state tracking updates:

```
## Current State

**Status:** In Progress

**Progress:**
- [x] Initial planning complete
- [x] Technical approach defined
- [x] Branch created: feature/PROJ-123-user-profile-export
- [x] Created failing tests for PDF export functionality
- [x] Implemented ExportService.js to pass tests
- [x] Refactored ExportService.js for better performance
- [ ] Writing tests for API endpoint (50% complete)
- [ ] Remaining tasks...

**Blockers:**
- Need design review for UI components
- Awaiting API permissions from DevOps team

**Next Steps:**
1. Complete API endpoint tests
2. Implement API endpoint to pass tests
3. Begin UI component test development

**Notes:**
- Discovered performance issue with large data sets - implementing chunking approach
- Need to adjust rate limiting for production deployment
```

### 5. Continuous Improvement

During implementation:

- Note any discoveries that differ from the technical plan
- Update the technical plan comment if implementation details change
- Document any new improvement opportunities discovered during implementation
- Create separate tickets for improvements beyond the scope of the current work

### 6. PR Creation and Review

- Create PR with reference to ticket ID
- Ensure PR description references the Technical Plan
- Address review comments promptly
- Update the Current State comment to reflect PR review status

### 7. Completion

- Verify all checklist items are complete
- Ensure all tests pass
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