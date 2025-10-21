# _ai.dev

Provider-agnostic AI workflow system for project management and agentic coding.

This system has been predominantly tested with **Claude Code** and **Codex**.

## Quick Start

```bash
# Clone into your repository
git clone https://github.com/joshuavial/_ai.dev.git
echo "_ai.dev/" >> .gitignore

# Run the build script
./_ai.dev/build.sh
```

This will:
- Create `_ai/` directory structure for repository-specific overrides
- Generate provider-specific onboarding files (CLAUDE.md, AGENTS.md, GEMINI.md)
- Build merged `.claude/` folders with all resources
- Configure `.gitignore` automatically

## Three-Tier Override System

Resources are resolved with this precedence (highest to lowest):

1. **`_ai/`** - Repository-specific overrides (committed to repo)
2. **`~/_ai/`** - User-global overrides (your personal defaults)
3. **`_ai.dev/`** - System defaults (the base system)

This allows you to customize workflows, protocols, agents, and commands at:
- **User level** - applies to all your repositories
- **Repository level** - applies only to this repository

**After making changes, rebuild:**
```bash
./_ai.dev/build.sh
```

## Directory Structure

```
your-repo/
├── _ai.dev/                    # System defaults (cloned)
│   ├── workflows/              # Default workflows
│   ├── protocols/              # Default protocols
│   ├── agents/                 # Default agents
│   ├── commands/               # Default slash commands
│   ├── skills/                 # Default skills
│   ├── build.sh               # Build script (replaces setup.sh)
│   └── _ai.dev.conf           # Default configuration
│
├── _ai/                        # Repository overrides (auto-created)
│   ├── workflows/              # Custom workflows for this repo
│   ├── protocols/              # Custom protocols
│   ├── agents/                 # Custom agents
│   ├── commands/               # Custom commands
│   ├── skills/                 # Custom skills
│   └── _ai.dev.conf           # Repo-specific config (optional)
│
├── CLAUDE.md                   # Auto-generated (do not edit)
├── AGENTS.md                   # Auto-generated (do not edit)
├── GEMINI.md                   # Auto-generated (do not edit)
│
└── .claude/                    # Auto-generated merged resources
    ├── agents/                 # Merged from all three tiers
    ├── commands/               # Merged from all three tiers
    ├── workflows/              # Merged from all three tiers
    ├── protocols/              # Merged from all three tiers
    └── skills/                 # Merged from all three tiers
```

User-global overrides (optional):
```
~/_ai/                          # User-global defaults
├── workflows/                  # Your global workflow customizations
├── protocols/                  # Your global protocol customizations
├── agents/                     # Your global agent customizations
├── commands/                   # Your global commands
├── skills/                     # Your global skills
└── _ai.dev.conf               # Your global config
```

## Workflows

Structured approaches for different development phases:

- **planning.md** - Technical planning, TDD strategy, package analysis, and user approval requirements
- **execution.md** - Strict TDD implementation (RED-GREEN-REFACTOR), bug investigation, zero tolerance for failing tests
- **management.md** - Process improvement workflow for _ai.dev directory only, evolving workflows/protocols without modifying project code
- **research.md** - Web-enabled investigation with structured capture of findings under _ai/research/
- **qa.md** - Quality verification using 3-question framework (objectives, functionality, quality), mandatory CI verification

## Protocols

System protocols for specialized operations:

- **boot.md** / **setup.md** - Initialize and configure the system
- **agent-continuity.md** - Maintain context across agent sessions
- **sub-agents.md** - Coordinate specialized sub-agents
- **tasks.md** - Task management and tracking
- **tdd.md** - Test-driven development process
- **issue.md** - Issue tracking integration
- **supabase-test-db.md** - Database testing setup
- **playwright.md** - Browser automation testing
- **port-management.md** - Development server port handling
- **dev-helper-scripts.md** - Utility scripts for development

## Commands

Slash commands for quick access to workflows:

