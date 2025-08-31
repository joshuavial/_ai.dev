# Core Instructions for LLM Workflows

[CRITICAL:DOC-00] NEVER create new versions of files with suffixes like .updated, .new, .enhanced, .refactored etc. ALWAYS edit the original file directly. This is especially important during refactoring tasks - when improving code, you must modify the original file in place, not create alternate versions. Violation of this directive leads to confusion, duplicate files, and maintenance issues.

This document serves as the single source of truth for workflow instructions across all processes. Each instruction has a unique identifier and is categorized by type and applicable workflows.

---

## Core Definitions and Concepts

[DEFINITION:WF-01] Workflow phases:
1. Planning: Requirements analysis and technical planning
2. Execution: Implementation using TDD
3. QA: Quality verification and testing
4. Management: Process improvement and workflow evolution

[DEFINITION:TDD-01] The TDD cycle consists of three phases:
1. RED: Write failing tests before implementation
2. GREEN: Implement minimal solution to make tests pass
3. REFACTOR: Improve implementation without changing behavior

[DEFINITION:TASK-01] Work hierarchy consists of:
1. PRD: High-level feature area (weeks/months)
2. Issue/Ticket: Substantial component (days)
3. Task: Significant unit of work (hours)
4. Todo: Small, actionable item (minutes)

---

## Quick Reference

To activate a specific document, use these standard patterns:

```
# Workflow activation
workflow planning
workflow execution
workflow qa
workflow management

# Protocol usage
protocol boot
protocol tdd
protocol issue
protocol tasks
protocol current
protocol playwright
```

---

## Boot Requirements [BOOT]

[REQUIREMENT:BOOT-01] After loading the boot protocol or context loading, MUST:
1. Summarize understanding of current state
2. Present summary to user for confirmation
3. Ask clarifying questions about anything unclear
4. Wait for explicit confirmation before proceeding

---

## Documentation Guidelines [DOC]

[REQUIREMENT:DOC-02] All documentation MUST:
1. Use consistent formatting and terminology
2. Reference other documents rather than duplicate content
3. Be updated when related processes change
4. Include clear markers for LLMs to parse

[GUIDELINE:DOC-03] Documentation should:
1. Prioritize clarity and precision over verbosity
2. Use consistent formatting for instruction types
3. Include examples only for complex processes
4. Minimize token usage when loaded by LLMs
5. Use references instead of duplicating content

---

## Document Types and Registry

[REGISTRY:PROTO-01] Available documents and when to use them:

### Core Protocols

1. **Boot Protocol**: File: `_ai.bws/protocols/boot.md`
   - When starting a new session
   - When loading or reloading context
   - When orienting to the current project state

2. **TDD Protocol**: File: `_ai.bws/protocols/tdd.md`
   - When implementing code using Test-Driven Development
   - When documenting TDD evidence
   - When verifying test coverage and quality

3. **Issue Protocol**: File: `_ai.bws/protocols/issue.md`
   - When creating or updating GitHub issues
   - When synchronizing with Knowledge Base
   - When managing issue state and comments

4. **KB Sync Protocol**: File: `_ai.bws/protocols/kb-sync/kb-github-sync.md`
   - When bidirectional synchronization is needed between KB and GitHub
   - When extracting documentation from GitHub issues
   - When creating KB structure for new issues

5. **Tasks Protocol**: File: `_ai.bws/protocols/tasks.md`
   - When breaking down work into PRD/Issue/Task/Todo hierarchy
   - When tracking progress across work levels
   - When organizing and prioritizing tasks

6. **Current Protocol**: File: `_ai.bws/protocols/current.md`
   - When switching between tasks or issues
   - When updating work state across sessions
   - When documenting context for a task

7. **Playwright Protocol**: File: `_ai.bws/protocols/playwright.md`
   - When automating browser interactions
   - When creating UI tests
   - When documenting UI testing approaches

8. **Port Management Protocol**: File: `_ai.bws/protocols/port-management.md`
   - When managing port assignments for development projects
   - When organizing port allocation across multiple instances
   - When preventing port conflicts in development environments

9. **Supabase Test Database Protocol**: File: `_ai.bws/protocols/supabase-test-db.md`
   - When setting up isolated test databases for Supabase projects
   - When implementing database snapshot systems for testing
   - When configuring test runners with Supabase integration
   - When optimizing test performance with database restoration

10. **Developer Helper Scripts Protocol**: File: `_ai.bws/protocols/dev-helper-scripts.md`
   - When creating shortcuts for common development tasks
   - When setting up port-aware development scripts
   - When wrapping complex commands with environment configuration
   - When standardizing team development workflows

### Workflow Phases

1. **Planning**: File: `_ai.bws/workflows/planning.md`
   - When understanding requirements
   - When creating technical plans
   - When breaking down issues into tasks
   - When defining test strategy
   - When identifying dependencies and risks

2. **Execution**: File: `_ai.bws/workflows/execution.md`
   - When implementing planned features using TDD
   - When creating all required components
   - When following technical plan
   - When documenting test evidence
   - When updating current state continuously

3. **QA**: File: `_ai.bws/workflows/qa.md`
   - When verifying implementation meets requirements
   - When testing all edge cases
   - When performing regression testing
   - When validating acceptance criteria
   - When identifying any issues or bugs

4. **Management**: File: `_ai.bws/workflows/management.md`
   - When improving development processes
   - When refining workflows and documentation
   - When standardizing approaches
   - When implementing process improvements
   - When evolving the workflow system

[PROCESS:PROTO-02] How to activate documents:

1. To load a protocol or workflow, use these directives:
   ```
   # For workflows:
   workflow planning
   workflow execution
   workflow qa
   
   # For protocols:
   protocol tdd
   protocol boot
   protocol issue
   protocol port-management
   protocol supabase-test-db
   protocol dev-helper-scripts
   ```

2. This will trigger loading of the appropriate document

3. Follow the document's procedures for the specific task

4. Reference loaded protocols when following their processes

[GUIDELINE:PROTO-03] Document lazy-loading advantages:
1. Reduced token usage in initial context
2. Only load documents relevant to current task
3. More precise focus on specific processes
4. Protocols and workflows can be updated without changing core instructions

---

## Instruction Document Format

### How to Use This Document

- Each instruction has a unique ID in the format [TYPE:CATEGORY-NN]
- Reference specific instructions in workflow documents using their ID
- Instructions are grouped by category and function
- Apply instructions based on the specified applicable workflows

### Instruction Types

- **[CRITICAL]**: Critical instruction that MUST NEVER be violated under any circumstances
- **[REQUIREMENT]**: Mandatory instruction that MUST be followed
- **[PROCESS]**: Step-by-step procedure to be executed
- **[GUIDELINE]**: Best practice recommendation that SHOULD be followed
- **[DEFINITION]**: Core concept or term definition
- **[WORKFLOW]**: Workflow-specific instruction set
- **[REFERENCE]**: Reference to external documentation
- **[REGISTRY]**: Index of available resources

### Categories

- **GH**: GitHub Issue Management
- **TDD**: Test-Driven Development
- **DOC**: Documentation Standards
- **WF**: Workflow Operations
- **BOOT**: Boot Procedures
- **TASK**: Task Management
- **PROTO**: Protocol Management
