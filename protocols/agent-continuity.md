# Agent Continuity Protocol

## Overview

Sub-agents are stateless and start with fresh context on each invocation. This protocol defines how agents handle interruptions and continue work through external state persistence.

## Core Strategy: File-Based State Persistence

Since agents cannot maintain memory between invocations, we use a **file-based checkpoint system** where agents:
1. Write progress to persistent files
2. Check for existing progress on startup
3. Resume from documented checkpoints
4. Update state files incrementally

## State File Locations

```
_ai/
├── agent-state/           # Agent progress tracking
│   ├── planning/          # Planning phase state
│   ├── execution/         # Execution phase state
│   └── qa/                # QA phase state
├── checkpoints/           # Discrete checkpoint files
└── analysis/              # Incremental analysis outputs
```

## Standard State File Format

```markdown
# Agent State: [Agent Name] - [Task ID]

## Status
- **Started**: [timestamp]
- **Last Updated**: [timestamp]  
- **Completion**: [percentage]%

## Completed Steps
- [x] Step 1 description
- [x] Step 2 description

## In Progress
- [ ] Current step (50% complete)
  - Sub-task details
  - Last action taken

## Pending Steps
- [ ] Next step
- [ ] Future step

## Context for Resumption
- **Working Directory**: [path]
- **Current File**: [filename]
- **Last Command**: [command]
- **Dependencies Installed**: [list]
- **Errors Encountered**: [any errors]

## Output Files Created
- `path/to/file1.md` - Description
- `path/to/file2.ts` - Description
```

## Agent Startup Protocol

Every agent MUST follow this startup sequence:

```python
1. Check for existing state file
   state_file = f"_ai/agent-state/{workflow}/{task_id}-state.md"
   
2. If state exists:
   - Read previous progress
   - Identify incomplete work
   - Resume from last checkpoint
   
3. If no state:
   - Create new state file
   - Initialize progress tracking
   
4. Update state file regularly:
   - After each completed step
   - Before starting time-consuming operations
   - When encountering errors
```

## Checkpoint Strategy

### Major Checkpoints
Create separate checkpoint files for significant milestones:

```markdown
# _ai/checkpoints/planning-requirements-complete.md
Requirements analysis completed at [timestamp]
- Analyzed [N] user stories
- Identified [N] technical requirements
- Next: Package evaluation
```

### Minor Checkpoints
Update the main state file for smaller progress points:

```markdown
## In Progress
- [ ] Analyzing codebase (75% complete)
  - ✓ Frontend components analyzed
  - ✓ Backend services analyzed
  - ⚠️ Database schema - IN PROGRESS
  - ○ Test coverage analysis - PENDING
```

## Interruption Handling

When an agent is interrupted (timeout, error, user cancellation):

1. **Graceful Degradation**
   - Save current progress immediately
   - Document the interruption point
   - Include context for resumption

2. **Recovery Instructions**
   ```markdown
   ## Interruption Point
   - **Reason**: Timeout/Error/User Cancel
   - **Last Action**: [what was being done]
   - **To Resume**: [specific instructions]
   - **Command to Continue**: Task("[agent]", "Continue from state file")
   ```

## Implementation by Agent Type

### Orchestrator Agents
```yaml
CONTINUITY PROTOCOL:
1. Check _ai/agent-state/[workflow]/orchestrator-state.md
2. Load list of delegated tasks and their status
3. Resume incomplete delegations
4. Update orchestration progress
```

### Analysis Agents
```yaml
CONTINUITY PROTOCOL:
1. Check _ai/analysis/[task]-progress.md
2. Read completed analysis sections
3. Continue from next section
4. Append new findings incrementally
```

### Code Generation Agents
```yaml
CONTINUITY PROTOCOL:
1. Check if target files exist
2. Look for TODO/CONTINUE markers
3. Read generation progress log
4. Continue from marked points
```

## Parent Context Coordination

The parent Claude conversation should:

1. **Track Agent Invocations**
   ```python
   agents_invoked = {
       "planning-orchestrator": "partial - 60% complete",
       "code-analyzer": "complete",
       "package-evaluator": "interrupted - 2/5 packages"
   }
   ```

2. **Provide Resume Context**
   ```python
   Task("planning-orchestrator", """
   Continue from previous state.
   State file: _ai/agent-state/planning/task-123-state.md
   Priority: Complete package evaluation
   """)
   ```

## Best Practices

1. **Atomic Operations**: Complete small units of work fully before moving on
2. **Verbose Progress**: Write what you're doing as you do it
3. **Clear Markers**: Use standardized progress indicators (✓, ⚠️, ○, ✗)
4. **Resumable State**: Always include enough context to continue
5. **Incremental Output**: Build documents section by section
6. **Error Documentation**: Record failures with recovery instructions

## Progress Indicators

Use consistent markers across all agents:
- `✓` or `[x]` - Completed
- `⚠️` or `[ ]` with percentage - In progress
- `○` or `[ ]` - Pending
- `✗` - Failed/Blocked
- `⏸️` - Paused/Interrupted

## Example: Handling Complex Analysis

```markdown
# _ai/analysis/security-scan-progress.md

## Security Analysis Progress

### 1. Authentication Review ✓
Completed: 2024-01-15 10:30
[Full results...]

### 2. API Endpoint Security (⚠️ 60%)
Started: 2024-01-15 10:45
- ✓ GET endpoints analyzed (15/15)
- ✓ POST endpoints analyzed (8/8)  
- ⚠️ PUT endpoints (3/7 complete)
- ○ DELETE endpoints (0/5)

INTERRUPTED: Timeout at 2024-01-15 11:00
TO RESUME: Continue with PUT endpoint /api/users/:id/profile

### 3. Data Validation ○
Not started

### 4. Dependency Vulnerabilities ○
Not started
```

## Success Criteria

Continuity is successful when:
- Agents can resume work after interruption
- No work is duplicated
- Progress is clearly tracked
- State files are human-readable
- Recovery is straightforward
- Parent context stays informed