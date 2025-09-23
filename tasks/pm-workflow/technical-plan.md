# Technical Plan: Project Management Workflow

## Goal
Design a dedicated project management workflow that makes it effortless to create, track, and evolve GitHub issues and Projects from within the `_ai.dev` agent system while keeping a synchronized knowledge base of project metadata under `_ai/github/`.

## Context & Requirements
- Workflow must center on GitHub issue lifecycle management (creation, triage, grooming, iteration planning, review, closure).
- Agents need direct access to the GitHub CLI (`gh`) plus custom helpers to work around limited Projects support.
- System should maintain an authoritative reference of organization, repositories, project IDs, field definitions, iteration cadence, and automation notes under `_ai/github/`.
- Users should be able to request actions such as "create issue", "move to iteration", or "update status" and have the workflow orchestrator coordinate the necessary steps.
- Workflow outcomes must feed downstream phases (planning/execution) by linking to `_ai/tasks/` artefacts when appropriate.

## Planned Deliverables
1. **Workflow Doc**: `_ai.dev/workflows/pm.md` describing phases (Intake → Triage → Planning Sync → Tracking → Review/Close), required artefacts, and success checks.
2. **Orchestrator Spec**: `_ai.dev/agents/workflow/pm-orchestrator.md` covering startup checklist, tools (GH CLI, custom scripts, TodoWrite), state handling, and outputs.
3. **Command Entry Point**: `_ai.dev/commands/workflow-pm.md` (or similar) enabling activation via `workflow pm` or `/pm` shortcuts.
4. **Knowledge Base Structure**:
   - `_ai/github/projects-reference.md`: project meta (IDs, GraphQL node IDs, custom fields, iterations, automation rules).
   - `_ai/github/repos.md`: canonical repository list with default labels, CODEOWNERS, branch policies.
   - `_ai/github/workflows.md`: conventions for backlog grooming, standups, reporting cadence.
5. **Automation Toolkit Outline**:
   - Spec for a helper script (e.g., `scripts/pm/projects-cli.sh`) wrapping `gh api` calls (create/update issue, add to project, set field values, change status, fetch iteration list).
   - Sample config file (YAML/JSON) describing environment variables (token scopes, org, project IDs).
6. **Integration Guidelines**: How PM workflow coordinates with Planning/Execution tasks (link issues ↔ tasks, maintain cross-references).

## Assumptions & Constraints
- GitHub CLI authenticated with necessary scopes (`repo`, `project`, `org:read`, `project:write`).
- Custom script can rely on environment variables stored in `.env` or secure keychain; documentation must avoid leaking secrets.
- Workflow must not duplicate `_ai/tasks/` content; instead, it references or generates tasks as needed.
- Automation should use GraphQL for Projects (v2) to support setting fields/iterations.

## Work Breakdown

### Phase 1 – Discovery & Inventory
- Audit existing GitHub usage (issues, labels, projects, automation) to capture baseline practices.
- Define canonical metadata capture format for `_ai/github/projects-reference.md` (fields: project name, ID, owner, iteration length, status field IDs, custom fields).
- Identify gaps in current GH CLI capabilities; list operations requiring custom scripts.

### Phase 2 – Workflow Architecture Draft
- Map lifecycle stages: Intake (capture requests), Analysis (triage/prioritize), Planning Sync (align with tasks/iterations), Tracking (daily updates), Review/Closure (retrospective, metrics).
- For each stage, specify required inputs/outputs, checklists, and decision gates.
- Determine required task artefacts (e.g., `pm-log.md` capturing meeting notes or weekly summaries).

### Phase 3 – Agent & Command Specification
- Define orchestrator responsibilities (loading metadata, verifying GH auth, presenting options, executing commands).
- Outline specialized helper agents if needed (e.g., `github-projects-sync`, `issue-auditor`).
- Plan command semantics (`workflow pm`, `/pm`) and default prompts.

### Phase 4 – Tooling Blueprint
- Document CLI workflows for issue creation, labeling, assigning, and linking to `_ai/tasks/`.
- Draft pseudo code/flow for project updates (GraphQL queries/mutations, error handling, dry-run mode).
- Outline logging/telemetry approach (store results under `_ai/github/logs/`?).

### Phase 5 – Documentation Integration
- Identify existing docs requiring updates (`AGENTS.md`, `core-instructions`, `management workflow`, maybe `CLAUDE.md`).
- Design onboarding guide for users describing prerequisites (install GH CLI, login, set environment variables).

### Phase 6 – Validation Planning
- Define success criteria (issues created with correct metadata, project updates succeed, cross-references maintained).
- Plan test scenarios for automation scripts (dry-run, sample issue, status transitions).

## Tooling & Automation Considerations
- Likely need a JSON/YAML mapping file under `_ai/github/` enumerating project/field IDs for script consumption.
- Evaluate using `gh project item-edit` vs direct GraphQL for reliability.
- Consider caching project schema responses to reduce API calls.
- Provide troubleshooting guide for common failures (missing scopes, stale field IDs, network errors).

## Risks & Mitigations
- **GH Projects API volatility**: Mitigate by versioning schema snapshots and providing fallback manual steps.
- **Auth/permission issues**: Document required scopes and validation checks (e.g., `gh auth status`).
- **Knowledge base drift**: Establish routine (maybe weekly) to sync `_ai/github/` metadata with live project state.

## Next Actions
1. Validate this plan with stakeholders and adjust scope/priorities.
2. Break into implementation subtasks (workflow doc, orchestrator spec, automation toolkit, knowledge base scaffolding).
3. Start collecting current project metadata to seed `_ai/github/projects-reference.md`.
