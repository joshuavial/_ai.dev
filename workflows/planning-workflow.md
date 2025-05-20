# Planning Workflow

This document outlines the process for planning tickets, with a focus on thorough analysis, technical approach definition, and implementation preparation.

## Planning Levels

The Planning workflow operates at multiple levels:

1. **PRD Level**: Breaking down a product area into issues
2. **Issue Level**: Creating technical plans for specific issues
3. **Task Level**: Breaking issues into discrete implementation tasks

## Planning Process

### 1. Ticket Creation

- Create issue with unique ID (e.g., PROJ-123)
- Add Product/Problem Statement as first comment
- Add Technical Plan as second comment
- Initialize Current State as third comment

### 2. Initial Technical Analysis

- Read and understand associated PRD (if applicable)
- Identify affected technical components
- Review related tickets or dependencies
- Estimate effort required

### 3. Code Review and Improvement Opportunities

- Review related code for technical debt
- Identify refactoring opportunities
- Document findings in the Technical Plan:

```
### Code Improvement Opportunities
- Identified technical debt in X component (potential future ticket)
- Authentication logic could be refactored to improve Y
- Consider standardizing Z pattern across the codebase
```

### 4. Package/Library Analysis

When new packages are needed:

- **Current Ecosystem Analysis:**
  - Review existing packages and patterns
  
- **Package Evaluation Criteria:**
  - Functionality, maintenance, community, bundle size
  - Dependencies, security, license, testing
  
- **Alternatives Comparison:**
  - List 2-3 alternatives with comparison table
  - Provide reasoned recommendation
  
Example package analysis:

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
```

### 5. Creating Sub-Tickets

For complex tickets:

- Break down into independent components
- Create sub-tickets with clear references to parent
- Ensure each sub-ticket has focused scope and criteria

Example:
- Parent: PROJ-123: Implement user profile export
- Sub-tickets:
  - PROJ-124: Implement PDF export service
  - PROJ-125: Create export UI components
  - PROJ-126: Add API endpoints for export

### 6. Testing Strategy Planning

Every technical plan must include a testing strategy:

- **Test Categories:** Unit, integration, UI, edge cases
- **Test-First Implementation Plan:** For each component:

```
#### Component: [ComponentName]

**Red Phase (Failing Tests):**
- Test 1: [Description of specific test case]
- Test 2: [Description of specific test case]

**Green Phase (Implementation):**
- Implement minimal solution to make tests pass

**Refactor Phase:**
- [Specific refactoring goals]
```

### 7. Final Technical Plan

Complete the technical plan with:

- Detailed implementation approach
- Package/library decisions with justification
- Components to modify or create
- Test-driven implementation strategy
- Implementation checklist with test steps
- Potential risks and mitigation strategies

### 8. User Confirmation

Before finalizing the plan:

1. **Present Complete Plan:** Summarize approach and key decisions
2. **Request Feedback:** Ask for approval to proceed
3. **Document Feedback:** Update plan based on user input
4. **Confirm Final Understanding:** Get final sign-off

### 9. Initialize Current State Tracking

Create the third comment with:

```
## Current State

**Status:** Planning

**Progress:**
- [x] Initial planning complete
- [x] Technical approach defined
- [x] Test strategy defined
- [ ] Branch created

**Test Status:**
- No tests implemented yet

**Blockers:**
- None at this time

**Next Steps:**
1. Create branch and begin implementation
2. Write failing tests for first component
```

## Next Steps

After planning is complete:

1. Confirm all planning deliverables are ready
2. Get stakeholder approval
3. Move to execution phase

See [execution-workflow.md](execution-workflow.md) for next steps.