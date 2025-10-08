# Planning Workflow

# IMPORTANT: Read Boot Protocol First
> **CRITICAL**: When adopting this workflow, you MUST first read the core instructions and follow the boot protocol to ensure proper initialization. Do NOT skip this step as it's essential for correct workflow operation.
>
> 1. Read core instructions: `_ai.dev/core-instructions.md`
> 2. Follow boot protocol: `_ai.dev/protocols/boot.md`
> 3. Only then proceed with this workflow

**Primary Protocol**: File: `_ai.dev/workflows/planning.md`
**Required Protocols**:
- Boot Protocol (File: `_ai.dev/protocols/boot.md`)
- Tasks Protocol (File: `_ai.dev/protocols/tasks.md`)
**How to Use**: Read this file directly to follow the planning workflow

This document outlines the recommended workflow for planning tickets, with a focus on thorough planning and structured preparation. The planning phase is where requirements are analyzed, technical approaches are defined, and implementation is prepared.

> Note: Always begin by activating the Boot Protocol to establish context before starting planning work.

## Multi-Level Planning Capabilities

The Planning workflow is designed to operate at multiple levels of the work hierarchy:

1. **PRD Level Planning**: Breaking down a product area into discrete tasks
2. **Task Level Planning**: Creating technical plans for specific tasks

> External trackers (e.g., GitHub issues) can be referenced within task plans when needed, but they are not required for this workflow.

When operating at a specific level, explicitly state which level you are planning for and follow the appropriate sections of this workflow.

## ⛔ CRITICAL: ABSOLUTE PROHIBITION OF TIME AND EFFORT ESTIMATES ⛔

**MANDATORY REQUIREMENT - ZERO TOLERANCE**: You MUST NEVER include ANY form of time, effort, schedule, or duration estimates in planning documents.

### PROHIBITED - DO NOT INCLUDE:

**Time-Based Estimates (NEVER ALLOWED):**
- ❌ Hours, days, weeks, months estimates
- ❌ Sprint estimates or story points
- ❌ Calendar dates or deadlines
- ❌ Timeline predictions or forecasts
- ❌ Duration estimates of any kind
- ❌ Schedule commitments or projections
- ❌ Time-to-completion predictions
- ❌ Velocity-based estimates

**Effort-Based Estimates (NEVER ALLOWED):**
- ❌ Effort estimates (low/medium/high effort)
- ❌ Complexity ratings that imply duration
- ❌ Story points or t-shirt sizing
- ❌ Resource allocation estimates
- ❌ Developer-hours or person-days
- ❌ Workload assessments with temporal implications

**Schedule-Related Items (NEVER ALLOWED):**
- ❌ Specific delivery dates
- ❌ Milestone timelines
- ❌ Phase duration estimates
- ❌ Implementation schedules
- ❌ Rollout timelines
- ❌ "This will take X time" statements

### WHY THIS PROHIBITION EXISTS:

1. **Estimates Create False Expectations**: Time predictions during planning are inherently uncertain and create unrealistic expectations
2. **Pressure and Quality Trade-offs**: Estimates create artificial pressure that can compromise code quality
3. **High Variability**: Actual implementation time varies dramatically based on countless factors discovered during execution
4. **Planning vs Execution**: Planning phase should focus on WHAT and HOW, not WHEN
5. **User Directive**: The user has explicitly prohibited all time and effort estimations

### WHAT TO USE INSTEAD:

**Allowed Complexity Indicators (Descriptive Only):**
- ✅ Relative complexity descriptors: "simple", "moderate", "complex"
- ✅ Technical difficulty assessment (without time implications)
- ✅ Dependency identification (without schedule implications)
- ✅ Risk assessment (without duration estimates)
- ✅ Scope clarity indicators

**Allowed Prioritization (Without Time):**
- ✅ Critical, High, Medium, Low priority
- ✅ Dependency ordering (what must come before what)
- ✅ Business value assessment
- ✅ Risk-based prioritization
- ✅ Impact analysis (without schedule implications)

**Focus Areas:**
- ✅ Technical approach and architecture
- ✅ Component design and interfaces
- ✅ Test strategy and coverage
- ✅ Quality requirements
- ✅ Technical thoroughness and completeness

