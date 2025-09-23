# Technical Plan: Research Workflow

## Goal
Create a research-focused workflow that empowers agents to perform extensive web investigations, synthesize findings, and persist knowledge under `_ai/research/` for reuse across planning and execution tasks.

## Context & Requirements
- Workflow must be optimized for open-ended discovery, literature reviews, competitive analysis, and technology evaluations.
- Agents require a sanctioned web-search/read capability (MCP web-search tool or equivalent) with clear guidance on citation, summarization, and note-taking.
- Research outputs must be stored systematically in `_ai/research/` with metadata, summaries, references, key takeaways, and follow-up questions.
- Workflow should define handoffs to planning/execution (e.g., when research informs technical plans or implementation decisions).
- Need an indexing approach so users can quickly locate prior research artefacts.

## Planned Deliverables
1. **Workflow Doc**: `_ai.dev/workflows/research.md` outlining phases (Intake → Scoping → Discovery → Synthesis → Publication → Handoff) and embedding agent/orchestrator instructions directly within the workflow description.
2. **Research Artefact Template**: Define the canonical `_ai/research/<slug>/` layout with required files (`summary.md`, `details.md`, `status.md`, `sources.md`) and a `sources/` subdirectory of cached source notes.
3. **Command Entry Point**: `_ai.dev/commands/research.md` enabling `workflow research` or `/research` activation with guidance for invoking the research workflow agent.
4. **Tool Usage Guidelines**: Document rate limits, safe browsing practices, citation formats, data validation expectations, and how to cache source material responsibly.
5. **Quality Checklist**: Criteria for research completeness (coverage of sources, bias checks, alignment with request) plus verification of artefact structure and citation hygiene.
6. **Knowledge Index Plan**: Approach for `_ai/research/index.md` (topic, owner, summary, tags) referencing each research folder and its status.

## Current Progress
- ✅ Workflow document published (`_ai.dev/workflows/research.md`) with embedded agent guidance and quality checks
- ✅ Command entry (`_ai.dev/commands/research.md`) and `AGENTS.md` updated to advertise activation paths
- 🚧 Artefact templates and index scaffolding remain outstanding (to be produced as reusable markdown templates)

## Assumptions & Constraints
- Web access subject to approval; workflow must fall back gracefully when offline (use cached knowledge, note limitations).
- Research outputs focus on text; binary artefacts stored only when necessary and referenced.
- Privacy/security: instruct agents to avoid capturing sensitive PII unless essential and to redact before storage.
- Standardize citation format (e.g., Markdown bullet list with source URL/title/date accessed).

## Work Breakdown

### Phase 1 – Capability Assessment
- Confirm availability/configuration of web-search MCP tool (parameters, cost, concurrency).
- Inventory existing research notes under `_ai/research/` (if any) to inform template design.
- Gather stakeholder requirements for typical research tasks (technology eval, competitor scan, regulatory research, etc.).

### Phase 2 – Workflow Architecture Draft
- Define stage objectives, inputs/outputs, and decision gates (e.g., when to stop searching, how to validate coverage).
- Specify evidence collection expectations (screenshots, quotes, data tables) and storage format.
- Outline review/QA process (peer review? summary verification?).

### Phase 3 – Agent Instruction Design
- Define responsibilities for the research workflow agent (formerly “orchestrator”) directly inside the workflow doc, including startup checks, use of web-search tools, and expectations for producing artefacts.
- Identify any supporting specialized agents (e.g., source summarizer) and document how they are invoked within the workflow.
- Plan prompts/instructions to maintain consistent voice, citation style, and traceability across all research outputs.

### Phase 4 – Knowledge Base Framework
- Specify folder structure: each `_ai/research/<slug>/` must contain `summary.md` (executive overview), `details.md` (full analysis), `status.md` (timeline notes/scratchpad), `sources.md` (index of cached sources), and a `sources/` directory with one markdown file per source.
- Define required metadata/frontmatter for each file (topic, requester, objective, tags, last-updated, confidence).
- For `sources/<name>.md`, require the original URL, retrieval date, concise summary, and curated verbatim quotes with context references.
- Plan indexing strategy via `_ai/research/index.md` plus cross-references to `_ai/tasks/` when research supports active work.

### Phase 5 – Governance & Quality Controls
- Draft guidelines for verifying source credibility, highlighting conflicting information, and stating confidence levels.
- Plan tracking of research requests (maybe `_ai/research/requests.md` backlog).
- Define retention/archive policy to avoid knowledge sprawl.

### Phase 6 – Validation Planning
- Identify pilot scenarios to test the workflow (e.g., research new payment processor integration).
- Define acceptance metrics (coverage adequacy, clarity of summary, reuse metrics).

## Tooling & Automation Considerations
- Provide wrapper helpers for the web-search tool (e.g., standardized query builder, deduplication of URLs, caching top results).
- Automate creation of research file skeletons and index entries (CLI or script).
- Consider tagging integration (YAML frontmatter + `rg` friendly patterns for retrieval).

## Risks & Mitigations
- **Information overload**: Mitigate with strict summarization templates and highlight top findings first.
- **Tool outages**: Provide fallback instructions (use existing knowledge, note blockers, request human input).
- **Knowledge rot**: Require timestamps and periodic review for time-sensitive research.

## Next Actions
1. Validate plan with stakeholders and confirm priority research use-cases.
2. Draft directory and file templates for `_ai/research/<slug>/` (summary.md, details.md, status.md, sources.md, sources/*.md) and the master index.
3. Outline tool integration tasks (web-search wrapper, citation helper, file scaffolding script).
