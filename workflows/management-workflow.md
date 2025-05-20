# Management Workflow

This document outlines the structured approach for managing, improving, and evolving the development process itself.

> **IMPORTANT**: When using the Management workflow, ONLY modify workflow and process documentation in `_ai.bws` directories. Never modify project-specific files in the `_ai` directory during management workflow activities. Place all improvement planning and documentation in the `_ai.bws/_improvements` directory.

## Purpose and Scope

### Purpose of the Management Workflow

1. **Process Evolution**: Systematically improve existing workflows
2. **Consistency Management**: Maintain consistency across the development lifecycle
3. **Knowledge Integration**: Incorporate insights from completed projects
4. **Standard Enhancement**: Update workflow documents, templates, and guidelines
5. **Organizational Learning**: Create a feedback loop for continuous improvement

### Scope of the Management Workflow

**Includes**:
- Process changes to existing workflows
- Template evolution and refinement
- Tool integration guidelines
- Role responsibilities clarification
- Documentation standards improvement

**Excludes**:
- Project-specific implementations
- Individual code or feature decisions
- Organization structure changes
- Tool selection for implementation tasks
- Product or feature requirements definition

## Management Workflow Process

### 1. Identification and Capture

- Review reflection documents for improvement suggestions
- Gather team feedback through review sessions
- Analyze process metrics (cycle time, defect rates, etc.)
- Create process improvement tickets with clear problem statements
- Prioritize based on impact and implementation effort

### 2. Analysis and Design

- Perform root cause analysis of process issues
- Research alternative approaches and best practices
- Analyze impact on workflows, team members, and tools
- Design specific process changes with examples
- Define clear success metrics for evaluation

### 3. Review and Approval

- Identify stakeholders affected by the change
- Share the Process Change Proposal with stakeholders
- Collect and document feedback
- Refine the proposal based on feedback
- Assess implementation risks and develop mitigation plans
- Obtain formal approval from decision makers

### 4. Implementation and Documentation

- Determine implementation approach (big bang, phased, parallel)
- Update affected workflow documents directly (never create separate versions)
- Communicate changes to the team with rationale and benefits
- Develop training materials if needed
- Support the team during rollout

### 5. Validation and Iteration

- Gather data on defined success metrics
- Collect team feedback on the change
- Evaluate against success criteria
- Identify and implement necessary adjustments
- Document lessons learned
- Close the process improvement ticket with results

## Process Improvement Proposal Template

```markdown
# Process Improvement Proposal: [Title]

## 1. Overview

### 1.1 Problem Statement
[Describe the process issue or improvement opportunity]

### 1.2 Current Process
[Describe the current process with references to workflow documents]

### 1.3 Impact of Current Process
[Describe negative impacts with specific examples]

### 1.4 Affected Workflows
[List all affected workflows]

## 2. Proposed Change

### 2.1 Change Description
[Detailed description of the proposed process change]

### 2.2 Expected Benefits
[List anticipated benefits and improvements]

### 2.3 Examples
[Provide concrete before/after examples]

### 2.4 Alternatives Considered
[Describe other approaches that were considered]

## 3. Impact Analysis

### 3.1 Workflow Document Changes
[List specific changes needed to workflow documents]

### 3.2 Team Impact
[Describe how the change will affect team members]

### 3.3 Implementation Plan
[Describe rollout strategy, communication plan, and timeline]

## 4. Success Criteria and Validation

### 4.1 Metrics for Success
[Define specific, measurable criteria to evaluate success]

### 4.2 Validation Approach
[Describe how the change will be validated]
```

## Implementation Approaches

### 1. Big Bang Implementation
- Use for simple, low-risk changes with few dependencies
- Requires comprehensive communication and contingency plans

### 2. Phased Implementation
- Use for complex changes affecting multiple workflows
- Start with a pilot group, gather feedback, then expand

### 3. Parallel Implementation
- Use for high-risk changes with uncertain outcomes
- Run old and new processes in parallel to compare results

## Critical Guidelines for Workflow Modifications

### User Consent Requirements

**MANDATORY:** Obtain explicit user approval before making ANY modifications:

1. **NEVER create new workflow files** without explicit user consent
2. **NEVER modify existing workflow files** without first presenting proposed changes
3. **ALWAYS present changes as proposals first** and wait for confirmation
4. **ALWAYS explain the rationale** for proposed changes

### Modification Guidelines

- Only change workflow documents in the `_ai.bws` directory
- Never create separate "update" versions of documents
- Always modify original workflow files directly
- Use the `_ai.bws/_improvements` directory for all improvement documentation

## Improvement Directory Guidelines

The `_ai.bws/_improvements` directory is for process improvement documentation:

### File Organization

- Use descriptive kebab-case filenames
- Organize complex initiatives in subdirectories
- Use consistent naming patterns:
  - Overview documents: `-overview.md`
  - Implementation plans: `-implementation.md`
  - Scratchpads: `-scratchpad.md`

### Document Structure

- Begin with clear title and purpose statement
- Use consistent heading levels
- Include cross-references to related documents
- For complex improvements, use multiple specialized files

## Roles and Responsibilities

### Process Owner
- Responsible for overall process health
- Final decision-maker for significant changes

### Process Champion (per change)
- Leads the improvement initiative
- Creates and maintains the Proposal
- Coordinates stakeholder reviews

### Team Members
- Provide feedback on current processes
- Participate in improvement discussions
- Adapt to implemented changes

## Continuous Improvement

The Management Workflow itself should be regularly reviewed:

1. **Regular Review**: Assess effectiveness quarterly
2. **Metric Tracking**: Monitor implementation success rates
3. **Adaptation**: Refine the workflow based on experience