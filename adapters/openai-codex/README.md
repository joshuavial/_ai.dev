# OpenAI Codex CLI Adapter

This adapter explains how to use the `_ai.bws` workflows and agents with OpenAI Codex CLI. It does not change any core logic; it only maps the existing system to Codex CLI conventions and tools.

## Quick Start

1. Open the repository in Codex CLI.
2. Say: “protocol boot” and follow the boot steps (summarize, confirm, clarify).
3. Say: “workflow planning” or “workflow execution” as needed.
4. To use orchestrators, say: “Activate [orchestrator] for [context]” (see Agents below).

Core references live in `_ai.bws/` and remain provider‑agnostic:
- Core Instructions: `_ai.bws/core-instructions.md`
- Workflows: `_ai.bws/workflows/*.md`
- Protocols: `_ai.bws/protocols/*.md`
- Agents: `_ai.bws/agents/*`

## Invocation Patterns

- Workflows: `workflow planning`, `workflow execution`, `workflow qa`, `workflow management`
- Protocols: `protocol boot`, `protocol tdd`, `protocol issue`, etc.
- Orchestrators: “Activate planning/execution/qa/management orchestrator for <context>.”
- Specialized agents: “Run test-writer for <component>,” “Run bug-investigator on <bug-id>.”

## Tools Mapping (Codex CLI)

- Planning: Use `update_plan` with one step `in_progress` at a time. Keep steps short (5–7 words). Always mark completed before moving on.
- Shell: Use `rg` for search; prefer chunked reads (max ~250 lines). Example: `rg "TODO" -n` then `sed -n '1,200p' file`.
- Edits: Use `apply_patch` only. Don’t create new “.updated/.new” files—modify originals in place.
- Evidence: Use `view_image` to attach screenshots, or paste concise logs.
- Preambles: Before running grouped commands, write a brief (1–2 sentences) preamble describing what you’re doing.

## Approvals & Safety

- Default sandbox: workspace‑write, network restricted, approvals on‑request.
- Request escalation only when necessary (e.g., network, writing outside workspace, destructive ops). Provide a one‑sentence justification.
- Avoid destructive actions unless explicitly requested. Prefer additive, documented changes.

## Continuity & State

- Where supported, agents record state under `_ai/agent-state/...` as referenced in their docs.
- Resume by reading the relevant state file, then continue per the agent’s continuity protocol.

## Validation Philosophy

- If the repo has tests, start with specific tests for changed code, then run broader suites.
- Don’t fix unrelated failures; surface them separately.
- For TDD, follow the RED → GREEN → REFACTOR loop and keep brief evidence.

## Agents (Using Orchestrators with Codex CLI)

Orchestrators and specialized agents are defined under `_ai.bws/agents/`. Use neutral prompts:

- Planning: “Activate planning orchestrator for issue <ID>. Produce technical plan and risks.”
- Execution: “Activate execution orchestrator for task <path>. Enforce strict TDD and update state.”
- QA: “Activate QA orchestrator for <feature>. Validate acceptance criteria and regression tests.”
- Management: “Activate management orchestrator. Propose process improvements and next steps.”

See `_ai.bws/adapters/openai-codex/agents.md` for more examples.

## Notes

- This adapter does not modify `_ai.bws/setup.sh` or `.claude/` symlinks. Claude integration remains intact.
- Future providers (e.g., Gemini) follow the same adapter contract under `_ai.bws/adapters/`.

