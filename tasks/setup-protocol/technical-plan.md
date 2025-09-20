# Technical Plan: Setup Protocol

## Goal
Define a repeatable protocol that guides agents through bootstrapping `_ai.dev` and the companion `_ai/` knowledge base inside a brand-new repository.

## Desired Outcomes
- Clear prerequisites and validation checks before running any setup commands
- Step-by-step instructions for executing `_ai.dev/setup.sh`
- Guidance for initializing the `_ai/` directory without shipping templates—describe required structure instead of creating files
- Post-setup verification checklist so agents can confirm a successful installation

## Planned Deliverables
1. `_ai.dev/protocols/setup.md` documenting the full process
2. Updates to shared documentation (e.g., protocol registry in `AGENTS.md`) referencing the new setup protocol and CLI onboarding files
3. Task artefacts (`status.md`, `todos.md`) capturing progress and follow-up work

## Open Questions
- Do we need automation scripts beyond `setup.sh`? (assume no for now; document manual steps only)
- Should the protocol include optional Git configuration or remote integration notes? (flag for stakeholder review)

## Next Steps
- ✅ Draft the setup protocol document (`_ai.dev/protocols/setup.md`)
- ✅ Align references and checklists (`AGENTS.md` now lists `protocol setup`)
- ✅ Extend `_ai.dev/setup.sh` with CLI detection, onboarding doc creation, and automated prompt
- 🔄 Review with stakeholders for additional requirements
