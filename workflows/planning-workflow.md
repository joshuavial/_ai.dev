# Planning Workflow

This document outlines the recommended workflow for planning tickets, with a focus on thorough planning and structured preparation.

For the standardized GitHub issue structure, see [../shared/github-issue-structure.md](../shared/github-issue-structure.md).

## Planning Phase Process

### 1. Ticket Creation

- Ensure the ticket has a clear, unique ID (e.g., PROJ-123)
- Create the main issue description
- Add the Product/Problem Statement as the first comment
- Add the Technical Plan as the second comment
- Initialize the Current State as the third comment

### 2. Initial Technical Analysis

- Read and understand the associated PRD (if applicable)
- Identify which technical components will be affected
- Review related tickets or dependencies
- Estimate effort required

### 3. Code Review and Improvement Opportunities

During the planning phase, actively look for opportunities to improve the codebase:

- Review related code for code smells or technical debt
- Identify opportunities for refactoring
- Note potential architectural improvements
- Document any findings that could lead to future improvement tickets
- Create separate improvement tickets for issues that are out of scope for the current ticket

Document any findings in a section of the Technical Plan:

```
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

### 6. Final Technical Plan

Complete the second comment with a comprehensive technical plan, including:

- Detailed implementation approach
- Package/library decisions with justification
- Components to modify
- New components to create
- Testing strategy
- Detailed implementation checklist
- Potential risks and mitigation strategies

### 7. Initialize Current State Tracking

Create the third comment with an initial state tracking template:

```
## Current State

**Status:** Planning _(Planning/In Progress/Ready for Review/Blocked)_

**Progress:**
- [x] Initial planning complete
- [x] Technical approach defined
- [ ] Branch created
- [ ] Implementation started

**Blockers:**
- None at this time

**Next Steps:**
1. Create branch and begin implementation
2. Set up initial file structure

**Notes:**
- Implementation will begin on [DATE]
```

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

### Implementation Checklist
- [ ] Create branch with ticket ID
- [ ] Add jsPDF package with lazy loading
- [ ] Implement ExportService.js with PDF and CSV formatters
- [ ] Add export endpoint to UserProfileController.js
- [ ] Update UserService.js with export functions
- [ ] Add ExportButton.jsx component
- [ ] Update ProfilePage.jsx with export option
- [ ] Update AuthMiddleware.js with permission checks
- [ ] Write unit tests for export functions
- [ ] Write integration tests for API endpoint
- [ ] Test with large profiles
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
- [x] jsPDF package added with lazy loading
- [x] ExportService.js implemented with PDF formatter
- [x] CSV formatter implemented
- [ ] API endpoint added to UserProfileController.js (50% complete)
- [ ] UserService.js updated with export functions
- [ ] ExportButton.jsx component created
- [ ] ProfilePage.jsx updated with export option
- [ ] AuthMiddleware.js updated with permission checks
- [ ] Unit tests written for export functions
- [ ] Integration tests written for API endpoint
- [ ] Large profile testing completed

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

For information on executing tickets, see [execution-workflow.md](execution-workflow.md)