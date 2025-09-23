# Status: research-workflow

- **Workflow**: management
- **Updated**: 2025-09-21
- **Progress**:
  - Research workflow document created (`_ai.dev/workflows/research.md`) with embedded agent guidance and artefact structure
  - Command entry (`_ai.dev/commands/research.md`) and `AGENTS.md` updated to expose the workflow
  - Research orchestrator spec added (`_ai.dev/agents/workflow/research-orchestrator.md`) to enable `/research`
  - Core registry updated (`_ai.dev/core-instructions.md`) to list the research workflow alongside other phases
- **Next Steps**:
  - [ ] Review plan with stakeholders and confirm priority research use-cases
  - [ ] Draft templates for `_ai/research/<slug>/` (summary.md, details.md, status.md, sources.md, sources/*.md) and the `_ai/research/index.md`
  - [ ] Outline tool integration tasks for web-search, citation helpers, and file scaffolding
- **Blockers**: None
- **Best Practices & Patterns**:
  - Standardise source capture: slugify filenames and record retrieval timestamps to simplify reuse
  - Update `_ai/research/index.md` during publication so downstream workflows can discover findings quickly
