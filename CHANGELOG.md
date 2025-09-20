# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Current]

### Changed
- Introduced `_ai.dev/tasks/` as the canonical management-workflow task hierarchy, migrated existing improvements into task folders, and updated boot/management docs to consume the new structure.
- Condensed `_ai.dev/protocols/boot.md`, limiting boot-time reads to task indexes until a task is selected and clarifying task-specific state awareness steps.
- Documented mandatory branch and diff awareness steps (including `git status`, targeted diffs, and divergence from `main`) across boot protocol and quick-start guides.

## [1.0.1] - 2025-09-20

### Changed
- Boot protocol now keeps management sessions scoped to `_ai.dev`, including a dedicated management boot example and guidance for state notes.
- Management workflow explicitly instructs agents to skip `_ai/` artefacts during boot and review `_ai.dev` process documents instead.

## [1.0.0] - 2025-09-19

### Changed
- Updated all workflow and agent documentation references from `_ai.bws` to `_ai.dev` to align the instruction hub with the new directory structure.

## [0.8.0] - 2025-09-17

### Added
- **Task Index** – Introduced `_ai/tasks.md` as the authoritative inventory of all task folders with workflow, status, and update metadata.
- **Agent Continuity Refresh** – New task-folder-based continuity protocol with `status.md`, `todos.md`, and optional `handoff.md` guiding all handoffs.

### Changed
- **Boot Protocol** – Boot sequence now reads the task index, prompts the user to choose/create a task (or opt out), and only then selects the workflow.
- **Agent Docs** – Planning, execution, QA, management orchestrators and specialist agents now read/update continuity artefacts inside each task folder; removed legacy `.claude/state` and `_ai/agent-state` patterns.
- **Status Templates** – `status.md` format now requires an explicit workflow field and includes an example snapshot for consistency across agents.

## [0.7.0] - 2025-08-31

### Added
- **Port Management Protocol** - Standardized port allocation system with 100-port blocks per project
- **Supabase Test Database Protocol** - Isolated test DB setup with snapshot system and migration tracking
- **Developer Helper Scripts Protocol** - Standardized patterns for development shortcuts and automation
- **Manual Functional Testing** - Integrated Playwright-based test plans into planning and QA workflows
- **CI Workflow Enhancements** - GitHub Actions job now provisions Supabase services, captures credentials, runs Vitest against live endpoints, and gathers diagnostics on failure

### Removed
- **Reflection Workflow** - Merged into management workflow
- **Fast Track Workflow** - Simplified workflow options

### Changed
- **Planning Workflow** - Added manual test plan requirements
- **QA Workflow** - Added mandatory functional testing with Playwright
- **Management Workflow** - Absorbed reflection workflow responsibilities

## [0.6.3] - 2025-06-25

### Enhanced
- Zero-Tolerance Test Enforcement:
  - Added ZERO TOLERANCE policy for failing tests across execution and QA workflows
  - Mandated complete test suite verification before PR creation and task completion
  - Required human sign-off for any test failures - no exceptions allowed
  - Enhanced QA workflow to immediately stop if ANY tests fail, regardless of perceived relevance
  - Added explicit test verification commands and requirements throughout workflows
- File Management Enforcement:
  - Added mandatory file management verification as second step in QA process
  - Required verification that all changes are made to original files only
  - Prohibited creation of duplicate files with suffixes (.new, .updated, .copy, .backup, .old, etc.)
  - Added file detection commands to identify redundant files
  - Enhanced both execution and QA workflows with file cleanup requirements

## [0.6.2] - 2025-06-18

### Enhanced
- QA Workflow CI/Build Enforcement:
  - Added mandatory CI Status Verification as first step in QA process
  - QA now requires all CI checks (builds, tests, linting) to pass before proceeding
  - Updated QA report template to include CI status verification
  - Prevents approval of PRs with failing builds or linting errors
  - Enhanced QA status classification to require CI passing for approval

## [0.6.1] - 2025-06-13

### Changed
- Simplified task naming convention:
  - Removed task letter pattern (e.g., `123-a-task`) in favor of simpler naming (`123-task-name`)
  - Updated all workflow documentation to reflect new naming pattern
- Enhanced multi-task support:
  - Updated system to support multiple active tasks instead of single active task
  - Improved task status tracking across boot, current, and issue protocols
  - Streamlined execution workflow to handle concurrent task management

## [0.6.0] - 2025-12-06

### Added
- Task folder-based organization system for `_ai/` directory:
  - Standardized task folder naming: `[task-slug]`
  - Required files: `technical-plan.md`, `qa-report.md`
  - Optional folders: `tdd-evidence/`, `artifacts/`, `implementation.md`
- Comprehensive QA workflow redesign:
  - 3-question quality framework (Objective, Functional, Quality Assessment)
  - Task-level QA with actionable output documents
  - Integration with execution workflow for feedback loop
- QA-Execution integration:
  - QA produces actionable `qa-report.md` with categorized issues
  - Execution workflow handles QA feedback systematically
  - TDD-based approach to resolving QA issues

