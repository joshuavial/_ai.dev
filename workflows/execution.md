# Execution Workflow

# IMPORTANT: Read Boot Protocol First
> **CRITICAL**: When adopting this workflow, you MUST first read the core instructions and follow the boot protocol to ensure proper initialization. Do NOT skip this step as it's essential for correct workflow operation.
>
> 1. Read core instructions: `_ai.dev/core-instructions.md`
> 2. Follow boot protocol: `_ai.dev/protocols/boot.md`
> 3. Only then proceed with this workflow

**Primary Protocol**: File: `_ai.dev/workflows/execution.md`
**Required Protocols**: 
- Boot Protocol (File: `_ai.dev/protocols/boot.md`)
- Tasks Protocol (File: `_ai.dev/protocols/tasks.md`)
- TDD Protocol (File: `_ai.dev/protocols/tdd.md`)
**Activation**: `workflow execution`

This document outlines the recommended workflow for executing tickets, with a focus on structured implementation using Test-Driven Development (TDD) and continuous improvement. Execution works from task folders created by the planning workflow.

> Note: Always begin by activating the Boot Protocol to establish context before starting implementation work.

## Task Folder Structure

Execution operates from task folders with this structure:
```
_ai/tasks/[task-slug]/
├── technical-plan.md    # Input: planning objectives and approach
├── qa-report.md         # Output: QA feedback (created by QA workflow)
├── implementation.md    # Optional: detailed execution notes
├── tdd-evidence/        # Optional: test artifacts and screenshots
├── bug-investigation/   # Bug-specific: investigation evidence and analysis
└── artifacts/           # Optional: other task-related files
```

**Primary Documents**: `technical-plan.md` (objectives & approach) + `status.md` (live progress snapshot)

**Note**: Execution workflow can operate on multiple tasks simultaneously. The developer manages task priorities and context switching as needed.

## Implementation Process

### 1. Branch Creation

- Use the task slug in the branch name (e.g., `feat/onboarding-step-navigation`)
- For bug fixes, use format: `bugfix/[task-slug]` (e.g., `bugfix/login-timeout`) and note reproduction steps in the task
- Reference the task folder in commit messages; add GitHub issue IDs only when coordination with an external tracker is required

### 2. Bug Investigation Process (When Applicable)

For tickets involving bug fixes, follow this systematic investigation process before implementation:

#### 2.1 Bug Analysis Phase
```markdown
## Bug Investigation Checklist

### Initial Assessment
- [ ] Severity classification (Critical/High/Medium/Low)
- [ ] User impact analysis
- [ ] Environment identification (Prod/Staging/Dev)
- [ ] Reproduction reliability assessment

### Symptom Documentation
- [ ] Error messages captured
- [ ] Unexpected behaviors documented
- [ ] Performance issues identified
- [ ] User workflow disruptions noted
```

#### 2.2 Reproduction Strategy
```javascript
// Create failing test that captures the bug
describe('Bug Reproduction: [BUG-ID]', () => {
  it('should demonstrate the reported issue', async () => {
    // Setup: Recreate user's conditions
    const problematicState = setupBugConditions();
    
    // Action: Perform the problematic action
    const result = await performProblematicAction(problematicState);
    
    // Assert: Document the unexpected behavior
    expect(result).not.toEqual(expectedBehavior);
    // This test should FAIL to confirm bug exists
  });
});
```

#### 2.3 Root Cause Analysis
```markdown
### Investigation Evidence Structure
```
bug-investigation/
├── reproduction/
│   ├── automated-test.js    # Failing test that captures bug
│   ├── manual-steps.md      # Manual reproduction steps
│   ├── playwright-evidence/ # UI bug evidence
│   └── error-logs.txt      # System logs and errors
├── root-cause/
│   ├── code-analysis.md     # Code path analysis
│   ├── data-flow-trace.md   # Data transformation tracking
│   └── dependency-check.md  # External system interactions
└── resolution/
    ├── tdd-evidence/        # Fix implementation evidence
    └── regression-tests/    # Additional prevention tests
```
```

#### 2.4 Bug-Specific TDD Cycle

**Bug Fix TDD Process:**

