Activate the research workflow (`/research`).

The agent will:
1. Scope the research objective and create/update `_ai/research/<slug>/`
2. Gather and cache sources using the approved web-search tool
3. Produce `summary.md`, `details.md`, `status.md`, and `sources.md` artefacts
4. Maintain a sources directory with per-source notes and citations
5. Update `_ai/research/index.md` so findings are discoverable

Usage: When you need structured web research with reusable documentation.
