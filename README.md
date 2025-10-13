# _ai.dev

Provider-agnostic AI workflow system for project management and agentic coding.

This system has been predominantly tested with **Claude Code** and **Codex**.

## Setup

```bash
git clone https://github.com/joshuavial/_ai.dev.git
echo "_ai.dev/" >> .gitignore
./_ai.dev/setup.sh
```

## Workflows

Structured approaches for different development phases:

- **planning.md** - Technical planning, TDD strategy, package analysis, and user approval requirements
- **execution.md** - Strict TDD implementation (RED-GREEN-REFACTOR), bug investigation, zero tolerance for failing tests, file management verification
- **management.md** - Process improvement workflow for _ai.dev directory only, evolving workflows/protocols without modifying project code
- **research.md** - Web-enabled investigation with structured capture of findings under _ai/research/, source documentation and synthesis
- **qa.md** - Quality verification using 3-question framework (objectives, functionality, quality), mandatory CI verification, Playwright testing

## Protocols

System protocols for specialized operations:

- **boot.md** / **setup.md** - Initialize and configure the system
- **agent-continuity.md** - Maintain context across agent sessions
- **sub-agents.md** - Coordinate specialized sub-agents
- **tasks.md** - Task management and tracking
- **tdd.md** - Test-driven development process
- **issue.md** - Issue tracking integration
- **supabase-test-db.md** - Database testing setup
- **playwright.md** - Browser automation testing
- **port-management.md** - Development server port handling
- **dev-helper-scripts.md** - Utility scripts for development

## Commands

Slash commands for quick access to workflows:

- **/plan** - Initiate planning workflow
- **/execute** - Start execution workflow
- **/manage** - Access management tools
- **/research** - Begin research mode
- **/qa** - Run quality assurance
- **/test** - Execute testing protocols
- **/debug** - Start debugging session

## Sub-Agents

### Workflow Orchestrators
- **planning-orchestrator** - Coordinates planning activities
- **execution-orchestrator** - Manages task execution
- **management-orchestrator** - Handles project management
- **research-orchestrator** - Directs research efforts
- **qa-orchestrator** - Oversees quality assurance

### Specialized Agents
- **code-analyzer** - Analyzes code structure and quality
- **code-generator** - Generates new code
- **test-writer** - Creates test suites
- **tdd-enforcer** - Enforces test-driven development
- **bug-investigator** - Investigates and diagnoses bugs
- **refactor-specialist** - Handles code refactoring
- **security-scanner** - Scans for security issues
- **package-evaluator** - Evaluates third-party packages
- **manual-tester** - Guides manual testing procedures

### Protocol Agents
- **boot-agent** - Handles system initialization
- **issue-sync-agent** - Syncs with issue tracking systems
