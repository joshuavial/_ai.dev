# Gemini Adapter (Placeholder)

This placeholder documents the adapter contract for future Gemini integration. Core workflows and agents remain provider‑agnostic; this adapter will map them to Gemini’s UX and tools.

## Adapter Contract

- Invocation: How to trigger workflows, protocols, and orchestrators using Gemini prompts/UI.
- Tools mapping: Equivalents for plan tracking, shell access, file edits, and artifact/evidence handling.
- Sub‑agents: Strategy for parallelization or delegation consistent with agent docs.
- Continuity: Use `_ai/agent-state/...` for state when applicable.
- Approvals & safety: When to request elevated permissions or confirm destructive actions.
- Onboarding: Quick start for using `_ai.dev` with Gemini.

## To Do (when implementing)

- Define invocation phrases consistent with `workflow` and `protocol` triggers.
- Document tool equivalents and constraints (token limits, context size, etc.).
- Provide examples mirroring the Codex and Claude adapters for consistency.