- **/plan** - Initiate planning workflow
- **/execute** - Start execution workflow
- **/manage** - Access management tools
- **/research** - Begin research mode
- **/qa** - Run quality assurance
- **/test** - Execute testing protocols
- **/debug** - Start debugging session

## Sub-Agents

### Workflow Orchestrators
- **planning-orchestrator** - Coordinates planning activities
- **execution-orchestrator** - Manages task execution
- **management-orchestrator** - Handles project management
- **research-orchestrator** - Directs research efforts
- **qa-orchestrator** - Oversees quality assurance

### Specialized Agents
- **code-analyzer** - Analyzes code structure and quality
- **code-generator** - Generates new code
- **test-writer** - Creates test suites
- **tdd-enforcer** - Enforces test-driven development
- **bug-investigator** - Investigates and diagnoses bugs
- **refactor-specialist** - Handles code refactoring
- **security-scanner** - Scans for security issues
- **package-evaluator** - Evaluates third-party packages
- **manual-tester** - Guides manual testing procedures

### Protocol Agents
- **boot-agent** - Handles system initialization
- **issue-sync-agent** - Syncs with issue tracking systems

## Configuration

Configuration is controlled via `_ai.dev.conf` files using the same three-tier precedence:

1. `_ai/_ai.dev.conf` (repository-specific)
2. `~/_ai/_ai.dev.conf` (user-global)
3. `_ai.dev/_ai.dev.conf` (system defaults)

### Key Configuration Options

```bash
# Control which provider files to generate
BUILD_CLAUDE=true
BUILD_AGENTS=true    # For OpenAI Codex CLI
BUILD_GEMINI=true

# Control which resource types to include
INCLUDE_WORKFLOWS=true
INCLUDE_PROTOCOLS=true
INCLUDE_AGENTS=true
INCLUDE_COMMANDS=true
INCLUDE_SKILLS=true

# Show override markers in generated files
SHOW_OVERRIDE_MARKERS=true
OVERRIDE_MARKER_REPO="[OVERRIDE: repo]"
OVERRIDE_MARKER_USER="[OVERRIDE: user]"

# Verbosity
VERBOSE=false
SHOW_DIFF=false
```

See `_ai.dev/_ai.dev.conf` for all available options.

## Customization Examples

### Override a Workflow (Repository-Specific)

```bash
# Copy the default to your repo override
cp _ai.dev/workflows/planning.md _ai/workflows/planning.md

# Edit the file
vim _ai/workflows/planning.md

# Rebuild to apply changes
./_ai.dev/build.sh
```

The repository-specific version will now be used instead of the default.

### Create a User-Global Default

Set preferences that apply to all your repositories:

```bash
# Create user-global directory structure
mkdir -p ~/_ai/{workflows,protocols,agents,commands,skills}

# Copy and customize
cp _ai.dev/workflows/planning.md ~/_ai/workflows/planning.md
vim ~/_ai/workflows/planning.md

# Create global config
cat > ~/_ai/_ai.dev.conf << 'EOF'
# My global preferences
VERBOSE=true
SHOW_OVERRIDE_MARKERS=true
BUILD_AGENTS=false    # I only use Claude
BUILD_GEMINI=false
EOF

# Rebuild any project to use your global defaults
./_ai.dev/build.sh
```

### Create a Custom Slash Command

```bash
# Create custom command in repo
cat > _ai/commands/custom.md << 'EOF'
Activate my custom workflow for special tasks.

Usage: /custom
EOF

# Rebuild
./_ai.dev/build.sh

# Use it in Claude Code
/custom
```

### Disable a Resource Type

```bash
# In _ai/_ai.dev.conf
cat > _ai/_ai.dev.conf << 'EOF'
# This repo doesn't use skills
INCLUDE_SKILLS=false
EOF

./_ai.dev/build.sh
```

## Generated Files

The build script auto-generates these files (do not edit manually):