### ENFORCEMENT:

**If you find yourself:**
- Thinking "this will take X hours/days"
- Wanting to estimate effort or duration
- Considering schedule predictions
- Planning timelines or milestones

**STOP IMMEDIATELY** and refocus on:
- Technical approach and design
- Quality and testing strategy
- Dependency identification
- Risk assessment (without time estimates)

## Planning Phase Process

### 1. Task Folder and Technical Plan Creation

**Create Task Folder Structure**:
- Create folder: `_ai/tasks/[task-slug]/`
- Task naming pattern: `[context]-[descriptive-task-name]` (e.g., `onboarding-step-navigation`)
- Keep slugs concise, lowercase, and hyphenated

**Create Technical Plan**:
- Create file: `technical-plan.md` in the task folder
- Use the technical plan template (see section 7)
- Reference parent PRD from `_ai/prds/` folder
- Include task-specific scope and objectives

### 2. Initial Technical Analysis

- Read and understand the associated PRD (if applicable)
- Identify which technical components will be affected
- Review related tasks, components, or external tracker entries
- Assess complexity (simple/moderate/complex) - DESCRIPTIVE ONLY, NO TIME ESTIMATES

### 3. Code Review and Improvement Opportunities

During the planning phase, actively look for opportunities to improve the codebase:

- Review related code for code smells or technical debt
- Identify opportunities for refactoring
- Note potential architectural improvements
- Document any findings that could lead to future improvement tickets
- Create separate improvement tasks or external tickets for findings that are out of scope for the current work

Document any findings in a section of the Technical Plan:

```

> Maintain live execution updates in the task's `status.md`. The technical plan should remain focused on objectives, architectural decisions, and testing strategy.
### Code Improvement Opportunities
- Identified technical debt in X component (potential future ticket)
- Authentication logic could be refactored to improve Y
- Consider standardizing Z pattern across the codebase
```

### 4. Package/Library Analysis

A critical component of technical planning is carefully analyzing any new package dependencies:

- **Current Ecosystem Analysis:**
  - Review existing packages in the project
  - Identify if any existing packages can fulfill the requirements
  - Document patterns for how similar functionality is implemented

- **Package Evaluation Criteria:**
  - Functionality: Does it fully meet the requirements?
  - Maintenance: Is it actively maintained? When was the last release?
  - Community: Size of community, GitHub stars, number of contributors
  - Bundle Size: Impact on application size and load time
  - Dependencies: How many additional dependencies will it bring in?
  - Security: Any known vulnerabilities or security concerns?
  - License: Is the license compatible with the project?
  - Testing: Does the package have good test coverage?

- **Alternatives Comparison:**
  - List at least 2-3 alternatives for any proposed package
  - Create a comparison table with the evaluation criteria
  - Provide a reasoned recommendation with clear justification

- **Final Recommendation:**
  - Clear recommendation with reasoning
  - Implementation considerations or caveats
  - Fallback plan if the package doesn't meet expectations during implementation

Example package analysis section:

```
### Package Analysis: PDF Generation

**Requirement:** Generate PDF exports of user profiles

**Current Ecosystem:**
- No existing PDF generation in the codebase
- We use React for rendering UI components

**Options Considered:**

| Package    | Size  | Maintenance | Community | Dependencies | License | Security  |
|------------|-------|-------------|-----------|--------------|---------|-----------|
| jsPDF      | 2.5MB | Active      | 27.4k ⭐  | 0            | MIT     | No issues |
| pdfmake    | 2.2MB | Active      | 10.3k ⭐  | 2            | MIT     | No issues |
| react-pdf  | 4.8MB | Less active | 13.2k ⭐  | 5            | MIT     | No issues |

**Recommendation:**
Use jsPDF because:
1. Zero dependencies minimizes attack surface
2. Smaller bundle size than alternatives
3. Most active community and maintenance
4. Direct canvas rendering approach fits our needs

**Implementation Considerations:**
- Will need custom CSS-to-PDF styling solution
- Consider lazy-loading to reduce initial bundle size
```

### 5. Creating Sub-Tickets

For complex tickets that can benefit from parallel work, consider creating sub-tickets:

