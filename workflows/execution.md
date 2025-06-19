# Execution Workflow

# IMPORTANT: Read Boot Protocol First
> **CRITICAL**: When adopting this workflow, you MUST first read the core instructions and follow the boot protocol to ensure proper initialization. Do NOT skip this step as it's essential for correct workflow operation.
>
> 1. Read core instructions: `_ai.bws/core-instructions.md`
> 2. Follow boot protocol: `_ai.bws/protocols/boot.md`
> 3. Only then proceed with this workflow

**Primary Protocol**: File: `_ai.bws/workflows/execution.md`
**Required Protocols**: 
- Boot Protocol (File: `_ai.bws/protocols/boot.md`)
- Tasks Protocol (File: `_ai.bws/protocols/tasks.md`)
- Issue Protocol (File: `_ai.bws/protocols/issue.md`)
- TDD Protocol (File: `_ai.bws/protocols/tdd.md`)
**Activation**: `workflow execution`

This document outlines the recommended workflow for executing tickets, with a focus on structured implementation using Test-Driven Development (TDD) and continuous improvement. Execution works from task folders created by the planning workflow.

> Note: Always begin by activating the Boot Protocol to establish context before starting implementation work.

## Task Folder Structure

Execution operates from task folders with this structure:
```
_ai/tasks/[issue-id]-[task-name]/
├── technical-plan.md    # Input: planning objectives and approach
├── qa-report.md         # Output: QA feedback (created by QA workflow)
├── implementation.md    # Optional: detailed execution notes
├── tdd-evidence/        # Optional: test artifacts and screenshots
└── artifacts/           # Optional: other task-related files
```

**Primary Document**: `technical-plan.md` contains objectives, approach, and Current State tracking

**Note**: Execution workflow can operate on multiple tasks simultaneously. The developer manages task priorities and context switching as needed.

## Implementation Process

### 1. Branch Creation

