# Sub-Agents Protocol for _ai.dev Workflows

This protocol defines how to use the Claude Code sub-agent system with _ai.dev workflows. All agent configurations are stored in `_ai.dev/agents/` for portability across projects.

## Overview

Sub-agents are specialized AI assistants that extend Claude Code's capabilities through the Task tool. Each agent:
- Has its own 200k token context window
- Operates independently from the main conversation
- Can be invoked in parallel (up to 10 simultaneously)
- Returns structured results to the parent context

## Setup for New Projects

### Option 1: Symlink (Recommended)
```bash
# Link agents from _ai.dev to project
ln -s /path/to/_ai.dev/agents .claude/agents
ln -s /path/to/_ai.dev/commands .claude/commands
```

### Option 2: Copy
```bash
# Copy agents to project
cp -r /path/to/_ai.dev/agents .claude/agents
cp -r /path/to/_ai.dev/commands .claude/commands
```

### Option 3: Global Installation
```bash
# Install globally for all projects
cp -r /path/to/_ai.dev/agents ~/.claude/agents
```

## Agent Categories

### Workflow Orchestrators
Located in `_ai.dev/agents/workflow/`:
- `planning-orchestrator` - Coordinates planning phase
- `execution-orchestrator` - Manages TDD implementation
- `qa-orchestrator` - Runs quality verification
- `management-orchestrator` - Handles process improvement

### Specialized Task Agents
Located in `_ai.dev/agents/specialized/`:
- `code-analyzer` - Deep codebase analysis
- `code-generator` - Pattern-aware code generation
- `tdd-enforcer` - Strict TDD compliance
- `test-writer` - Comprehensive test suites
- `package-evaluator` - Package scoring and comparison
- `manual-tester` - MCP Playwright UI testing
- `security-scanner` - Vulnerability detection
- `refactor-specialist` - Code improvement

### Protocol Enforcement Agents
Located in `_ai.dev/agents/protocol/`:
- `boot-agent` - Context initialization
- `issue-sync-agent` - GitHub-KB synchronization

## Agent Invocation

### Using Slash Commands

```
/plan
# Activates planning-orchestrator

/execute
# Activates execution-orchestrator

/qa
# Activates qa-orchestrator

/manage
# Activates management-orchestrator

/test
# Runs manual-tester agent
```

### Direct Task Invocation

```python
# In Claude Code conversation
Task(
    subagent_type="planning-orchestrator",
    description="Plan authentication feature",
    prompt="Analyze requirements for OAuth integration and create technical plan"
)
```

### Parallel Execution

```python
# Multiple agents simultaneously
results = parallel([
    Task("code-analyzer", "Analyze frontend components"),
    Task("code-analyzer", "Analyze backend services"),
    Task("package-evaluator", "Evaluate auth libraries")
])
```

## Workflow Integration Examples

### Planning Phase
```
User: "Plan the payment integration feature"
Claude: "I'll use the planning-orchestrator to analyze and plan this feature."

[Invokes planning-orchestrator]
→ Spawns code-analyzer agents for different areas
→ Runs package-evaluator for payment processors
→ Executes security-scanner for requirements
→ Aggregates results into technical plan
```

### Execution Phase
```
User: "Implement the login component"
Claude: "I'll use the execution-orchestrator for TDD implementation."

[Invokes execution-orchestrator]
→ tdd-enforcer writes failing test
→ code-generator implements solution
→ test-writer adds comprehensive tests
→ manual-tester validates UI
→ refactor-specialist improves code
```

### QA Phase
```
User: "Run QA on the completed feature"
Claude: "I'll use the qa-orchestrator for comprehensive verification."

[Invokes qa-orchestrator]
→ Checks CI status (zero tolerance)
→ Runs parallel verification agents
→ manual-tester executes UI tests
→ security-scanner checks vulnerabilities
→ Generates QA report
```

## Agent Communication

### Context Passing
Agents receive context through their prompt:
```python
Task("code-generator", """
Context: Working on issue #123 - User Authentication
Technical Plan: _ai/tasks/123-auth/technical-plan.md
Patterns: Follow existing auth patterns in src/auth/

Generate login component following project conventions.
""")
```

### Result Aggregation
Parent context receives structured results:
```python
result = Task("package-evaluator", "Compare: stripe, paddle, lemonsqueezy")
# Returns comparison matrix with scores and recommendation
```

## Performance Optimization

### Parallel Execution Strategy
- Identify independent tasks
- Launch up to 10 agents simultaneously
- Aggregate results efficiently
- Queue additional agents if needed

### Context Management
- Each agent gets fresh 200k tokens
- No context pollution between agents
- Results passed back structured
- Parent maintains overall state

### Resource Usage
```python
# Efficient parallel pattern
if independent_tasks > 1:
    use_parallel_agents()
else:
    use_sequential_execution()

# Prioritize critical path
critical_agents_first()
background_agents_queued()
```

## Error Handling

### Agent Failures
```python
try:
    result = Task("agent-name", prompt)
except AgentError as e:
    # Fallback to manual process
    handle_manually()
    log_failure(e)
```

### Timeout Handling
- Default timeout: 5 minutes per agent
- Can be extended for complex tasks
- Automatic retry once on timeout

### Recovery Strategy
1. Log agent failure
2. Attempt retry once
3. Fall back to manual process
4. Document issue for improvement

## Best Practices

### 1. Clear Task Definition
```python
# ✅ Good: Specific and focused
Task("code-analyzer", "Analyze React components in src/components for patterns")

# ❌ Bad: Vague and broad
Task("code-analyzer", "Look at the code")
```

### 2. Appropriate Agent Selection
```python
# ✅ Use specialized agent for specific task
Task("security-scanner", "Check for SQL injection vulnerabilities")

# ❌ Don't use general agent for specialized work
Task("code-analyzer", "Find security issues")
```

### 3. Context Provision
```python
# ✅ Provide necessary context
Task("code-generator", """
File: src/components/UserCard.tsx
Pattern: Follow existing card components
Dependencies: Use only packages in package.json
""")

# ❌ Insufficient context
Task("code-generator", "Make a user card")
```

### 4. Result Validation
Always validate agent results:
- Check completeness
- Verify accuracy
- Test generated code
- Review recommendations

## Monitoring and Metrics

### Track Agent Performance
- Execution time per agent
- Success/failure rates
- Token usage
- Quality of results

### Continuous Improvement
- Refine agent prompts based on results
- Update agents with learnings
- Document common issues
- Share improvements across projects

## Troubleshooting

### Agent Not Found
```
Error: Unknown subagent_type: "agent-name"
```
Solution: Verify agent file exists and name matches in frontmatter

### Context Overflow
```
Error: Agent response exceeded context limit
```
Solution: Break task into smaller parts or increase max_tokens

### Parallel Execution Issues
```
Error: Maximum parallel agents exceeded
```
Solution: Queue additional agents or reduce parallelism

## Future Enhancements

### Planned Improvements
1. Agent result caching
2. Cross-agent communication
3. Learning from feedback
4. Custom tool creation
5. Agent marketplace

### Version Management
- Agents versioned with _ai.dev
- Backward compatibility maintained
- Migration guides for updates
- Changelog for agent changes

## Success Criteria

Sub-agent integration is successful when:
- Workflows execute faster through parallelization
- Quality standards are maintained or improved
- Repetitive tasks are automated
- Cognitive load is reduced
- Best practices are consistently applied
- Knowledge is preserved in agent configurations