- Assess if the ticket can be broken down into independent components
- Create sub-tickets that focus on specific parts of the implementation
- Each sub-ticket should:
  - Have a clear reference to the parent ticket
  - Contain a focused problem statement
  - Include specific acceptance criteria for just that component
  - Have minimal technical details, focused only on the specific sub-task

Example of a parent-child ticket relationship:

**Parent Ticket:** PROJ-123: Implement user profile export functionality
**Sub-Tickets:**
- PROJ-124: Implement PDF export service
- PROJ-125: Create export UI components
- PROJ-126: Add API endpoints for export functionality

The parent ticket maintains the comprehensive technical plan, while sub-tickets contain only the details relevant to their specific scope.

### 6. Testing Strategy Planning

Every technical plan must include a comprehensive testing strategy that explicitly prepares for Test-Driven Development and manual functional testing:

- **Test Categories & Coverage Goals:**
  - Unit Tests: Identify specific units requiring testing and coverage targets
  - Integration Tests: Define integration points requiring testing
  - UI Tests: Identify UI components requiring test coverage
  - Edge Cases: List specific edge cases to be tested
  - Regression Tests: Identify related functionality that needs regression testing
  - Manual Functional Tests: Reference sections of `_ai/manual-test-plan.md` that need verification

- **Test-First Implementation Plan:**
  For each component to be modified or created, define the following test-first approach:

  ```
  #### Component: [ComponentName]

  **Red Phase (Failing Tests):**
  - Test 1: [Description of specific test case]
  - Test 2: [Description of specific test case]

  **Green Phase (Implementation):**
  - Implement minimal solution to make tests pass
  - Run and document test results

  **Refactor Phase:**
  - [Specific refactoring goals]
  - Run tests after refactoring to ensure they pass
  ```

- **Test Verification Requirements:**
  - Required evidence of test execution at each phase
  - Expected test coverage percentages
  - Mock requirements for external dependencies
  - Performance test criteria (if applicable)

Example testing strategy section:

```
### Test-Driven Implementation Strategy

#### Test Categories
- Unit Tests: Export service formatting functions (target: 90% coverage)
- Integration Tests: API endpoint functionality with mock data
- UI Tests: Export button behavior and loading states
- Edge Cases: Empty data, large files, various export formats

#### Component: ExportService.js

**Red Phase (Failing Tests):**
- Test PDF data formatting with mock user data
- Test CSV data formatting with mock user data
- Test error handling for invalid data formats

**Green Phase (Implementation):**
- Implement formatUserData function with PDF/CSV formatters
- Verify tests pass and document results

**Refactor Phase:**
- Extract format-specific logic to separate modules
- Optimize large data handling
- Verify refactored code passes all tests

#### Component: UserProfileController.js

**Red Phase (Failing Tests):**
- Test API endpoint returns correctly formatted data
- Test authorization requirements
- Test error handling for various cases

**Green Phase (Implementation):**
- Implement endpoint with basic authorization
- Connect to export service
- Verify tests pass

**Refactor Phase:**
- Improve error responses
- Add caching for repeated requests
- Verify refactored code passes all tests

#### Test Verification Requirements
- Screenshot or log of RED phase (failing tests) before implementation
- Screenshot or log of GREEN phase (passing tests) after implementation
- Screenshot or log of tests passing after REFACTOR phase
- Test coverage report showing minimum 85% coverage
- Manual test execution results from Playwright testing

#### Manual Functional Testing Plan
- Sections from `_ai/manual-test-plan.md` affected:
  - Section 2.3: User Profile Export (all test cases)
  - Section 4.1: Authorization (export permissions)
  - Section 5.2: File Downloads (PDF/CSV generation)
- New test cases to add to manual test plan:
  - Export with special characters in user data
  - Export with maximum file size limits
  - Concurrent export requests handling
```

### 7. Technical Plan Template

The `technical-plan.md` file should follow this structure:

