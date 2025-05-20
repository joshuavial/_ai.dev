# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

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