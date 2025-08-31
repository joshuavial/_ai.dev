# Protocol Library

This directory contains specialized protocols that can be lazy-loaded when needed instead of being loaded in the initial context. Each protocol provides detailed guidance on specific aspects of the development process.

## Lazy-Loading Approach

Protocols are designed to be loaded only when needed, using the explicit directive:

```
protocol [name]
```

This approach offers several advantages:
1. Reduced initial context size and token usage
2. More focused attention on relevant processes
3. Ability to update protocols independently
4. Clearer separation of concerns

## Available Protocols

### Core Development Protocols

- File: `_ai.bws/protocols/tdd.md` - Test-Driven Development protocol
- File: `_ai.bws/protocols/issue.md` - GitHub issue structure and management
- File: `_ai.bws/protocols/tasks.md` - Work breakdown and task tracking
- File: `_ai.bws/protocols/current.md` - Managing current work state
- File: `_ai.bws/protocols/boot.md` - Boot sequence protocol
- File: `_ai.bws/protocols/playwright.md` - Browser automation handling
- File: `_ai.bws/protocols/port-management.md` - Port assignment and management policy
- File: `_ai.bws/protocols/supabase-test-db.md` - Supabase test database setup and management

### Specialized Protocols

- Directory: `_ai.bws/protocols/kb-sync/` - Knowledge Base synchronization protocols
  - File: `_ai.bws/protocols/kb-sync/kb-github-sync.md` - Bidirectional KB-GitHub sync
  - File: `_ai.bws/protocols/kb-sync/github-extraction.md` - Extracting KB content from GitHub

## Protocol Structure

Each protocol follows a consistent structure:
1. Overview and purpose
2. Detailed requirements and processes
3. Examples and implementation guidance
4. Common pitfalls to avoid
5. Reference implementations where applicable

## Usage Guidelines

1. Reference protocols from core-instructions.md
2. Load protocols only when needed for the current task
3. Update protocol documents as processes evolve
4. Use explicit protocol loading for clarity

## Extending the Protocol Library

When creating new protocols:
1. Focus on a single area of responsibility
2. Use consistent formatting and structure
3. Include clear examples
4. Add to the protocol registry in core-instructions.md
5. Reference from other protocols where appropriate
