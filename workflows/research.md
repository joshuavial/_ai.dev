# Research Workflow

# IMPORTANT: Read Boot Protocol First
> **CRITICAL**: Before starting any research work you MUST follow the Boot protocol.
>
> 1. Read core instructions: `_ai.dev/core-instructions.md`
> 2. Follow boot sequence: `_ai.dev/protocols/boot.md`
> 3. Confirm the research workflow is the active phase before continuing

The Research Workflow standardises how agents perform web-enabled investigation, capture findings, and publish knowledge so it can fuel downstream planning and execution.

> **SCOPE**: Research work focuses on discovery, synthesis, and knowledge capture. Do **not** modify application code or project configuration while in this workflow. Implementation belongs to execution; process changes belong to management.

## Purpose
- Provide a reliable, repeatable process for answering complex questions through external research
- Ensure every investigation leaves behind durable artefacts under `_ai/research/`
- Maintain traceability from findings back to original sources
- Prepare insights so they can be consumed by planning, execution, or product stakeholders

## Required Tools & Prerequisites
- Web-search / browsing MCP tool (or equivalent) approved for use in this environment
- `Read`, `Write`, `TodoWrite`, and `shell` access for documentation tasks
- Awareness of current research backlog via `_ai/research/index.md` (create if missing)
- Any relevant task context from `_ai/tasks/`

## Artefact Structure (`_ai/research/<slug>/`)
Each research initiative MUST create or update a folder `/_ai/research/<slug>/` with the following contents:

```
_ai/research/<slug>/
├── summary.md        # Executive overview, key findings, recommendations
├── details.md        # Full analysis, timelines, supporting arguments
├── status.md         # Scratchpad of steps taken, timestamps, next actions
├── sources.md        # Index referencing cached sources/notes
└── sources/
    ├── <source-id>.md  # One file per source (URL, retrieval date, summary, quotes)
    └── ...
```

### File Expectations
- `summary.md`
  - Audience-facing digest (bullets or short sections)
  - Highlight objective, top insights, recommended actions, confidence level
- `details.md`
  - Narrative of investigation (scope decisions, analysis, comparisons)
  - Include data tables, quotes (with citations), and reasoning
- `status.md`
  - Chronological log (timestamped entries) of actions performed
  - Track open questions, pending follow-ups, blockers
- `sources.md`
  - Table or bullet index mapping source identifier → title, URL, retrieval date, relevance notes
  - Link each entry to the corresponding `sources/<id>.md`
- `sources/<id>.md`
  - Must capture:
    - Source title and canonical URL
    - Retrieval timestamp
    - Short summary (3–5 bullets)
    - Curated verbatim quotes with context (include section headings or line refs)
    - Notes on reliability or bias considerations

> **TIP**: Use consistent, slugified filenames for sources (e.g., `cloudflare-workers-docs.md`).

## Workflow Phases

### Phase 0 – Intake & Alignment
1. Confirm the request objective (who needs the research and why)
2. Check `_ai/research/index.md` for existing or related investigations
3. Update or create the research folder (`status.md` entry: objective, requester, due pressures)
4. Record initial todos using `TodoWrite` (active questions, early hypotheses)

### Phase 1 – Scoping
- Define boundaries: what will and will not be covered
- Identify key topics, search terms, stakeholders, and success criteria
- Capture scope decisions in `details.md` (Scoping section) and `status.md`
- Plan source mix (official docs, blogs, academic papers, competitors, etc.)

### Phase 2 – Discovery
- Execute web searches and targeted visits using approved tools
- For each valuable source:
  - Create `sources/<id>.md` with summary + quotes
  - Add entry to `sources.md`
- Log progress in `status.md` with timestamps
- Track leads, potential dead ends, and contradictory information

### Phase 3 – Synthesis
- Cluster findings into themes (technology options, pros/cons, metrics)
- Distill insights into `summary.md`
- Develop detailed narrative in `details.md` (include comparisons, tables, diagrams if helpful)
- Explicitly note assumptions and confidence ratings

### Phase 4 – Publication & Handoff
- Ensure `summary.md` answers the original question succinctly
- Verify `details.md` includes references to every citied source
- Update `status.md` with final actions and remaining follow-ups
- Update `_ai/research/index.md` with a new entry or revised status
- Cross-link relevant `_ai/tasks/` or GitHub issues when research informs future work

### Phase 5 – Maintenance
- Revisit research artefacts when new information arises or time-sensitive facts may have changed
- Use `status.md` to schedule reviews or flag outdated findings
- When closing out, include a final note in `status.md` indicating completion and any archival date

## Quality & Verification Checklist
- [ ] Objective and scope explicitly documented
- [ ] All required files present with up-to-date timestamps
- [ ] Every source cached under `sources/` with URL, summary, and quotes
- [ ] `summary.md` communicates key findings and recommendations clearly
- [ ] `details.md` supports conclusions with evidence and analysis
- [ ] `status.md` provides reproducible timeline of actions
- [ ] `_ai/research/index.md` updated (new row or status change)
- [ ] Sensitive data reviewed/redacted before storage

## Tool Usage Guidance
- Use the designated web-search MCP tool; respect rate limits and terms of service
- Cache critical pages manually if automated capture is unavailable (copy relevant excerpts with citations)
- Be explicit about uncertainties; mark speculative statements as such
- When web access is unavailable, note the limitation in `status.md` and surface blockers to stakeholders

## Integration With Other Workflows
- **Planning**: Link research findings to technical plans; reference `_ai/tasks/<task>/technical-plan.md`
- **Execution**: Provide implementation teams with `summary.md` and highlight critical takeaways or risks
- **QA**: Share relevant benchmarks or compliance requirements uncovered during research

## Continuity Requirements
- Every handoff must update `status.md` with current state, open questions, and next steps
- If research spans sessions, append a `## Next Session Prep` section in `details.md`
- Maintain consistent tagging in `_ai/research/index.md` for fast retrieval (e.g., topic, component, level of confidence)

Following this workflow ensures research efforts are auditable, reusable, and ready for action by the rest of the organization.
