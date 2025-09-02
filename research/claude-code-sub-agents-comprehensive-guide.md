# Claude Code Sub-Agents: Comprehensive Guide

## Table of Contents
1. [Overview](#overview)
2. [Core Concepts](#core-concepts)
3. [Architecture & Technical Details](#architecture--technical-details)
4. [Configuration & Setup](#configuration--setup)
5. [Creating Sub-Agents](#creating-sub-agents)
6. [Tool Management](#tool-management)
7. [Execution Models](#execution-models)
8. [Best Practices](#best-practices)
9. [Common Use Cases](#common-use-cases)
10. [Advanced Patterns](#advanced-patterns)
11. [Performance Optimization](#performance-optimization)
12. [Troubleshooting](#troubleshooting)
13. [Future Developments](#future-developments)

## Overview

Claude Code sub-agents are specialized AI assistants that extend Claude Code's capabilities through task-specific configurations. Introduced as a major feature in 2025, they represent a paradigm shift in AI-assisted development by enabling sophisticated multi-agent workflows.

### Key Benefits

1. **Context Isolation**: Each sub-agent operates in its own 200,000 token context window, preventing conversation pollution
2. **Parallel Processing**: Up to 10 sub-agents can execute simultaneously
3. **Task Specialization**: Purpose-built agents for specific domains
4. **Resource Efficiency**: Lightweight instances that don't consume main context
5. **Reusability**: Share agents across projects and teams
6. **Controlled Access**: Granular tool permissions per agent

### When to Use Sub-Agents

Sub-agents are ideal for:
- Complex multi-step tasks requiring different expertise
- Large codebases that would exceed single context limits
- Parallel analysis or processing tasks
- Recurring specialized operations (code review, testing, security)
- Isolating experimental or risky operations

## Core Concepts

### Sub-Agent Definition

A sub-agent is a configured instance of Claude that:
- Has a unique identity and purpose
- Operates with a specific system prompt
- Has controlled access to tools
- Maintains its own conversation context
- Can be invoked automatically or explicitly

### Task Tool Integration

The Task tool is the primary mechanism for invoking sub-agents:

```typescript
// Internal structure (conceptual)
interface TaskInvocation {
  subagent_type: string;      // The agent to invoke
  description: string;         // Short task description
  prompt: string;             // Detailed instructions
}
```

### Context Management

Each sub-agent maintains:
- **Independent memory**: No access to parent conversation
- **Clean slate**: Starts fresh for each invocation
- **Result passing**: Returns structured output to parent
- **No persistence**: Context cleared after completion

## Architecture & Technical Details

### System Architecture

```
┌─────────────────┐
│  Main Claude    │
│     Context     │
└────────┬────────┘
         │
    Task Tool
         │
    ┌────┴────┐
    │         │
┌───▼───┐ ┌──▼────┐
│Agent 1│ │Agent 2│  ... (up to 10 parallel)
└───────┘ └───────┘
```

### File Structure

```
project/
├── .claude/
│   ├── agents/           # Project-specific agents
│   │   ├── code-reviewer.md
│   │   ├── test-writer.md
│   │   └── security-auditor.md
│   └── commands/         # Slash commands
│       └── review.md
└── ~/.claude/
    └── agents/           # User-level global agents
        └── personal-assistant.md
```

### Agent Discovery

Claude Code discovers agents through:
1. **Project agents**: `.claude/agents/` in current project
2. **User agents**: `~/.claude/agents/` in home directory
3. **Built-in agents**: System-provided specialized agents
4. **Dynamic registration**: Agents created via `/agents` command

## Configuration & Setup

### Basic Agent Structure

```markdown
---
name: agent-name
description: When this agent should be invoked
tools: Read, Write, Bash  # Optional - inherits all if omitted
---

# System Prompt

You are a specialized agent for [specific purpose]. Your role is to...

## Core Responsibilities
1. Primary task description
2. Secondary responsibilities
3. Quality standards

## Approach
- Step-by-step methodology
- Decision criteria
- Output format requirements

## Constraints
- What NOT to do
- Limitations to observe
- Safety considerations
```

### YAML Frontmatter Options

```yaml
---
name: security-auditor              # Required: unique identifier
description: Security vulnerability analysis  # Required: invocation trigger
tools:                              # Optional: tool whitelist
  - Read
  - Grep
  - WebSearch
auto_invoke: true                   # Optional: automatic activation
priority: high                      # Optional: execution priority
max_tokens: 50000                   # Optional: response limit
temperature: 0.3                    # Optional: creativity level
tags:                              # Optional: categorization
  - security
  - code-quality
  - automation
---
```

### Tool Specifications

Available tools for sub-agents:
- **Read**: File reading and analysis
- **Write**: File creation and modification
- **Edit**: Precise file editing
- **MultiEdit**: Batch file modifications
- **Bash**: Command execution
- **Grep**: Pattern searching
- **Glob**: File pattern matching
- **WebSearch**: Internet searches
- **WebFetch**: URL content retrieval
- **TodoWrite**: Task management
- **Task**: Recursive sub-agent invocation

## Creating Sub-Agents

### Using the /agents Command

```
/agents
```

This launches an interactive wizard:
1. Choose creation method (manual/assisted)
2. Define agent purpose and capabilities
3. Select appropriate tools
4. Generate or write system prompt
5. Test the agent configuration

### Manual Creation

1. **Create agent file**:
```bash
mkdir -p .claude/agents
touch .claude/agents/my-agent.md
```

2. **Define configuration**:
```markdown
---
name: my-specialized-agent
description: Handles specific task X with expertise Y
tools: Read, Grep, Edit
---

You are an expert in...
```

### Agent Template Examples

#### Code Reviewer Agent

```markdown
---
name: code-reviewer
description: Performs comprehensive code review
tools: Read, Grep, Glob
---

You are an expert code reviewer specializing in finding bugs, security issues, and suggesting improvements.

## Review Process
1. Analyze code structure and architecture
2. Check for common anti-patterns
3. Identify security vulnerabilities
4. Suggest performance optimizations
5. Verify coding standards compliance

## Output Format
Provide feedback in this structure:
- **Critical Issues**: Must fix before merge
- **Major Concerns**: Should address soon
- **Minor Suggestions**: Nice-to-have improvements
- **Positive Feedback**: What's done well

Always be constructive and provide examples.
```

#### Test Writer Agent

```markdown
---
name: test-writer
description: Creates comprehensive test suites
tools: Read, Write, MultiEdit, Bash
---

You are a test automation specialist who writes comprehensive, maintainable test suites.

## Approach
1. Analyze code to understand functionality
2. Identify edge cases and error conditions
3. Write unit tests with high coverage
4. Include integration tests where appropriate
5. Add performance tests for critical paths

## Standards
- Follow AAA pattern (Arrange, Act, Assert)
- Use descriptive test names
- Include both positive and negative cases
- Mock external dependencies
- Aim for >80% code coverage
```

## Tool Management

### Tool Restriction Strategies

1. **Minimal Access** (Most Secure):
```yaml
tools: Read  # Only reading capability
```

2. **Read-Analyze** (Safe Analysis):
```yaml
tools: Read, Grep, Glob
```

3. **Full Development** (Maximum Capability):
```yaml
tools: Read, Write, Edit, MultiEdit, Bash, Task
```

### Tool Combination Patterns

#### Research Pattern
```yaml
tools: Read, Grep, WebSearch, WebFetch
```
Use for: Documentation research, API exploration, best practices discovery

#### Refactoring Pattern
```yaml
tools: Read, Edit, MultiEdit, Grep
```
Use for: Code cleanup, naming changes, structure improvements

#### Automation Pattern
```yaml
tools: Bash, Read, Write, TodoWrite
```
Use for: Build automation, deployment, task orchestration

## Execution Models

### Automatic Invocation

Agents can be triggered automatically based on context:

```markdown
---
name: security-scanner
description: Automatically reviews code for security issues
auto_invoke: true
trigger_patterns:
  - "auth"
  - "password"
  - "token"
  - "api_key"
---
```

### Explicit Invocation

Direct invocation through Task tool:

```python
# In main Claude conversation
"I'll use the Task tool to invoke the security-scanner agent"

Task(
    subagent_type="security-scanner",
    description="Security audit",
    prompt="Review the authentication module for vulnerabilities"
)
```

### Parallel Execution

Multiple agents running simultaneously:

```python
# Conceptual example
tasks = [
    Task("code-reviewer", "Review PR #123"),
    Task("test-writer", "Write tests for new feature"),
    Task("doc-generator", "Update API documentation")
]
# All three execute in parallel
```

### Sequential Chaining

Agents passing results to each other:

```python
# First agent analyzes
result1 = Task("analyzer", "Analyze codebase structure")

# Second agent uses analysis
result2 = Task("optimizer", f"Optimize based on: {result1}")

# Third agent validates
result3 = Task("validator", f"Validate optimizations: {result2}")
```

## Best Practices

### 1. Single Responsibility Principle

Each agent should have ONE clear purpose:

❌ **Bad**: "general-helper" that does everything
✅ **Good**: "sql-optimizer" that only optimizes SQL queries

### 2. Detailed System Prompts

Include:
- Clear role definition
- Step-by-step methodology
- Success criteria
- Example outputs
- Edge case handling

### 3. Tool Minimization

Only grant necessary tools:

```yaml
# For a documentation agent
tools: Read, Grep  # Don't need Write, Edit, Bash
```

### 4. Clear Descriptions

The description determines when agents are invoked:

❌ **Vague**: "Helps with code"
✅ **Specific**: "Performs security vulnerability scanning on Python code"

### 5. Version Control

Always commit project agents:

```bash
git add .claude/agents/
git commit -m "Add specialized code review agent"
```

### 6. Testing Agents

Test before deployment:
1. Create test scenarios
2. Invoke with edge cases
3. Verify output quality
4. Check tool usage patterns
5. Monitor token consumption

### 7. Documentation

Document each agent's:
- Purpose and capabilities
- Required context/setup
- Expected inputs/outputs
- Limitations
- Usage examples

## Common Use Cases

### 1. Code Quality Assurance

```markdown
---
name: quality-guardian
description: Comprehensive code quality checks
tools: Read, Grep, Glob
---

Analyze code for:
- Design patterns violations
- SOLID principles adherence  
- Cyclomatic complexity
- Code duplication
- Dead code detection
```

### 2. Database Operations

```markdown
---
name: db-specialist
description: Database optimization and migration
tools: Read, Write, Bash
---

Expert in:
- Query optimization
- Index recommendations
- Migration script generation
- Performance profiling
- Schema design
```

### 3. API Development

```markdown
---
name: api-builder
description: RESTful API development
tools: Read, Write, Edit, MultiEdit
---

Specializes in:
- OpenAPI specification
- Endpoint design
- Authentication/authorization
- Rate limiting
- API versioning
```

### 4. DevOps Automation

```markdown
---
name: devops-engineer
description: CI/CD and infrastructure automation
tools: Read, Write, Bash, Edit
---

Handles:
- Docker configuration
- Kubernetes manifests
- GitHub Actions workflows
- Terraform scripts
- Monitoring setup
```

### 5. Data Science

```markdown
---
name: data-scientist
description: Data analysis and ML tasks
tools: Read, Write, Bash
---

Capabilities:
- Exploratory data analysis
- Feature engineering
- Model training scripts
- Visualization code
- Statistical testing
```

## Advanced Patterns

### Multi-Agent Orchestration

```markdown
---
name: orchestrator
description: Coordinates multiple specialized agents
tools: Task
---

You coordinate complex operations by:
1. Breaking down tasks into specialties
2. Invoking appropriate sub-agents
3. Combining results
4. Ensuring consistency

Available agents:
- frontend-dev: UI/UX tasks
- backend-dev: Server logic
- db-admin: Database operations
- tester: Test creation
```

### Recursive Agent Networks

Agents can invoke other agents:

```markdown
---
name: feature-builder
description: Complete feature implementation
tools: Task, TodoWrite
---

For each feature:
1. Use 'architect' agent for design
2. Use 'implementer' agent for coding
3. Use 'tester' agent for test creation
4. Use 'documenter' agent for docs
```

### Context Bridge Pattern

Passing context between isolated agents:

```python
# Main context
summary = Task("analyzer", "Analyze and summarize codebase")

# Pass summary to next agent
improvement = Task("optimizer", f"""
Based on this analysis: {summary}
Suggest optimizations
""")
```

### Validation Chain

Multiple agents validating each other's work:

```
Code Writer → Reviewer → Tester → Security Auditor → Deployer
```

Each agent validates previous work before proceeding.

## Performance Optimization

### Token Management

1. **Limit response size**:
```yaml
max_tokens: 10000  # Prevent excessive output
```

2. **Focused prompts**: Be specific to avoid exploration
3. **Tool restrictions**: Fewer tools = faster execution

### Parallel vs Sequential

**Use parallel when**:
- Tasks are independent
- Time is critical
- Different expertise needed simultaneously

**Use sequential when**:
- Tasks depend on each other
- Order matters
- Resource constraints exist

### Caching Strategies

Though agents don't persist state, you can:
1. Store results in files
2. Use consistent naming for retrieval
3. Check for existing work before re-processing

### Resource Monitoring

Track:
- Token consumption per agent
- Execution time
- Success/failure rates
- Tool usage patterns

## Troubleshooting

### Common Issues

#### 1. Agent Not Found
```
Error: Unknown subagent_type: "my-agent"
```
**Solution**: Check file name matches `name` field in frontmatter

#### 2. Tool Access Denied
```
Error: Agent doesn't have access to tool 'Bash'
```
**Solution**: Add tool to agent's `tools` list

#### 3. Context Overflow
```
Error: Agent response exceeded context limit
```
**Solution**: Add `max_tokens` limit or break into smaller tasks

#### 4. Circular Dependencies
```
Error: Maximum recursion depth exceeded
```
**Solution**: Prevent agents from invoking themselves recursively

### Debugging Techniques

1. **Verbose mode**: Add debugging instructions to prompts
2. **Incremental testing**: Test with simple tasks first
3. **Tool logging**: Monitor which tools are being used
4. **Output validation**: Check agent outputs match expectations

### Performance Issues

**Slow execution**:
- Reduce tool access
- Simplify system prompts
- Break complex tasks into smaller parts
- Use parallel execution where possible

**Poor quality results**:
- Improve system prompt clarity
- Add examples to prompt
- Restrict tool access to prevent tangents
- Provide more specific task descriptions

## Future Developments

### Expected Enhancements (2025-2026)

1. **Dynamic Agent Creation**: Agents that spawn specialized sub-agents on demand
2. **Persistent Context**: Optional state preservation between invocations
3. **Cross-Agent Communication**: Direct message passing between agents
4. **Learning Agents**: Agents that improve based on feedback
5. **Visual Agents**: Integration with image generation/analysis
6. **Custom Tools**: User-defined tools for agents
7. **Agent Marketplaces**: Sharing and discovering community agents
8. **Performance Profiling**: Built-in analytics for agent optimization

### Emerging Patterns

1. **Self-Organizing Teams**: Agents that form dynamic collaborations
2. **Adaptive Specialization**: Agents that adjust expertise based on project
3. **Continuous Integration**: Agents as part of CI/CD pipelines
4. **Real-time Collaboration**: Agents working alongside human developers
5. **Cross-Platform Agents**: Agents that work across different IDEs/platforms

### Integration Roadmap

- **IDE Plugins**: Native support in VS Code, JetBrains, etc.
- **Cloud Deployment**: Agents as cloud services
- **API Access**: RESTful APIs for agent invocation
- **Workflow Automation**: Integration with GitHub Actions, Jenkins, etc.
- **Enterprise Features**: RBAC, audit logs, compliance tools

## Appendix: Quick Reference

### Agent Creation Checklist

- [ ] Clear, unique name
- [ ] Specific description for triggering
- [ ] Minimal necessary tools
- [ ] Detailed system prompt
- [ ] Test with sample tasks
- [ ] Document usage examples
- [ ] Version control the agent file
- [ ] Share with team if applicable

### Tool Quick Reference

| Tool | Purpose | Risk Level | Common Uses |
|------|---------|------------|-------------|
| Read | File reading | Low | Analysis, review |
| Write | File creation | Medium | Generation, reports |
| Edit | File modification | Medium | Refactoring, fixes |
| MultiEdit | Batch edits | Medium | Large refactors |
| Bash | Command execution | High | Automation, testing |
| Grep | Pattern search | Low | Code analysis |
| Glob | File finding | Low | Discovery, inventory |
| WebSearch | Internet search | Low | Research, docs |
| WebFetch | URL retrieval | Low | API docs, examples |
| TodoWrite | Task management | Low | Planning, tracking |
| Task | Sub-agent invocation | Medium | Orchestration |

### Performance Guidelines

| Agents Count | Execution Model | Expected Performance |
|--------------|-----------------|---------------------|
| 1-3 | Sequential | Fast, predictable |
| 4-6 | Mixed | Moderate, efficient |
| 7-10 | Parallel | Fast but resource-intensive |
| 10+ | Queued | Slower, batched processing |

### Security Recommendations

1. **Never grant Bash access** to untrusted agents
2. **Limit Write/Edit access** for analysis agents
3. **Use Read-only tools** for review agents
4. **Validate agent sources** before installation
5. **Regular audit** of agent permissions
6. **Monitor tool usage** for anomalies
7. **Implement approval workflows** for critical operations

---

*Last updated: August 2025*
*Based on Claude Code latest features and community best practices*