1. **RED Phase - Bug Capture:**
   ```javascript
   // Write test that fails and captures the bug
   it('should handle edge case that causes bug', () => {
     const result = buggyFunction(edgeCaseInput);
     expect(result).toEqual(expectedCorrectBehavior);
     // This SHOULD fail initially
   });
   ```

2. **GREEN Phase - Minimal Fix:**
   ```javascript
   // Implement just enough to fix the bug
   function fixedFunction(input) {
     // Add specific handling for the bug condition
     if (isBugTriggeringCondition(input)) {
       return handleEdgeCase(input);
     }
     return originalLogic(input);
   }
   ```

3. **REFACTOR Phase - Robust Solution:**
   ```javascript
   // Improve the fix and add defensive programming
   function robustFunction(input) {
     validateInput(input);
     try {
       return processInput(input);
     } catch (error) {
       logger.error('Processing failed', { input, error });
       return createSafeDefault();
     }
   }
   ```

#### 2.5 Regression Prevention
```javascript
// Add comprehensive tests for related scenarios
describe('Bug [BUG-ID] Regression Prevention', () => {
  it('should handle boundary values correctly', () => {
    testBoundaryConditions();
  });
  
  it('should handle null and undefined inputs', () => {
    testNullHandling();
  });
  
  it('should maintain performance under load', () => {
    testPerformanceRequirements();
  });
  
  it('should not break existing functionality', () => {
    testExistingWorkflows();
  });
});
```

### 3. Test-Driven Development (TDD)

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
   - Document the failing test in `status.md`
   - Consider both the happy path and edge cases

2. **GREEN Phase (Implement Minimal Solution):**
   - Write just enough code to make the test pass - no more, no less
   - Focus on functionality, not optimization
   - Run the test to confirm it passes (green phase)
   - REQUIRED: Take a screenshot or save logs showing the passing test
   - Document the passing test in `status.md`
   - Do not proceed to refactoring until tests pass

3. **REFACTOR Phase:**
   - Improve the implementation without changing behavior
   - Remove duplication and improve code clarity
   - Run tests after each significant refactoring step
   - REQUIRED: Take a screenshot or save logs showing tests still pass after refactoring
   - Document the refactoring in `status.md`
   - Consider performance optimizations if needed
   - CRITICAL: You MUST run the complete test suite after completing all refactoring to ensure no regressions were introduced

4. **Verification Requirements:**
   - You MUST have evidence of all three phases for each component
   - Each phase must be documented in `status.md`
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

### 4. Implementation Flow

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
   - **IMPORTANT**: When using Playwright for browser automation, strictly follow the Playwright Protocol (File: `_ai.dev/protocols/playwright.md`)

4. **Cross-Cutting Concerns:**
   - Add error handling
   - Implement logging
   - Apply security measures
   - Address performance considerations

**CRITICAL REQUIREMENT**: When using Playwright, you MUST immediately stop execution if any Playwright operation fails. Never attempt to install browsers as they are already installed. See Playwright Protocol (File: `_ai.dev/protocols/playwright.md`) for complete details.

### 5. TDD Verification and State Tracking

#### Retrieving GitHub Issues (Optional)

When external coordination requires GitHub context, refer to the Issue Protocol (File: `_ai.dev/protocols/issue.md`), section "Accessing GitHub Issues".

**IMPORTANT:** If you access GitHub issues, always use GitHub CLI commands (like `gh issue view`). Never attempt to locate GitHub issues by searching local directories.

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

#### Updating Task Status

Throughout implementation, continuously refresh the task's `status.md`:

**Status.md Checklist**:
- Maintain RED/GREEN/REFACTOR progress for each component
- Document which tests were written and what they verify
- Record coverage percentages after refactoring
- Update the snapshot after every meaningful milestone (tests added, implementation complete, refactor done)
- Check off completed todos as they are finished (and ensure `todos.md` reflects the same)
- Add newly discovered todos if they emerge during implementation
- Document blockers and challenges as they appear
- Update next steps with the top pending actions
- Note discoveries or changes to the approach succinctly

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
   - Document refactoring in `status.md`, not in separate files

