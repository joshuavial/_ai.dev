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
2. Task: Significant unit of work tracked in `_ai/tasks/` (hours)
3. Todo: Small, actionable item (minutes)

> External trackers (e.g., GitHub issues) can mirror or reference tasks when collaboration outside `_ai/` is required, but they are not part of the core hierarchy.

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
protocol playwright
protocol sub-agents

# Agent activation (via slash commands)
/plan       # Planning orchestrator
/execute    # Execution orchestrator
/qa         # QA orchestrator
/manage     # Management orchestrator
/test       # Manual testing agent
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

3. **Tasks Protocol**: File: `_ai.bws/protocols/tasks.md`
   - When organizing work into task folders under `_ai/tasks/`
   - When tracking progress across planning, execution, and QA
   - When breaking tasks into actionable todos

4. **KB Sync Protocol**: File: `_ai.bws/protocols/kb-sync/kb-github-sync.md`
   - When bidirectional synchronization is needed between Knowledge Base and GitHub
   - When extracting documentation from GitHub issues
   - When creating shared documentation mirrors

5. **Playwright Protocol**: File: `_ai.bws/protocols/playwright.md`
   - When automating browser interactions
   - When creating UI tests
   - When documenting UI testing approaches

6. **Port Management Protocol**: File: `_ai.bws/protocols/port-management.md`
   - When managing port assignments for development projects
   - When organizing port allocation across multiple instances
   - When preventing port conflicts in development environments

7. **Supabase Test Database Protocol**: File: `_ai.bws/protocols/supabase-test-db.md`
   - When setting up isolated test databases for Supabase projects
   - When implementing database snapshot systems for testing
   - When configuring test runners with Supabase integration
   - When optimizing test performance with database restoration

8. **Developer Helper Scripts Protocol**: File: `_ai.bws/protocols/dev-helper-scripts.md`
   - When creating shortcuts for common development tasks
   - When setting up port-aware development scripts
   - When wrapping complex commands with environment configuration
   - When standardizing team development workflows

9. **Sub-Agents Protocol**: File: `_ai.bws/protocols/sub-agents.md`
   - When using Claude Code sub-agents for workflow acceleration
   - When setting up agents for new projects
   - When invoking specialized agents for tasks
   - When coordinating parallel agent execution

> **Note:** The Issue Protocol (`_ai.bws/protocols/issue.md`) remains available as a specialized reference whenever GitHub issue coordination is required, but task documentation within `_ai/` serves as the primary source of truth for in-progress work.

### Workflow Phases

1. **Planning**: File: `_ai.bws/workflows/planning.md`
   - When understanding requirements
   - When creating technical plans
   - When breaking down work into tasks
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
- **AGENT**: Sub-Agent Operations

---

## Sub-Agent Integration [AGENT]

[DEFINITION:AGENT-01] Sub-agents are specialized AI assistants that:
1. Operate in independent 200k token contexts
2. Can run in parallel (up to 10 simultaneously)
3. Are invoked via the Task tool
4. Return structured results to parent context

[REQUIREMENT:AGENT-02] When using sub-agents:
1. Agents are defined in `_ai.bws/agents/` directory
2. Workflow orchestrators coordinate specialized agents
3. Clear task definition required for each invocation
4. Results must be validated before use

[GUIDELINE:AGENT-03] Agent best practices:
1. Use orchestrators for workflow phases
2. Delegate specialized tasks to appropriate agents
3. Run independent tasks in parallel
4. Provide sufficient context in prompts
5. Monitor agent performance and refine prompts

[REGISTRY:AGENT-04] Available agent types:
1. **Workflow Orchestrators**: 
   - `planning-orchestrator` - Coordinates planning with parallel analysis
   - `execution-orchestrator` - Manages TDD implementation
   - `qa-orchestrator` - Runs quality verification
   - `management-orchestrator` - Handles process improvement

2. **Specialized Agents**: 
   - `code-analyzer` - Deep codebase analysis
   - `code-generator` - Pattern-aware code generation
   - `tdd-enforcer` - Strict TDD compliance
   - `test-writer` - Comprehensive test suites
   - `package-evaluator` - Package scoring matrix
   - `manual-tester` - MCP Playwright UI testing
   - `security-scanner` - Vulnerability detection
   - `refactor-specialist` - Code improvement

3. **Protocol Agents**: 
   - `boot-agent` - Context initialization
   - `issue-sync-agent` - GitHub-KB synchronization

[PROCESS:AGENT-05] Agent invocation process:
1. Identify task requiring specialization
2. Select appropriate agent from registry
3. Provide clear context and requirements
4. Invoke via Task tool or slash command
5. Validate and integrate results

[WORKFLOW:AGENT-06] Workflow-specific agent usage:
- **Planning**: Use planning-orchestrator to coordinate analysis
- **Execution**: Use execution-orchestrator for TDD implementation
- **QA**: Use qa-orchestrator for parallel verification
- **Management**: Use management-orchestrator for improvements
