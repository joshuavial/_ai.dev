# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

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
  - Uses `_ai/current.md` as the central work state reference

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