4. **Required File Management Checks**
   - After completing each TDD cycle, verify no duplicate files exist
   - Before creating PRs, run a check for any files with suffixes indicating they are alternate versions
   - If duplicate files are discovered, consolidate changes into the original file and remove the duplicates

Example `status.md` snapshot with explicit TDD documentation:

```
## Status: User Profile Export

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

### 6. Bug Investigation with Debug Agent

For systematic bug investigation and resolution, use the specialized bug-investigator agent:

#### 6.1 Debug Agent Activation
```bash
# Activate the bug investigator for systematic diagnosis
/debug
```

The bug-investigator agent provides:
- **Systematic diagnostic investigation** with log analysis and state reconstruction
- **Automated bug reproduction** using failing tests that capture the issue
- **Root cause analysis** through code path tracing and dependency analysis
- **TDD-driven bug resolution** with comprehensive regression prevention
- **Integration with manual-tester** for UI bug reproduction via Playwright
- **Quality gates** to ensure thorough resolution before completion

#### 6.2 Bug Investigation Process
```markdown
### Bug Investigation Workflow

#### Phase 1: Analysis & Reproduction
- [ ] Bug report analysis and severity assessment
- [ ] Systematic diagnostic investigation
- [ ] Create failing test that captures bug behavior
- [ ] Manual reproduction via Playwright (if UI bug)
- [ ] Document investigation evidence

#### Phase 2: Root Cause Analysis
- [ ] Code path tracing and data flow analysis
- [ ] Dependency and integration point analysis
- [ ] Timing and race condition investigation
- [ ] Document root cause findings

#### Phase 3: TDD Resolution
- [ ] RED: Failing test that captures bug
- [ ] GREEN: Minimal fix implementation
- [ ] REFACTOR: Robust solution with error handling
- [ ] Regression prevention tests

#### Phase 4: Validation
- [ ] Complete test suite passes
- [ ] No side effects introduced
- [ ] Performance impact acceptable
- [ ] Documentation and pattern capture complete
```

### 7. Continuous Improvement

During implementation:

- Note any discoveries that differ from the technical plan
- Update the technical plan comment if implementation details change
- Document any new improvement opportunities discovered during implementation
- Create separate tickets for improvements beyond the scope of the current work

### 8. PR Creation and Review

**ZERO TOLERANCE FOR FAILING TESTS - MANDATORY VERIFICATION**

Before creating any PR, you MUST complete the following verification steps:

#### 8.1 Complete Test Suite Verification
- **MANDATORY**: Run the complete test suite: `poetry pytest tests/ --verbose`
- **VERIFY**: ALL tests show PASSED status - NO FAILED tests allowed
- **DOCUMENT**: Test execution results with timestamp
- **STOP IMMEDIATELY**: If ANY test fails, do not proceed with PR creation
- **HUMAN SIGN-OFF REQUIRED**: Cannot skip any failing test without explicit human approval

#### 8.2 File Management Verification
- **MANDATORY**: Verify all code modifications made directly to original files
- **CHECK**: No alternate versions created with suffixes (.updated, .new, .enhanced, .refactored, .copy, .backup, .old, etc.)
- **VERIFY**: No redundant or duplicate files exist
- **CLEAN**: Remove any temporary files created during development

#### 8.3 PR Creation Requirements
- Create PR with reference to ticket ID
- Ensure PR description references the Technical Plan
- Include evidence of TDD compliance in the PR description
- Include test coverage reports in the PR
- Document that all tests are passing with screenshots/logs

#### 8.4 Review Process
- Address review comments promptly
- **MANDATORY**: Run complete test suite after addressing review comments
- Update `status.md` to reflect PR review status
- **ZERO TOLERANCE**: If any tests fail during review, follow the TDD cycle to fix them - NO EXCEPTIONS

### 9. Completion

**ZERO TOLERANCE FINAL VERIFICATION**

Before marking any task complete, you MUST verify:

#### 9.1 Implementation Verification
- Verify all checklist items in `technical-plan.md` are complete
- Update documentation if needed

#### 9.2 Test Verification (MANDATORY)
- **MANDATORY**: Run the complete test suite: `poetry pytest tests/ --verbose`
- **VERIFY**: ALL tests pass - NO FAILED tests allowed
- **DOCUMENT**: Evidence of passing tests in `tdd-evidence/` folder
- **HUMAN SIGN-OFF REQUIRED**: Cannot skip any failing test without explicit human approval

#### 9.3 File Management Verification (MANDATORY)
- **VERIFY**: All changes made to original files directly
- **CHECK**: No alternate versions with suffixes (.updated, .new, .enhanced, .refactored, .copy, .backup, .old, etc.)
- **CLEAN**: Remove any redundant or duplicate files
- **CONFIRM**: Repository contains only necessary files

#### 9.4 Task Completion
- Update `status.md` to "Ready for QA"
- Close ticket with reference to the PR
- Proceed to the QA workflow for final quality verification

**CRITICAL**: Task cannot be marked complete with ANY failing tests or redundant files

### 10. QA Response Process

When QA returns work with issues (creates `qa-report.md` in task folder):

#### QA Document Review
- Read the `qa-report.md` in the task folder
- Review all Critical and Recommended action items
- Update `status.md` to "Addressing QA Feedback"

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
- Update `status.md` with progress on QA action items
- Check off completed items directly in the `qa-report.md`
- When all Critical items resolved, update `status.md` to "Ready for Re-QA"
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

### Example 3: Bug Fix TDD Approach

```javascript
// Step 1: Write failing test that captures the bug
test('should handle user input with special characters correctly', () => {
  // Arrange
  const inputWithSpecialChars = 'user@domain.com & <script>alert("xss")</script>';
  
  // Act
  const result = sanitizeUserInput(inputWithSpecialChars);
  
  // Assert - This should initially fail, capturing the bug
  expect(result).not.toContain('<script>');
  expect(result).toContain('user@domain.com');
  expect(result).not.toContain('&lt;script&gt;'); // Should be properly escaped
});