- **CLAUDE.md** - Onboarding file for Claude CLI with complete file listings
- **AGENTS.md** - Onboarding file for Codex CLI
- **GEMINI.md** - Onboarding file for Gemini CLI
- **.claude/** - Merged resource directories for Claude Code

These files are added to `.gitignore` by default (controlled by `COMMIT_GENERATED=false`).

To regenerate after any changes:
```bash
./_ai.dev/build.sh
```

## How the Build System Works

When you run `_ai.dev/build.sh`:

1. **Load Configuration** - Merges config from `_ai.dev/`, `~/_ai/`, and `_ai/`
2. **Discover Resources** - Scans all three locations for workflows, protocols, agents, commands, skills
3. **Apply Precedence** - Repository overrides beat user overrides beat system defaults
4. **Generate Provider Files** - Creates CLAUDE.md, AGENTS.md, GEMINI.md with explicit resource lists
5. **Build Merged Folders** - Copies resources to `.claude/` with overrides applied
6. **Update .gitignore** - Adds generated files to .gitignore

## Workflow Usage

### Claude Code

The build script sets up slash commands automatically:

```bash
/plan       # Start planning phase
/execute    # Start execution phase with TDD
/qa         # Start QA verification
/manage     # Manage and improve workflows
/research   # Start research workflow
/test       # Run manual testing
/debug      # Start bug investigation
```

### OpenAI Codex CLI

Read the auto-generated `AGENTS.md` file for instructions.

### Google Gemini CLI

Read the auto-generated `GEMINI.md` file for instructions.

## Migration from Old setup.sh

If you previously used `setup.sh`:

1. The old symlinks will continue to work
2. Run `./_ai.dev/build.sh` to generate new files
3. Generated files are automatically added to `.gitignore`
4. `build.sh` includes all `setup.sh` functionality and more

You can safely remove `setup.sh` references and use `build.sh` going forward.

## Troubleshooting

### Changes Not Appearing

Always rebuild after modifying files:
```bash
./_ai.dev/build.sh
```

### Override Not Working

Check which file is being used:

1. Enable override markers in config:
   ```bash
   echo "SHOW_OVERRIDE_MARKERS=true" >> _ai/_ai.dev.conf
   ./_ai.dev/build.sh
   ```

2. Check the generated CLAUDE.md for markers like `[OVERRIDE: repo]`

3. Verify file exists in the right location:
   - `_ai/workflows/planning.md` (highest priority)
   - `~/_ai/workflows/planning.md` (medium priority)
   - `_ai.dev/workflows/planning.md` (lowest priority)

### Verbose Output

```bash
VERBOSE=true ./_ai.dev/build.sh
```

Or set permanently:
```bash
echo "VERBOSE=true" >> _ai/_ai.dev.conf
```

### See What Would Be Generated

The build script currently overwrites files. For safety, you can:

1. Commit your changes first
2. Run the build script
3. Use `git diff` to review changes
4. Revert if needed with `git checkout`

## Best Practices

1. **Keep _ai.dev clean** - Don't modify files in `_ai.dev/`, create overrides in `_ai/` or `~/_ai/`
2. **Commit _ai/ to git** - Repository-specific overrides should be versioned
3. **Don't commit generated files** - CLAUDE.md, AGENTS.md, GEMINI.md, .claude/ are auto-generated
4. **Rebuild after changes** - Always run `build.sh` after modifying workflows, protocols, or config
5. **Use user-global for personal preferences** - Put your personal workflow tweaks in `~/_ai/`
6. **Document overrides** - Add comments in your override files explaining why they differ from defaults

## Version

Current version: 1.0.0

## Contributing

To contribute to the core `_ai.dev` system:

1. Fork the repository
2. Make changes in `_ai.dev/`
3. Test thoroughly with `build.sh`
4. Update documentation
5. Submit pull request

Do not modify `_ai/` in contributions - that's for user customization.

## License

[Your License Here]

## Support

For issues or questions:
- Read the protocols in `_ai.dev/protocols/`
- Check examples in `_ai.dev/examples/`
- Review this README
- Open an issue in the repository
