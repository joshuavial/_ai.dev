# Agent Registry (Provider-Agnostic)

This registry lists the workflow orchestrators and specialized agents available in this workspace. It is provider‑agnostic: use the same concepts and files with any AI tool. Provider‑specific usage notes live under `_ai.bws/adapters/`.

## How To Invoke

- Load core docs as needed: `workflow planning`, `workflow execution`, `workflow qa`, `workflow management`, and `protocol tdd`, `protocol boot`, etc.
- Ask your AI to “activate the [orchestrator] for [context]” and follow the referenced agent doc below.
- State is stored under `_ai/agent-state/...` when agents support continuity.

## Orchestrators

- planning-orchestrator: Coordinates planning with parallel analysis
  - Doc: `_ai.bws/agents/workflow/planning-orchestrator.md`
  - Usage: “Activate planning orchestrator for issue <ID> and produce a technical plan.”

- execution-orchestrator: Manages TDD implementation
  - Doc: `_ai.bws/agents/workflow/execution-orchestrator.md`
  - Usage: “Activate execution orchestrator for task folder <path> using strict TDD.”

- qa-orchestrator: Runs quality verification
  - Doc: `_ai.bws/agents/workflow/qa-orchestrator.md`
  - Usage: “Activate QA orchestrator to verify <feature> against acceptance criteria.”

- management-orchestrator: Handles process improvement
  - Doc: `_ai.bws/agents/workflow/management-orchestrator.md`
  - Usage: “Activate management orchestrator to propose workflow improvements.”

## Specialized Agents

- tdd-enforcer: Enforces RED/GREEN/REFACTOR
  - Doc: `_ai.bws/agents/specialized/tdd-enforcer.md`
  - Use when starting a new TDD cycle or enforcing evidence collection

- code-generator: Pattern‑aware code generation
  - Doc: `_ai.bws/agents/specialized/code-generator.md`

- refactor-specialist: Code improvement without behavior change
  - Doc: `_ai.bws/agents/specialized/refactor-specialist.md`

- test-writer: Comprehensive test suites
  - Doc: `_ai.bws/agents/specialized/test-writer.md`

- security-scanner: Vulnerability detection
  - Doc: `_ai.bws/agents/specialized/security-scanner.md`

- code-analyzer: Deep codebase analysis
  - Doc: `_ai.bws/agents/specialized/code-analyzer.md`

- package-evaluator: Package scoring
  - Doc: `_ai.bws/agents/specialized/package-evaluator.md`

- manual-tester: MCP Playwright UI testing
  - Doc: `_ai.bws/agents/specialized/manual-tester.md`

- bug-investigator: Systematic bug investigation and TDD fix
  - Doc: `_ai.bws/agents/specialized/bug-investigator.md`

## Core References

- Core Instructions: `_ai.bws/core-instructions.md`
- Workflows: `_ai.bws/workflows/*.md`
- Protocols: `_ai.bws/protocols/*.md`

For provider‑specific usage (e.g., OpenAI Codex CLI), see `_ai.bws/adapters/`.

