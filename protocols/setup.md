# Setup Protocol

Use this protocol when bootstrapping `_ai.dev` (and the companion `_ai/` knowledge base) inside a new repository or when reinitialising the instruction system after a clean clone.

> **Prerequisites**
> - Confirm you are operating on the intended repository and branch (run `git status -sb`).
> - Ensure you have execution permissions for shell scripts (`chmod +x _ai.dev/setup.sh` if needed).
> - Identify whether the repository already contains `_ai.dev/` or `_ai/`; abort if conflicting structures exist.

## Step 1 – Orientation
1. Read the Boot protocol (File: `_ai.dev/protocols/boot.md`) to load core instructions.
2. Review `_ai.dev/README.md` (if present) for repository-specific notes.
3. Capture current state in a management task folder if setup spans multiple sessions.

## Step 2 – Execute Setup Script
1. From the repository root, execute:
   ```bash
   ./_ai.dev/setup.sh
   ```
2. Follow interactive prompts (if any). The script installs baseline adapters, command definitions, and workflow templates under `_ai.dev/`.
3. Once complete, inspect the output for errors. If the script fails, capture logs in the management task and resolve before proceeding.

## Step 3 – Initialize `_ai/` Knowledge Base
Instead of creating files directly, verify or describe the target structure so automated agents can scaffold it later. The desired layout is:

```
_ai/
├── README.md                 # High-level description of the knowledge base
├── index.md                  # Optional index of available research/PRDs (create when needed)
├── prds/                     # Product requirements documents (one folder or file per PRD)
├── tasks/                    # Mirrors `_ai/tasks/` when exporting summaries (rare)
├── research/                 # Research artefacts produced by the Research Workflow
│   └── <slug>/
│       ├── summary.md        # Executive summary of findings
│       ├── details.md        # Full analysis
│       ├── status.md         # Worklog / scratchpad
│       ├── sources.md        # Index of cached sources
│       └── sources/
│           └── <source>.md   # Per-source notes with URL, summary, key quotes
├── devops/                   # Environment & deployment documentation
├── github/                   # Metadata for GitHub repos/projects (IDs, fields, scripts)
└── ... (other domain folders as needed)
```

> **Action**: Document the intended `_ai/` structure in your task notes (`status.md`) and ensure future automation aligns with this blueprint. Do not create placeholder files unless the project requires them immediately.

## Step 4 – Post-Setup Verification
1. Confirm `_ai.dev/` contains core directories:
   - `agents/`, `commands/`, `protocols/`, `workflows/`, `tasks.md`
2. Confirm workflow files are accessible (e.g., verify `_ai.dev/workflows/management.md` exists and is readable).
3. Update `AGENTS.md` or project docs if repository-specific adaptations were made during setup.
4. Run `git status -sb` to verify there are no unintended modifications.

## Step 5 – Adapter Notes (Codex, Gemini, Claude)
After running the setup script, ensure the root-level onboarding files exist for any detected CLI tools and contain the minimal guidance below. Create or update each file only when the corresponding CLI is available. The setup script prints manual follow-up commands (e.g., `codex "setup the _ai folder"`); run whichever CLI you prefer to finish provider-specific initialization.

### `AGENTS.md` (Codex CLI)
```
# Codex CLI Setup

This repository uses the provider-agnostic `_ai.dev` workflow system.

1. Read File: `_ai.dev/protocols/boot.md` to load the core instructions.
2. Read File: `_ai.dev/protocols/setup.md` to bootstrap this repository.
3. Read workflow files as needed (e.g., `_ai.dev/workflows/planning.md`, `_ai.dev/workflows/execution.md`, `_ai.dev/workflows/management.md`, `_ai.dev/workflows/research.md`).

Refer to `_ai.dev/core-instructions.md` and `_ai.dev/workflows/` for the full process catalogue.
```

### `GEMINI.md` (Gemini CLI)
```
# Gemini CLI Setup

This repository uses the provider-agnostic `_ai.dev` workflow system.

1. Read File: `_ai.dev/protocols/boot.md` to load the core instructions.
2. Read File: `_ai.dev/protocols/setup.md` to bootstrap this repository.
3. Read workflow files as needed (e.g., `_ai.dev/workflows/planning.md`, `_ai.dev/workflows/execution.md`, `_ai.dev/workflows/management.md`, `_ai.dev/workflows/research.md`).

Refer to `_ai.dev/core-instructions.md` and `_ai.dev/workflows/` for the full process catalogue.
```

### `CLAUDE.md` (Claude CLI)
```
# Claude CLI Setup

This repository uses the provider-agnostic `_ai.dev` workflow system.

1. Read File: `_ai.dev/protocols/boot.md` to load the core instructions.
2. Read File: `_ai.dev/protocols/setup.md` to bootstrap this repository.
3. Read workflow files as needed (e.g., `_ai.dev/workflows/planning.md`, `_ai.dev/workflows/execution.md`, `_ai.dev/workflows/management.md`, `_ai.dev/workflows/research.md`).

Refer to `_ai.dev/core-instructions.md` and `_ai.dev/workflows/` for the full process catalogue.
```

> **Note**: Keeping these files lightweight ensures the canonical instructions remain under `_ai.dev`. Update the setup protocol if additional provider-specific steps become necessary.

## Step 6 – Record Outcome
- Update the management task `status.md` with results, outstanding follow-ups, and any deviations from the default process.
- If additional onboarding steps are required (e.g., configuring secrets, setting up GH auth), capture them in `_ai.dev/tasks/<setup-task>/todos.md` for subsequent work.

## Troubleshooting
- **Missing Script**: If `_ai.dev/setup.sh` is absent, fetch it from the upstream template repo or clone `_ai.dev` afresh.
- **Permission Denied**: `chmod +x _ai.dev/setup.sh` and rerun.
- **Conflicting Directories**: Back up existing `_ai/` or `_ai.dev/` folders before running setup to avoid destructive overwrites.
- **Network Dependencies**: Setup is designed to run offline; if optional dependencies require network access, request approval as per sandbox policy.

Following this protocol ensures every repository hosts a consistent `_ai.dev` instruction set and an aligned `_ai/` knowledge base blueprint.
