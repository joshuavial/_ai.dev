# Technical Plan: DevOps Workflow

## Goal
Establish a DevOps-oriented workflow that owns local developer environment setup, CI/CD reliability, and production deployment documentation, ensuring agents can configure, diagnose, and operate infrastructure across development and production contexts.

## Context & Requirements
- Workflow must guide agents through onboarding a developer environment (install dependencies, configure services, validate builds/tests).
- Must maintain living documentation for deployment pipelines, infrastructure topology, environment variables, secrets handling, and rollback procedures.
- Agents should be able to act on requests like "set up my environment", "fix CI failure", "deploy to production", with clear checklists and automation support.
- Workflow should integrate with Execution/QA phases by providing environment readiness and deployment gates.
- Documentation and scripts belong under `_ai/devops/` (environment notes, deployment runbooks, CI guides).

## Planned Deliverables
1. **Workflow Doc**: `_ai.dev/workflows/devops.md` covering phases (Environment Provisioning → Validation → CI/CD Maintenance → Release Management → Post-Incident Review).
2. **Orchestrator Spec**: `_ai.dev/agents/workflow/devops-orchestrator.md` describing required tools (shell, docker, gh, terraform, etc.), diagnostics, and state tracking.
3. **Specialized Agents**: e.g., `_ai.dev/agents/specialized/environment-auditor.md`, `ci-doctor.md`, `deployment-runbook-agent.md` for focused tasks.
4. **Command Entry Point**: `_ai.dev/commands/workflow-devops.md` enabling `workflow devops` or `/devops` activations.
5. **Knowledge Base Structure**:
   - `_ai/devops/environments/<env>.md` (local, staging, production) with services, dependencies, setup scripts.
   - `_ai/devops/deployments.md` summarizing pipeline steps, approval flows, rollback plans.
   - `_ai/devops/ci-cd.md` documenting GitHub Actions jobs, triggers, secrets, troubleshooting tips.
   - `_ai/devops/tooling.md` listing required CLIs, versions, installation commands.
6. **Automation Plan**:
   - Scripts for environment bootstrap (`scripts/devops/bootstrap.sh`), health checks, log capture.
   - Guidance for GitHub Actions maintenance (lint/test matrix, caching, secret rotation).
   - Templates for deployment checklists and release notes.

## Assumptions & Constraints
- Access to necessary credentials is managed securely (environment variables, secret managers). Documentation must note placeholders, not real secrets.
- Workflow must respect infrastructure-as-code practices; if Terraform/Docker/Kubernetes present, include pointers to canonical configs.
- Must avoid duplicating application readme; instead reference or augment it.
- Agents operate within sandbox but plan should accommodate manual approval for privileged actions.

## Work Breakdown

### Phase 1 – Current State Assessment
- Inventory existing scripts (`scripts/`, `docker-compose.yml`, `Makefile`, etc.) and document what is missing.
- Review CI pipelines (GitHub Actions) to capture steps, dependencies, known flaky jobs.
- Gather current deployment process (manual steps, automation, branching strategy).

### Phase 2 – Workflow Design
- Define lifecycle stages, success gates, and required artefacts per stage.
- Specify onboarding checklist (OS packages, language runtimes, environment variables, database seeding, sanity tests).
- Outline release management flow (candidate build, approvals, deployment, verification, rollback, post-mortem).

### Phase 3 – Agent & Tooling Specification
- Detail orchestrator responsibilities (environment audits, verifying prerequisites, orchestrating scripts).
- Plan specialized agents (e.g., one for reading CI logs, another for verifying infrastructure).
- Identify tooling integration points (Docker, Compose, Supabase, Terraform, AWS CLI, etc.).

### Phase 4 – Knowledge Base Framework
- Define structure for environment documents: prerequisites, install steps, validation command list, troubleshooting.
- Create templates for deployment runbooks and incident reports.
- Determine how to log completed deployments (maybe `_ai/devops/releases/<date>.md`).

### Phase 5 – Automation & Script Design
- Outline bootstrap script flow (detect OS, install dependencies, run tests, output summary).
- Plan regular maintenance tasks (update dependencies, rotate secrets, sync environment docs).
- Document interactions with GitHub Actions (trigger rebuild, inspect logs, re-run workflow, update workflow files).

### Phase 6 – Validation Planning
- Define acceptance criteria (fresh environment setup success, CI pass, documented deployment path).
- Plan dry-run exercises (spin up new dev environment, perform sample deployment, simulate incident response).

## Tooling & Automation Considerations
- Provide checklists/commands for verifying installations (e.g., `node --version`, `poetry check`).
- Recommend structure for capturing system diagnostics (logs stored under `_ai/devops/logs/`).
- Outline how to script interactions with remote servers (SSH config templates, Ansible playbook references, etc.).

## Risks & Mitigations
- **Environment drift**: Mitigate with regular audits and automated bootstrap checks.
- **CI complexity**: Keep documentation synchronized with workflow files, include change history.
- **Deployment risk**: Emphasize verification and rollback steps; require runbooks for high-risk operations.

## Next Actions
1. Share plan with stakeholders for feedback and prioritization.
2. Break implementation into subtasks (workflow doc, orchestrator + agents, knowledge base scaffolding, bootstrap/CI scripts).
3. Begin capturing current environment/deployment data to populate `_ai/devops/` docs.