```markdown
# Technical Plan: [Task Name]

**Task**: {task-name}
**References (Optional)**:
- PRD: {prd-name}.md
- GitHub Issue: #{issue-number}
**Status**: Planning

## Objectives
[What this task aims to achieve - specific goals and outcomes]

## Technical Approach
[Implementation strategy and high-level approach]

## Package/Library Analysis
[If new packages needed - include analysis from section 4]

## Components to Modify
[List existing components that will be changed]

## New Components to Create
[List new components to be built]

## Test-Driven Implementation Strategy
[Detailed TDD approach - see section 6 for requirements]

## Manual Functional Testing Requirements
[List specific sections from _ai/manual-test-plan.md that need verification]
[Any new test cases to be added to the manual test plan]

## Implementation Checklist
[Detailed checklist with TDD steps - see examples in section 8]

## Risks and Mitigation
[Potential risks and how to address them]

```

### 8. User Confirmation and Sign-Off

**CRITICAL REQUIREMENT**: Before saving or updating any planning documents:

1. **Present the Complete Plan to the User**:
   - Summarize key aspects of the plan
   - Highlight important decisions or approaches
   - Present any alternatives that were considered
   - Explain any risks or areas of uncertainty

2. **Request Explicit Feedback and Approval**:
   - Ask specific questions about areas that might need adjustment
   - Solicit feedback on the implementation approach
   - Request explicit approval to proceed with the plan

3. **Document User Feedback**:
   - Record any changes requested by the user
   - Update the plan based on user feedback
   - Note specific approvals in the plan

4. **Confirm Final Understanding**:
   - Present the updated plan if changes were made
   - Get final confirmation before saving

**Example user confirmation request**:
```
Based on my analysis, here's the technical plan I've developed:

[Summary of key technical approach points]

Key decisions:
1. Using jsPDF for PDF generation because of [reasons]
2. Implementing feature in components X, Y, and Z
3. Breaking down implementation into these phases: [phases]

Are you comfortable with this approach? Would you like me to make any adjustments before finalizing the plan?
```

Only proceed with saving/updating the plan after receiving explicit user confirmation.

### 9. Task Folder Completion

After user confirmation:

**Finalize Task Folder**:
- Save `technical-plan.md` in the task folder
- Create placeholder `qa-report.md` (empty file for later use)
- Create `tdd-evidence/` subfolder if TDD artifacts will be needed
- Create `artifacts/` subfolder if other task files will be needed

**Update Project State**:
- Create or refresh the task's `status.md` with key context and open questions
- Add links to relevant GitHub issues or external trackers (optional)

## Example Technical Plans

### Example 1: Feature Addition with Package Analysis

