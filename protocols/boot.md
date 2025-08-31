# LLM Boot Sequence Protocol

This document defines the initialization process for LLMs working on this project. It provides a clear, ordered sequence of operations that should be performed at the start of each session to establish proper context and state awareness.

## Boot Sequence Overview

When starting a new session or after context clearing:

1. **Read CLAUDE.md** - Establish base project understanding
2. **Load current.md** - Determine current project state
3. **Determine active workflow** - Based on current.md
4. **Load workflow-specific instructions** - From core instructions
5. **Establish awareness** - Confirm understanding before proceeding

## 1. Read CLAUDE.md

The first step in any session is to read the project's base configuration:

```
Read CLAUDE.md
```

CLAUDE.md contains:
- Project-specific guidelines
- Development environment details
- Command reference
- Workflow system reference

This establishes foundational knowledge about the project and its structure.

## 2. Load current.md

After establishing baseline understanding, determine the current project state:

```
Read _ai/current.md
```

The current.md file contains structured data about:
- Active PRD (or null)
- Active Issue (or null)
- Active Task (or null)
- Current workflow phase
- Next workflow phase (if transitioning)
- State-specific context

## 3. Determine Active Workflow

Based on the workflow phase indicated in current.md, identify which workflow is active:

```
# Example current.md content
{
  "active_prd": "user-onboarding-system",
  "active_issue": 123,
  "active_task": "wizard-navigation-component",
  "workflow_phase": "execution",
  "next_workflow": null,
  "state_notes": "Implementation of navigation component in progress"
}
```

In this example, the active workflow is "execution".

## 4. Load Workflow Instructions

Once the active workflow is identified, load the core instructions for that workflow:

```
# Read core instructions
Read _ai.bws/core-instructions.md

# Filter for relevant workflow instructions
Apply [WORKFLOW:${workflow_phase}] instructions
```

The core-instructions.md contains tagged instructions for each workflow phase.

## 5. Establish Awareness

Before taking any action, establish awareness of the current state and confirm understanding:

```
# If current.md indicates a specific issue
if [[ -n "$active_issue" ]]; then
  # Load issue protocol for reference
  Read File: `_ai.bws/protocols/issue.md`
  
  # Check GitHub issue state
  gh issue view $active_issue
  
  # Check KB documentation
  Read _ai/issues/${active_issue}-*.md
  Read _ai/states/${active_issue}-current-state.md
  
  # If there are active tasks
  if [[ active_tasks != "None" ]]; then
    Read task folders and technical plans for active tasks
  fi
fi

# Check todos
Read current todo list
```

## 6. Confirm Understanding with User

After completing the boot sequence, provide a concise summary to the user:

```
Based on my initialization:
- Project: [from CLAUDE.md]
- Current workflow: [workflow_phase]
- Active issue: #[active_issue] [issue title]
- Active tasks: [list of active tasks with status]
- Current status: [from state document]
- Next steps: [from todos or state document]

Is this understanding correct before I proceed?
```

Wait for user confirmation before taking any actions.

## Workflow Transitions

When current.md indicates a workflow transition:

```
# Example transitioning state
{
  "active_prd": "user-onboarding-system",
  "active_issue": 123,
  "active_task": null,
  "workflow_phase": "execution",
  "next_workflow": "qa",
  "state_notes": "Implementation complete, ready for QA"
}
```

1. **Complete current workflow** - Finish any remaining tasks in the current workflow
2. **Acknowledge transition** - Inform user of imminent transition
3. **Load next workflow** - Read instructions for the upcoming workflow
4. **Update current.md** - After transition approval, update workflow_phase and clear next_workflow

## Workflow Phase-Specific Boot Extensions

### Planning Phase Boot

When in planning phase, additionally:
- Read related PRD document if specified
- Check for similar existing issues or features
- Prepare issue structure templates

### Execution Phase Boot

When in execution phase, additionally:
- Check branch status and name
- Review technical plan
- Verify test environment
- Check TDD evidence requirements

### QA Phase Boot

When in QA phase, additionally:
- Load QA checklist templates
- Check testing requirements
- Verify implementation completeness

## Implementation Details

### current.md Format

The current.md file should be a structured JSON or YAML file:

```json
{
  "active_prd": "string | null",
  "active_issue": "number | null",
  "active_task": "string | null",
  "workflow_phase": "planning | execution | qa | management",
  "next_workflow": "string | null",
  "state_notes": "string",
  "last_updated": "ISO timestamp",
  "context": {
    "additional_context_key1": "value1",
    "additional_context_key2": "value2"
  }
}
```

### Workflow Identification

Workflows are identified by simple string identifiers:
- `planning` - Planning phase
- `execution` - Execution phase 
- `qa` - Quality Assurance phase
- `management` - Process improvement and workflow evolution

### Example Boot Sequence

```
# 1. Read base project configuration
Read CLAUDE.md

# 2. Load current state
Read _ai/current.md
# {
#   "active_prd": "user-onboarding",
#   "active_issue": 123,
#   "active_task": "wizard-navigation",
#   "workflow_phase": "execution",
#   "next_workflow": null,
#   "state_notes": "Implementing wizard navigation component"
# }

# 3. Determine we're in execution workflow

# 4. Load execution workflow instructions
Read _ai.bws/core-instructions.md
# Filter for [WORKFLOW:execution] instructions

# 5. Establish state awareness
Read File: `_ai.bws/protocols/issue.md`
Check GitHub issue #123
Read _ai/issues/123-user-onboarding-wizard.md
Read _ai/states/123-current-state.md
Read _ai/tasks/123-wizard-navigation.md
Check todo list

# 6. Confirm understanding with user
# Provide state summary and await confirmation
```

## Benefits of This Approach

This boot sequence approach offers several advantages:

1. **Clean Start Capability** - Works after context clearing
2. **Consistent State Management** - Single source of truth in current.md
3. **Minimal Redundancy** - No need for transition-specific documentation
4. **Workflow Flexibility** - Easy to modify workflow phases
5. **Clarity for LLMs** - Explicit, ordered instructions reduce confusion
6. **User Confirmation** - Verifies correct understanding before proceeding

## Implementation Notes

1. The current.md file should be updated:
   - When starting a new feature
   - When completing a workflow phase
   - When transitioning between workflows
   - When changing active tasks

2. LLMs should follow this exact boot sequence after context clearing

3. Workflow-specific instructions should be tagged in core-instructions.md to allow filtering