# Claude Code Adapter (Existing)

This repository already includes integration for Claude Code via symlinked agents and commands. This document summarizes the current setup for parity with other adapters.

## Setup

- Run `_ai.bws/setup.sh` from the repo root to configure symlinks:
  - Creates/updates `.claude/agents` → `_ai.bws/agents`
  - Creates/updates `.claude/commands` → `_ai.bws/commands`
  - Adds `_ai.bws`, `_ai.bws/.claude`, and `.wt.conf` to `.gitignore` if missing

## Usage

- Slash commands available in Claude Code:
  - `/plan` → planning orchestrator
  - `/execute` → execution orchestrator (TDD)
  - `/qa` → QA orchestrator
  - `/manage` → management orchestrator
  - `/test` → manual testing agent

- Core instruction and workflow files remain under `_ai.bws/` and are provider‑agnostic.

## Notes

- This adapter is documentation‑only; the actual behavior is implemented by `_ai.bws/setup.sh` and the symlinked agent definitions.
- No changes to Claude integration were made as part of the provider‑agnostic adapter work.