// Step 2: Implement minimal fix
function sanitizeUserInput(input) {
  // Add basic sanitization to fix the security bug
  return input
    .replace(/</g, '&lt;')
    .replace(/>/g, '&gt;')
    .replace(/"/g, '&quot;')
    .replace(/'/g, '&#x27;');
}

// Step 3: Refactor for comprehensive solution
function sanitizeUserInput(input) {
  if (!input || typeof input !== 'string') {
    return '';
  }
  
  // Use a robust sanitization library
  return DOMPurify.sanitize(input, {
    ALLOWED_TAGS: [],
    ALLOWED_ATTR: []
  });
}
```

## Working with Sub-Tickets

When implementing a feature that has been broken down into sub-tickets:

1. **Coordinated Implementation:**
   - Start with the parent ticket's technical plan as your overall guide
   - Focus implementation on the specific scope defined in the sub-ticket
   - Update progress in both the sub-task and parent task `status.md`

2. **Integration Points:**
   - Pay special attention to integration points between sub-ticket implementations
   - Document any interface contracts or shared dependencies clearly
   - Use consistent naming and patterns across all sub-ticket implementations

3. **Completion Process:**
   - Mark sub-tasks as complete individually
   - Update the parent task's `status.md` (and `handoff.md`, if needed) to reflect progress
   - Only close parent work items when all sub-tasks are complete and integrated

## AI Implementation Guidelines

When working with AI tools to implement features:

1. **Clear Context:** Provide task folder path, PRD links, and any external references
2. **GitHub Issue Access (Optional):** When referencing GitHub issues, use the GitHub CLI commands described in the Issue Protocol (File: `_ai.dev/protocols/issue.md`), section "Accessing GitHub Issues"
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

For details on quality verification, refer to QA Workflow (File: `_ai.dev/workflows/qa.md`).

## Related Protocols

- TDD Protocol (File: `_ai.dev/protocols/tdd.md`) - For test-driven development process
- Tasks Protocol (File: `_ai.dev/protocols/tasks.md`) - For work tracking
- Issue Protocol (File: `_ai.dev/protocols/issue.md`, optional) - For GitHub issue structure when coordination is needed
- Playwright Protocol (File: `_ai.dev/protocols/playwright.md`) - For browser automation