### Changed
- Planning workflow updated to create task folders instead of individual files
- Execution workflow updated to work from task folder structure
- Issue Protocol updated to reference task folders instead of individual documents
- All workflows now operate at task level with folder-based organization

### Enhanced
- Clear separation between PRDs (high-level) and task implementation (detailed)
- Improved traceability between planning, execution, and QA phases
- Streamlined documentation structure for easier reference and archival

## [0.5.4] - 2025-11-06

### Added
- Explicit prohibition of time estimates in planning workflow:
  - Added "Critical Guidelines for Planning" section with time estimate prohibition
  - Listed specific prohibited items (sprint estimates, deadlines, calendar dates, etc.)
  - Provided rationale for the prohibition
  - Suggested alternatives (complexity indicators, dependency identification, prioritization)

### Changed
- Modified "Initial Technical Analysis" to use complexity assessment instead of effort estimation
- Updated example state tracking to remove date references

## [0.5.3] - 2025-05-21

### Changed
- Reinforced file naming standards to prevent .refactored and .enhanced files:
  - Enhanced [CRITICAL:DOC-00] directive in core-instructions.md with stronger wording
  - Added explicit file management requirements to TDD protocol's REFACTOR phase
  - Added file management verification steps to execution workflow
  - Added mandatory checks for proper file modification during PR creation and completion
  - Eliminated duplicate files with version suffixes created during refactoring

## [0.5.2] - 2025-05-21

### Changed
- Simplified TDD protocol to focus on test coverage metrics:
  - Removed evidence collection requirements
  - Added emphasis on tracking test coverage percentages
  - Simplified documentation requirements for TDD phases
  - Updated execution workflow to align with new TDD approach
  - Focused on behavior coverage instead of screenshot evidence

## [0.5.1] - 2025-05-21

### Fixed
- Added critical boot protocol instructions to all workflow files:
  - Ensures proper initialization when workflows are accessed directly
  - Prevents workflows from being used as entry points without proper context
  - Requires reading core instructions before starting any workflow
  - Enforces boot protocol for consistent workflow adoption

## [0.5.0] - 2025-05-21

### Enhanced
- Streamlined all workflow documentation for better clarity and reduced token usage:
  - Simplified planning workflow (56% token reduction)
  - Simplified execution workflow (53% token reduction)
  - Simplified QA workflow (50% token reduction)
  - Simplified reflection workflow (50% token reduction)
  - Simplified management workflow (46% token reduction)
- Created new fast-track workflow for small changes and bug fixes
- Reorganized improvement documentation structure
- Updated CLAUDE.md to reference all streamlined workflows
- Added comprehensive documentation metrics and success tracking

## [0.4.2] - 2025-05-20

### Added
- New Current Work Tracking document:
  - Defines structure for tracking active PRD, Issue, and Task
  - Provides mechanism for contextual continuity across sessions
- Establishes task-folder `status.md` files as the central work reference

### Enhanced
- Updated Orientation Protocol to check current work state as first step
- Added current work verification to orientation checklist
- Improved orientation flow to prioritize current work context

## [0.4.1] - 2025-05-20

### Enhanced
- Added critical user confirmation requirement to Planning workflow:
  - Requires presenting the complete plan to the user before saving
  - Mandates explicit feedback and approval process
  - Enforces documenting user feedback and confirming final understanding
  - Provides example confirmation request format

## [0.4.0] - 2025-05-20

### Added
- New shared documents:
  - Playwright Management Guidelines - explicit requirements for handling browser automation
  - Task Management Guidelines - hierarchy and tracking for PRDs, Issues, Tasks, and Todos
  - Orientation Protocol - required process for understanding context before beginning work

### Enhanced
- Updated all workflows to reference shared documents
- Added multi-level planning capabilities to Planning workflow
- Improved error handling for browser automation
- Enhanced CLAUDE template and instructions to:
  - Require reading all linked documents before starting work
  - Trigger orientation protocol when adopting workflows
  - Confirm understanding before beginning work

## [0.3.0] - 2025-05-19

### Enhanced
- Enhanced execution workflow with mandatory test requirements:
  - Added critical instruction to always run tests after refactoring
  - Updated PR creation process to require test execution first
  - Added requirement to document test evidence after completion
  - Reinforced test verification as part of the completion checklist

## [0.2.0] - 2025-05-19

### Enhanced
- Enhanced TDD requirements across workflow documentation:
  - Added explicit TDD verification steps in execution workflow
  - Expanded test strategy planning in planning workflow
  - Improved documentation requirements in management workflow

## [0.1.0] - 2025-05-17

### Added
- Initial repository setup
- Workflow documentation:
  - Planning workflow
  - Execution workflow
  - QA workflow
  - Reflection workflow
  - Fast-track workflow
  - Management workflow
- Shared documentation:
  - GitHub issue structure template
- CLAUDE.template for project-specific Claude instructions