- Always use ticket ID in branch name (e.g., `2-stripe-xero-integration` for issue #2)
- Reference the issue in commit messages

### 2. Test-Driven Development (TDD)

You MUST follow the TDD cycle for implementation. This is a strict requirement, not a suggestion. Each cycle must be documented with evidence of test execution.

#### API Testing Requirements

**CRITICAL**: When testing API endpoints, you MUST write integration tests that connect to a test database. NEVER write unit tests that mock database calls for API testing.

- **Integration Tests Only**: All API tests must use real database connections
- **Test Database**: Use the test database environment, never mock database interactions
- **Real Dependencies**: API tests should exercise the full stack including database operations
- **Database State**: Set up and tear down test data as needed for each test
- **Mock External APIs**: DO mock external API calls (e.g., Stripe, Xero, email services) to avoid dependencies on external services
- **Example**: Use `poetry pytest tests/test_api/` which connects to the test database

**What to Mock vs Not Mock**:
- ✅ **DO Mock**: External API calls (Stripe, Xero, email services, third-party APIs)
- ❌ **DON'T Mock**: Database calls, internal application logic, data models
- ✅ **DO Mock**: Network requests to external services
- ❌ **DON'T Mock**: Internal service-to-service communication within the application

**Forbidden Approach**: Do not create unit tests that mock out database calls when testing API endpoints. This defeats the purpose of verifying the complete API functionality.

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
   - **IMPORTANT**: When using Playwright for browser automation, strictly follow the Playwright Protocol (File: `_ai.bws/protocols/playwright.md`)

4. **Cross-Cutting Concerns:**
   - Add error handling
   - Implement logging
   - Apply security measures
   - Address performance considerations

**CRITICAL REQUIREMENT**: When using Playwright, you MUST immediately stop execution if any Playwright operation fails. Never attempt to install browsers as they are already installed. See Playwright Protocol (File: `_ai.bws/protocols/playwright.md`) for complete details.

### 4. TDD Verification and State Tracking

#### Retrieving GitHub Issues

For detailed instructions on accessing GitHub issues and their comments, refer to the Issue Protocol (File: `_ai.bws/protocols/issue.md`), section "Accessing GitHub Issues".

**IMPORTANT:** Always use GitHub CLI commands (like `gh issue view`) to access GitHub issues. Never attempt to locate GitHub issues by searching local directories.

#### TDD Tracking Requirements

You MUST track the following for each component:

1. **RED Phase Tracking:**
   - List specific test cases written
   - Note which functionality each test covers
   - Document behavior coverage rather than collecting evidence

2. **GREEN Phase Tracking:**
   - Document the implementation approach taken
   - Verify all tests pass with minimal implementation
   - Ensure implementation focuses only on making tests pass

3. **REFACTOR Phase Tracking:**
   - Record code improvements made
   - Measure and document test coverage percentage
   - Identify any gaps in test coverage

#### Updating the Current State

Throughout implementation, continuously update the Current State section in `technical-plan.md`:

**Update Current State Section**:
- Maintain "Test Status" tracking RED/GREEN/REFACTOR for each component
- Document which tests were written and what they verify  
- Record test coverage percentages after refactoring
- Update status after completing each phase of the TDD cycle
- Check off completed tasks as they are finished
- Add newly discovered tasks if they emerge during implementation
- Document blockers and challenges as they appear
- Update next steps section regularly
- Add relevant notes about discoveries or changes to the approach

**Optional Implementation Notes**:
- Create `implementation.md` for detailed execution notes and discoveries
- Store TDD artifacts in `tdd-evidence/` subfolder
- Store other task files in `artifacts/` subfolder

#### File Management Verification

Throughout implementation, you MUST follow these file management requirements:

1. **Direct File Modification**
   - Always modify original files in place when making changes
   - NEVER create new versions of files with suffixes like .updated, .new, .enhanced, .refactored, etc.
   - Use version control (git) to track changes, not separate files

2. **Verification Steps Before Committing**
   - Before committing changes, verify no duplicate files have been created
   - Check that all modifications have been made to the original files
   - Remove any temporary files that may have been created during development

3. **Refactoring Guidelines**
   - When refactoring code, always modify the original file directly
   - Do not create new "refactored" or "enhanced" versions
   - Use proper testing to ensure changes maintain expected behavior
   - Document refactoring in the Current State comment, not in separate files

4. **Required File Management Checks**
   - After completing each TDD cycle, verify no duplicate files exist
   - Before creating PRs, run a check for any files with suffixes indicating they are alternate versions
   - If duplicate files are discovered, consolidate changes into the original file and remove the duplicates

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
  - [x] Tests written (RED ✓) - Tests cover basic PDF generation, headers, and error cases
  - [x] Implementation complete (GREEN ✓) - Minimal implementation passes all tests
  - [x] Code refactored (REFACTOR ✓) - Coverage: 92%

- **ExportService CSV formatter:**
  - [x] Tests written (RED ✓) - Tests cover data formatting, special characters, and large datasets
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
2. Implement CSV formatter and ensure test coverage
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
- MANDATORY: Verify that all code modifications have been made directly to original files, with no alternate versions created with suffixes like .updated, .new, .enhanced, .refactored, etc.
- Address review comments promptly
- Run tests after addressing review comments to verify no regressions
- Update the Current State comment to reflect PR review status
- If any tests fail during review, follow the TDD cycle to fix them

### 7. Completion

- Verify all checklist items in `technical-plan.md` are complete
- MANDATORY: Run the complete test suite to ensure ALL tests pass before considering the task complete
- Document evidence of passing tests in `tdd-evidence/` folder
- Update documentation if needed
- Perform a final check to ensure all changes were made to original files directly, with no alternate versions created with suffixes like .updated, .new, .enhanced, .refactored, etc.
- Update Current State section in `technical-plan.md` to "Ready for QA"
- Close ticket with reference to the PR
- Proceed to the QA workflow for final quality verification

### 8. QA Response Process

When QA returns work with issues (creates `qa-report.md` in task folder):

#### QA Document Review
- Read the `qa-report.md` in the task folder
- Review all Critical and Recommended action items
- Update Current State in `technical-plan.md` to "Addressing QA Feedback"

#### Issue Resolution Approach
1. **Critical Issues (Must Fix)**
   - Address these using full TDD cycle (RED-GREEN-REFACTOR)
   - Update `qa-report.md` by checking off resolved items
   - Re-run complete test suite after each fix

2. **Recommended Issues (Should Fix)**
   - Evaluate impact and priority
   - Address using TDD cycle if implementing
   - Document decision in `implementation.md` if deferring to future work

3. **Optional Issues (Nice to Have)**
   - Review for quick wins
   - Document decisions about implementation in `implementation.md`

#### QA Feedback Loop
- Update Current State in `technical-plan.md` with progress on QA action items
- Check off completed items directly in the `qa-report.md`
- When all Critical items resolved, update Current State to "Ready for Re-QA"
- Continue until QA report shows ✅ PASS status

#### QA Action Item Template
For each QA issue being addressed, add to `implementation.md`:

```markdown
## Addressing QA Issue: [Issue Description]

### TDD Approach
- [ ] RED: Write test for the fix
- [ ] GREEN: Implement minimal fix
- [ ] REFACTOR: Improve implementation
- [ ] Verify: Complete test suite passes

### Resolution Notes
- [Description of what was changed]
- [Impact on other components]
- [Updated item in qa-report.md: ✅]
```

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
2. **GitHub Issue Access:** When referencing GitHub issues, always use the GitHub CLI commands described in the Issue Protocol (File: `_ai.bws/protocols/issue.md`), section "Accessing GitHub Issues"
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

## Next Steps

After completing the execution phase, the next step is typically to move into quality assurance. This is done by:

1. Making sure all implementation is complete
2. Running the full test suite to ensure everything passes
3. Ensuring all items in the technical plan have been addressed
4. Activating the QA Workflow with: `workflow qa`

For details on quality verification, refer to QA Workflow (File: `_ai.bws/workflows/qa.md`).

## Related Protocols

- TDD Protocol (File: `_ai.bws/protocols/tdd.md`) - For test-driven development process
- Tasks Protocol (File: `_ai.bws/protocols/tasks.md`) - For work tracking
- Issue Protocol (File: `_ai.bws/protocols/issue.md`) - For GitHub issue structure
- Playwright Protocol (File: `_ai.bws/protocols/playwright.md`) - For browser automation