```
## Technical Plan for PROJ-123: Add User Profile Export Feature

Related PRD: user-profile-management-prd.md

### Approach
- Add new API endpoint at `/api/users/:id/export` with PDF and CSV format options
- Create export service to fetch and format user data
- Add UI component in profile page for export options
- Implement access controls to validate user permissions

### Package Analysis: PDF Generation

**Requirement:** Generate PDF exports of user profiles

**Current Ecosystem:**
- No existing PDF generation in the codebase
- We use React for rendering UI components

**Options Considered:**

| Package    | Size  | Maintenance | Community | Dependencies | License | Security  |
|------------|-------|-------------|-----------|--------------|---------|-----------|
| jsPDF      | 2.5MB | Active      | 27.4k ⭐  | 0            | MIT     | No issues |
| pdfmake    | 2.2MB | Active      | 10.3k ⭐  | 2            | MIT     | No issues |
| react-pdf  | 4.8MB | Less active | 13.2k ⭐  | 5            | MIT     | No issues |

**Recommendation:**
Use jsPDF because:
1. Zero dependencies minimizes attack surface
2. Smaller bundle size than alternatives
3. Most active community and maintenance
4. Direct canvas rendering approach fits our needs

**Implementation Considerations:**
- Will need custom CSS-to-PDF styling solution
- Consider lazy-loading to reduce initial bundle size

### Components to Modify
1. UserProfileController.js - Add export endpoint
2. UserService.js - Add export formatting functions
3. ProfilePage.jsx - Add export UI component
4. AuthMiddleware.js - Add permission check for exports

### New Components
1. ExportService.js - Handle different export formats
2. ExportButton.jsx - Reusable export button component

### Testing Strategy
- Unit tests for export formatting functions
- Integration tests for API endpoint
- UI tests for export flow

### Code Improvement Opportunities
- UserService.js has inconsistent error handling patterns - potential refactoring opportunity
- Profile data is fetched in multiple places - could be centralized
- Consider adding a general-purpose file download utility that could be reused elsewhere

### Risks
- Large profiles may timeout during export - implement chunking
- PDF generation may have rendering issues - need thorough testing

### TDD Implementation Checklist
- [ ] Create branch with ticket ID
- [ ] **ExportService PDF formatter:**
  - [ ] Write failing tests (RED)
  - [ ] Document test failure with logs/screenshots
  - [ ] Implement minimal solution to pass tests (GREEN)
  - [ ] Document passing tests with logs/screenshots
  - [ ] Refactor code (REFACTOR)
  - [ ] Verify tests still pass after refactoring
  - [ ] Generate test coverage report

- [ ] **ExportService CSV formatter:**
  - [ ] Write failing tests (RED)
  - [ ] Document test failure with logs/screenshots
  - [ ] Implement minimal solution to pass tests (GREEN)
  - [ ] Document passing tests with logs/screenshots
  - [ ] Refactor code (REFACTOR)
  - [ ] Verify tests still pass after refactoring
  - [ ] Generate test coverage report

- [ ] **UserProfileController - export endpoint:**
  - [ ] Write failing tests (RED)
  - [ ] Document test failure with logs/screenshots
  - [ ] Implement minimal solution to pass tests (GREEN)
  - [ ] Document passing tests with logs/screenshots
  - [ ] Refactor code (REFACTOR)
  - [ ] Verify tests still pass after refactoring
  - [ ] Generate test coverage report

- [ ] **ExportButton.jsx component:**
  - [ ] Write failing tests (RED)
  - [ ] Document test failure with logs/screenshots
  - [ ] Implement minimal solution to pass tests (GREEN)
  - [ ] Document passing tests with logs/screenshots
  - [ ] Refactor code (REFACTOR)
  - [ ] Verify tests still pass after refactoring
  - [ ] Generate test coverage report

- [ ] Add jsPDF package with lazy loading
- [ ] Update remaining components as needed
- [ ] Perform final integration testing
```

### Example 2: Integration Implementation

```
## Technical Plan for PROJ-456: Stripe-Xero Integration

Related PRD: stripe-xero-integration-prd.md

### Approach
- Implement webhook handler for Stripe events
- Create mapping service to transform Stripe data to Xero format
- Add scheduled reconciliation process
- Create error handling and retry mechanism
- Add admin interface for manual reconciliation

### Package Analysis: API Clients

**Requirement:** Integrate with Stripe and Xero APIs

**Current Ecosystem:**
- We already use stripe-node for payment processing
- No existing Xero integration

**Options for Xero Integration:**

| Package         | Size   | Maintenance  | Community | Dependencies | License | Security  |
|-----------------|--------|--------------|-----------|--------------|---------|-----------|
| xero-node       | 1.2MB  | Very active  | 180 ⭐    | 8            | MIT     | No issues |
| node-xero       | 680KB  | Not active   | 78 ⭐     | 3            | MIT     | 2 warnings|
| Custom SDK      | ~500KB | N/A          | N/A       | 2 (axios)    | N/A     | N/A       |

**Recommendation:**
Use xero-node because:
1. Official library with full API support
2. Active maintenance and community support
3. Comprehensive documentation and examples
4. Pre-built OAuth2 handling

**Implementation Considerations:**
- Library is somewhat large, but justified for the comprehensive API coverage
- Will need to implement proper credential management and refresh token handling

### Components to Modify
1. WebhookController.js - Add Stripe webhook endpoint
2. PaymentService.js - Update to trigger Xero sync
3. AdminDashboard.jsx - Add reconciliation interface

### New Components
1. StripeXeroMappingService.js - Transform data between systems
2. XeroApiClient.js - Handle Xero API communication
3. ReconciliationService.js - Handle scheduled and manual reconciliation
4. ReconciliationDashboard.jsx - UI for monitoring and manual actions

### Data Models
1. New table: stripe_xero_transactions
   - stripe_payment_id (PK)
   - xero_invoice_id
   - status (pending, synced, error)
   - error_message
   - retry_count
   - last_synced_at

### Testing Strategy
- Mock Stripe webhook events for testing
- Unit tests for mapping logic
- Integration tests with Stripe/Xero sandboxes
- End-to-end flow tests

### Code Improvement Opportunities
- Current webhook handling has no standardized validation - opportunity to implement shared webhook verification
- Error logging is inconsistent across payment services - could standardize
- Consider a more generic integration framework that could be reused for future third-party integrations

### Risks
- API rate limits for Xero - implement backoff strategy
- Transaction matching edge cases - need robust reconciliation logic

### Implementation Checklist
- [ ] Create branch with ticket ID
- [ ] Add xero-node package
- [ ] Create stripe_xero_transactions table migration
- [ ] Implement XeroApiClient.js with authentication
- [ ] Implement StripeXeroMappingService.js
- [ ] Implement ReconciliationService.js
- [ ] Update WebhookController.js with Stripe endpoint
- [ ] Update PaymentService.js with Xero sync
- [ ] Implement ReconciliationDashboard.jsx
- [ ] Update AdminDashboard.jsx
- [ ] Write unit tests for mapping logic
- [ ] Write integration tests with sandboxes
- [ ] Configure error handling and retries
```

