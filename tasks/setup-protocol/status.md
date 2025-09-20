# Status: setup-protocol

- **Workflow**: management
- **Updated**: 2025-09-21
- **Progress**:
  - Task created and setup protocol drafted (`_ai.dev/protocols/setup.md`)
  - Global protocol list in `AGENTS.md` updated to reference `protocol setup`
  - `_ai.dev/setup.sh` enhanced to detect Codex/Gemini/Claude CLIs, generate CLI notes, surface manual follow-up commands, and tolerate pre-existing symlinks
  - Setup script verified locally (`./_ai.dev/setup.sh`); script now simply prints `codex|gemini|claude "setup the _ai folder"` for human execution
  - Setup protocol now documents the required content for `AGENTS.md`, `GEMINI.md`, and `CLAUDE.md` when respective CLIs are present
- **Next Steps**:
  - [ ] Review protocol with stakeholders for additional requirements
  - [ ] Add follow-up guidance if `_ai/` needs project-specific variants
- **Blockers**: None
