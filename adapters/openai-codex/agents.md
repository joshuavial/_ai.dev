# Codex CLI Agent Playbook

This playbook maps neutral agent/orchestrator actions to Codex CLI usage. It references existing agent documents and provides copy‑paste prompts.

## Orchestrators

- Planning Orchestrator
  - Doc: `_ai.bws/agents/workflow/planning-orchestrator.md`
  - Prompt: “Activate planning orchestrator for issue <ID>. Read core-instructions boot guidance, confirm context, then produce a technical plan with risks, dependencies, and test strategy.”

- Execution Orchestrator
  - Doc: `_ai.bws/agents/workflow/execution-orchestrator.md`
  - Prompt: “Activate execution orchestrator for task folder `_ai/tasks/<issue-id>-<task-name>/`. Enforce strict TDD (RED/GREEN/REFACTOR), track state in `_ai/agent-state/execution/orchestrator-state.md`, and provide brief evidence after each phase.”

- QA Orchestrator
  - Doc: `_ai.bws/agents/workflow/qa-orchestrator.md`
  - Prompt: “Activate QA orchestrator for <feature>. Verify acceptance criteria, edge cases, and regression. Summarize defects with reproduction steps.”

- Management Orchestrator
  - Doc: `_ai.bws/agents/workflow/management-orchestrator.md`
  - Prompt: “Activate management orchestrator. Review workflow adherence, propose improvements, and create actionable next steps.”

## Specialized Agents

- TDD Enforcer
  - Doc: `_ai.bws/agents/specialized/tdd-enforcer.md`
  - Use: “Run tdd-enforcer for <component>. Write failing tests (RED) first and capture short evidence.”

- Code Generator
  - Doc: `_ai.bws/agents/specialized/code-generator.md`
  - Use: “Run code-generator to implement minimal changes required to pass tests.”

- Refactor Specialist
  - Doc: `_ai.bws/agents/specialized/refactor-specialist.md`
  - Use: “Run refactor-specialist to improve clarity and remove duplication without changing behavior.”

- Test Writer
  - Doc: `_ai.bws/agents/specialized/test-writer.md`
  - Use: “Run test-writer to expand coverage for <feature>, including happy paths and edge cases.”

- Security Scanner
  - Doc: `_ai.bws/agents/specialized/security-scanner.md`
  - Use: “Run security-scanner to identify vulnerabilities relevant to recent changes.”

- Bug Investigator
  - Doc: `_ai.bws/agents/specialized/bug-investigator.md`
  - Use: “Run bug-investigator for bug <ID>. Perform analysis, reproduce with failing test, fix via TDD, and add regression tests.”

- Manual Tester
  - Doc: `_ai.bws/agents/specialized/manual-tester.md`
  - Use: “Run manual-tester to validate UI flows for <feature> using Playwright MCP.”

## Codex CLI Tips

- Always add a short preamble before grouped tool calls (what you’re about to do and why).
- Keep `update_plan` with exactly one `in_progress` step; mark completed steps promptly.
- Prefer `rg` for search and chunk file reads to ~250 lines.
- Use `apply_patch` for edits and avoid creating parallel file copies with suffixes.
- Attach evidence via `view_image` or paste concise logs.