### Example of Current State Comment

```
## Current State

**Status:** In Progress

**Progress:**
- [x] Initial planning complete
- [x] Technical approach defined
- [x] Branch created: feature/PROJ-123-user-profile-export

**Test Status:**
- **ExportService PDF formatter:**
  - [x] Tests written (RED ✓) - [Link to screenshot/log]
  - [x] Implementation complete (GREEN ✓) - [Link to screenshot/log]
  - [x] Code refactored (REFACTOR ✓) - [Link to screenshot/log]
  - Current coverage: 92%

- **ExportService CSV formatter:**
  - [x] Tests written (RED ✓) - [Link to screenshot/log]
  - [ ] Implementation complete (GREEN ⬤)
  - [ ] Code refactored (REFACTOR ⬤)
  - Current coverage: N/A

- **UserProfileController export endpoint:**
  - [ ] Tests written (RED ⬤)
  - [ ] Implementation complete (GREEN ⬤)
  - [ ] Code refactored (REFACTOR ⬤)
  - Current coverage: N/A

**Blockers:**
- Need design review for ExportButton.jsx component
- Waiting on API token permissions from DevOps team

**Next Steps:**
1. Complete API endpoint implementation
2. Update UserService.js with export functions
3. Schedule design review for UI components

**Notes:**
- Found potential performance issue with large profiles - will need to implement chunking
- May need to adjust API rate limits for production deployment
```

## Manual Test Plan Maintenance

The project should maintain a comprehensive manual test plan at `_ai/manual-test-plan.md` that documents all functional test scenarios. During planning:

1. **Review Existing Test Plan**: Check which sections of the manual test plan are affected by the new feature
2. **Identify Test Gaps**: Determine what new test cases need to be added
3. **Reference in Technical Plan**: List specific test plan sections that need verification
4. **Update Test Plan**: Add new test cases for the feature being planned

The manual test plan should be organized by functional areas and include:
- Test case ID and description
- Prerequisites and test data
- Step-by-step test procedures
- Expected results
- Links to relevant Playwright scripts for automation

## Next Steps

After completing the planning phase, the next step is typically to move into execution. This is done by:

1. Ensuring task folder is created with `technical-plan.md`
2. Confirming the technical plan with stakeholders
3. Updating the task's `status.md` with planning outcomes
4. Updating `_ai/manual-test-plan.md` with any new test cases identified
5. Reading and following the Execution Workflow (File: `_ai.dev/workflows/execution.md`)

The execution workflow will work from the `technical-plan.md` file in the task folder and keep `status.md` current as implementation progresses.

For details on implementation, refer to Execution Workflow (File: `_ai.dev/workflows/execution.md`).

## Related Protocols

- Tasks Protocol (File: `_ai.dev/protocols/tasks.md`) - For work breakdown
- TDD Protocol (File: `_ai.dev/protocols/tdd.md`) - For test planning
- Issue Protocol (File: `_ai.dev/protocols/issue.md`, optional) - For GitHub issue structure when external coordination is needed
