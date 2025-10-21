# Claude Skills: Comprehensive Research Note

## Table of Contents
1. [Overview](#overview)
2. [Core Concepts](#core-concepts)
3. [Technical Architecture](#technical-architecture)
4. [File Structure & SKILL.md Format](#file-structure--skillmd-format)
5. [Progressive Disclosure Mechanism](#progressive-disclosure-mechanism)
6. [Code Execution Capabilities](#code-execution-capabilities)
7. [Platform Availability](#platform-availability)
8. [Official Example Skills](#official-example-skills)
9. [Creating Custom Skills](#creating-custom-skills)
10. [Best Practices](#best-practices)
11. [API Integration](#api-integration)
12. [Security Considerations](#security-considerations)
13. [Performance & Cost Benefits](#performance--cost-benefits)
14. [Comparison with Other Approaches](#comparison-with-other-approaches)
15. [Limitations & Constraints](#limitations--constraints)
16. [Developer Resources](#developer-resources)

## Overview

### What Are Claude Skills?

Claude Skills are **modular, reusable "task packs"** that teach Claude how to complete specific tasks in a repeatable way. Officially introduced by Anthropic in October 2025, Skills represent a new paradigm for customizing Claude's capabilities across all platforms.

**Official Definition (from Anthropic):**
> "Skills are folders of instructions, scripts, and resources that Claude loads dynamically to improve performance on specialized tasks. They teach Claude how to complete specific tasks in a repeatable way, whether that's creating documents with your company's brand guidelines, analyzing data using your organization's specific workflows, or automating personal tasks."

### Key Benefits

1. **Token Efficiency**: Each skill uses only 30-50 tokens until needed, compared to thousands for MCP
2. **Composability**: Skills automatically stack together for complex workflows
3. **Portability**: Build once, use across Claude.ai, Claude Code, and API
4. **Progressive Disclosure**: Only loads what's needed, when it's needed
5. **No Code Required**: Simple skills can be created with just Markdown
6. **Accessibility**: Available to Pro, Max, Team, and Enterprise users

### When to Use Skills

Skills are ideal for:
- Repeating structured workflows (document generation, reports, presentations)
- Enforcing brand guidelines and style consistency
- Organization-specific data analysis workflows
- Automating personal or team tasks
- Creating specialized domain expertise (legal, finance, medical, etc.)
- Building reusable templates and checklists

## Core Concepts

### Skill Definition

A Claude Skill consists of:
- **A folder** containing all skill resources
- **A SKILL.md file** (required) with YAML frontmatter and Markdown instructions
- **Optional resources** like scripts, templates, reference docs, and datasets
- **Metadata** that helps Claude discover when to use the skill

### How Skills Work

1. **At Startup**: Claude loads skill metadata (name + description) into the system prompt (~30-50 tokens per skill)
2. **During Use**: When a relevant task is requested, Claude identifies which skills apply
3. **On-Demand Loading**: Claude reads the SKILL.md file and only loads additional resources if needed
4. **Automatic Coordination**: Multiple skills can work together automatically

### The Skill-Creator Meta-Skill

Anthropic provides a built-in **"skill-creator" skill** that interactively guides users through building new skills:
- Asks questions about your workflow
- Generates the folder structure
- Formats the SKILL.md file
- Bundles required resources
- Makes skill creation accessible to non-technical users

## Technical Architecture

### System Architecture

```
┌─────────────────────────────────────┐
│   Claude Session (Main Context)    │
│   - Skill metadata loaded at start  │
│   - ~30-50 tokens per skill         │
└──────────────┬──────────────────────┘
               │
               │ Task Request
               ▼
┌─────────────────────────────────────┐
│  Skill Discovery & Selection        │
│  - Matches task to skill metadata   │
│  - Identifies relevant skills       │
└──────────────┬──────────────────────┘
               │
               │ Load Skill
               ▼
┌─────────────────────────────────────┐
│  SKILL.md Loading (via Bash tool)   │
│  - Reads core instructions          │
│  - Parses YAML frontmatter          │
└──────────────┬──────────────────────┘
               │
               │ If additional resources needed
               ▼
┌─────────────────────────────────────┐
│  Progressive Resource Loading       │
│  - Scripts, templates, docs         │
│  - Only loaded when referenced      │
└─────────────────────────────────────┘
```

### Storage Locations

**Claude.ai:**
- Skills uploaded via web interface as ZIP files
- Managed through Settings > Capabilities

**Claude Code:**
```
~/.claude/skills/              # User-level (global) skills
.claude/skills/                # Project-level (shared) skills
```

**API:**
- Skills managed via `/v1/skills` endpoint
- Referenced in Messages API via `container` parameter

### Discovery Mechanism

Claude discovers skills through:
1. **User-level skills**: `~/.claude/skills/` (global, available across all projects)
2. **Project-level skills**: `.claude/skills/` (shared with team, version-controlled)
3. **Anthropic skills**: Pre-built skills for common tasks (documents, PDFs, etc.)
4. **API-uploaded skills**: Custom skills managed via the Skills API

## File Structure & SKILL.md Format

### Basic Folder Structure

```
my-skill/
├── SKILL.md              # Required: Entry point with metadata and instructions
├── scripts/              # Optional: Executable code
│   ├── process.py
│   └── validate.sh
├── templates/            # Optional: Reusable templates
│   └── report_template.md
├── reference/            # Optional: Documentation and examples
│   ├── api_docs.md
│   └── examples.md
└── resources/            # Optional: Additional assets
    ├── brand_colors.json
    └── logo.png
```

### SKILL.md Format

The `SKILL.md` file is the only required file. It must start with YAML frontmatter followed by Markdown content.

**Required Structure:**

```markdown
---
name: my-skill-name
description: A clear description of what this skill does and when to use it
---

# Skill Name

## Purpose
Explain what this skill does and when Claude should use it.

## Instructions
1. Step-by-step instructions for Claude
2. How to process inputs
3. How to format outputs

## Examples
[Optional: Example inputs and outputs]

## Reference
[Optional: Additional guidance or edge cases]
```

### YAML Frontmatter Fields

**Required Fields:**

| Field | Type | Description | Constraints |
|-------|------|-------------|-------------|
| `name` | string | Unique identifier for the skill | Lowercase, hyphens only, no leading/trailing hyphens, max 64 chars |
| `description` | string | What the skill does and when to use it | No angle brackets (< >), max 1024 chars |

**Optional Fields:**

| Field | Type | Description |
|-------|------|-------------|
| `license` | string | License for the skill |
| `allowed-tools` | array | Pre-approved tools (Claude Code only) |
| `metadata` | object | Extensible key-value pairs for client-specific data |

**Example with Optional Fields:**

```yaml
---
name: brand-compliance
description: Apply company brand guidelines to documents and presentations, including official colors, fonts, and logo usage
license: MIT
allowed-tools:
  - Read
  - Write
  - Edit
metadata:
  version: "1.2.0"
  author: "Marketing Team"
  category: "branding"
---
```

### Naming Conventions

**Skill Name Rules:**
- Hyphen-case (lowercase with hyphens)
- Letters, digits, and hyphens only
- Cannot start or end with hyphen
- No consecutive hyphens
- Should match directory name

**Valid Examples:**
- `brand-guidelines`
- `financial-report-2025`
- `sql-optimizer`

**Invalid Examples:**
- `Brand_Guidelines` (uppercase, underscore)
- `-brand-guidelines` (starts with hyphen)
- `brand--guidelines` (consecutive hyphens)

## Progressive Disclosure Mechanism

Progressive disclosure is the **core design principle** that makes Skills flexible, scalable, and token-efficient.

### The Three-Stage Loading Process

**1. Startup/Discovery Phase**
- Claude loads skill metadata at startup
- Includes only `name` and `description` in system prompt
- Approximately 30-50 tokens per skill
- Enables installation of many skills without context penalty

**2. Skill Activation Phase**
- When a relevant task is requested, Claude identifies matching skills
- Uses the Bash tool to read the SKILL.md file
- Loads core instructions into context
- Still no penalty for unused resources

**3. On-Demand Resource Loading Phase**
- Only if the task requires deeper support
- Claude fetches additional assets from skill subfolders
- Examples: reference docs, scripts, templates, datasets
- Files don't consume context until accessed

### Progressive Disclosure Benefits

**Token Efficiency:**
> "Each skill only takes up a few dozen extra tokens, with the full details only loaded in should the user request a task that the skill can help solve."

**No Context Penalty for Large Resources:**
- Skills can include comprehensive API documentation
- Large datasets can be bundled
- Extensive examples and reference materials
- No cost until actually used

**Comparison to Alternative Approaches:**
- **MCP**: Front-loads thousands of tokens for every capability
- **Long System Prompts**: Always consume context regardless of relevance
- **Skills**: Scale to hundreds of skills with minimal overhead

### Structuring for Progressive Disclosure

**Best Practices:**

1. **Keep SKILL.md focused**: Core instructions only
2. **Split large content**: Use separate reference files
3. **Organize by usage pattern**: Frequently used info in SKILL.md, rare info in reference docs
4. **Use clear file names**: Claude can intelligently choose which files to read

**Example Structure for Large Skills:**

```
data-analysis-skill/
├── SKILL.md                    # Core workflow (always loaded)
├── reference/
│   ├── pandas_api.md           # Only if pandas needed
│   ├── matplotlib_examples.md  # Only if visualization needed
│   └── statistics_formulas.md  # Only if statistical analysis needed
└── templates/
    ├── basic_report.md         # Only if report generation needed
    └── advanced_charts.py      # Only if advanced viz needed
```

## Code Execution Capabilities

### Overview

Skills can include executable code (Python, Bash, JavaScript, etc.) that runs in Claude's secure sandboxed environment via the **Code Execution Tool**.

### Code Execution Environment

**Available Runtimes:**
- Python 3.x (with common data science libraries pre-installed)
- Bash/shell scripting
- JavaScript/Node.js
- Other languages available in the container

**Pre-installed Python Packages:**
- numpy, pandas, matplotlib, seaborn
- scikit-learn, scipy
- requests, beautifulsoup4
- openpyxl (Excel), python-pptx (PowerPoint), python-docx (Word)
- And many more standard packages

### How Code Execution Works with Skills

1. **Skill includes executable scripts** in its folder
2. **Claude reads SKILL.md** and identifies when code execution is needed
3. **Claude invokes Code Execution Tool** with the skill's script
4. **Script runs in sandboxed container** with isolated filesystem
5. **Results returned** to Claude for processing

### Example: Python Script in a Skill

**Folder Structure:**
```
excel-analysis/
├── SKILL.md
└── scripts/
    └── analyze.py
```

**SKILL.md:**
```markdown
---
name: excel-analysis
description: Analyze Excel spreadsheets and generate insights
---

# Excel Analysis Skill

## Instructions
When given an Excel file:
1. Load the data using the analyze.py script
2. Generate statistical summary
3. Create visualizations if requested
4. Format results in a clear report

## Script Usage
Use `scripts/analyze.py <filename>` to process Excel files.
```

**scripts/analyze.py:**
```python
import pandas as pd
import sys

def analyze_excel(filename):
    df = pd.read_excel(filename)
    summary = df.describe()
    return summary

if __name__ == "__main__":
    result = analyze_excel(sys.argv[1])
    print(result)
```

### Code Execution Constraints

**Security Restrictions:**
- No network access (cannot make external API calls)
- No runtime package installation (only pre-installed packages)
- Isolated filesystem (cannot access system files)
- Fresh container per request (no state persistence)

**Resource Limits:**
- Execution timeout limits apply
- Memory and CPU constraints
- No persistent storage between sessions

### Code Execution Tool API

**Enabling Code Execution:**
- Requires `code_execution_20250825` tool type
- Requires `anthropic-beta: code-execution-2025-08-25` header
- Container can be reused across API requests via container ID

**Example API Usage:**
```python
import anthropic

client = anthropic.Anthropic(api_key="your-api-key")

response = client.messages.create(
    model="claude-sonnet-4.5-20250929",
    max_tokens=1024,
    tools=[{"type": "code_execution_20250825"}],
    messages=[{
        "role": "user",
        "content": "Use the excel-analysis skill to analyze sales_data.xlsx"
    }],
    betas=["code-execution-2025-08-25"]
)
```

## Platform Availability

### Availability by Platform

| Platform | Status | Requirements | Notes |
|----------|--------|--------------|-------|
| **Claude.ai** | Available | Pro, Max, Team, or Enterprise plan | Upload skills as ZIP files |
| **Claude Code** | Available | Pro, Max, Team, or Enterprise plan | Skills in `~/.claude/skills/` or `.claude/skills/` |
| **API** | Available | API access with Code Execution Tool | Manage via `/v1/skills` endpoint |

### Availability by Plan

| Plan | Skills Access | Anthropic Skills | Custom Skills | API Access |
|------|---------------|------------------|---------------|------------|
| **Free** | No | No | No | No |
| **Pro** | Yes | Yes | Yes | No (unless API credits purchased) |
| **Max** | Yes | Yes | Yes | Via API credits |
| **Team** | Yes | Yes | Yes | Via API credits |
| **Enterprise** | Yes | Yes | Yes | Full API access |

### Platform-Specific Features

**Claude.ai:**
- Upload skills via Settings > Capabilities
- Toggle skills on/off individually
- Maximum 8MB upload size per skill (all files combined)
- Skills managed through web interface

**Claude Code:**
- Install via plugins: `/plugin install document-skills@anthropic-agent-skills`
- Manual installation to `~/.claude/skills/` (user-level) or `.claude/skills/` (project-level)
- Project skills are version-controlled and shared with team
- Automatic discovery and loading

**API:**
- Programmatic control via `/v1/skills` endpoint
- Up to 8 skills per Messages API request
- Skill versioning and management via Console
- Same skill format across all platforms

### Enabling Skills

**Claude.ai:**
1. Navigate to Settings
2. Go to Capabilities section
3. Enable "Code execution and file creation"
4. Toggle on desired skills
5. Upload custom skills as needed

**Claude Code:**
1. Enable Code Execution in settings
2. Install skills via plugins or manually
3. Skills automatically discovered at startup
4. No manual activation required

**API:**
1. Enable Code Execution Tool in API requests
2. Upload skills via `/v1/skills` endpoint
3. Reference skills in Messages API via `container` parameter
4. Manage versions through Claude Console

## Official Example Skills

Anthropic provides a comprehensive collection of example skills in the official GitHub repository at [github.com/anthropics/skills](https://github.com/anthropics/skills).

### Document Skills (Source-Available)

These skills power Claude's official document capabilities:

**1. Excel (xlsx)**
- Create and edit spreadsheets
- Support for formulas and formatting
- Data analysis and visualization
- Import/export capabilities

**2. PowerPoint (pptx)**
- Create and edit presentations
- Support for layouts and templates
- Charts and visualizations
- Automated slide generation

**3. Word (docx)**
- Create and edit documents
- Tracked changes and comments
- Formatting preservation
- Text extraction and analysis

**4. PDF**
- Extract text and tables
- Create new PDFs
- Merge and split documents
- Handle fillable forms

### Creative Skills

**Algorithmic Art**
- Generate creative coding art
- SVG and canvas-based outputs
- Parametric design patterns

**Canvas Design**
- Visual design creation
- Layout and composition
- Design system enforcement

**Slack GIF Creator**
- Generate GIFs for team communication
- Custom animations
- Branded content

### Technical Skills

**Webapp Testing**
- Automated web application testing
- Test case generation
- Bug reporting workflows

**MCP Server Generator**
- Generate Model Context Protocol servers
- Standardized tool integration
- Custom MCP configurations

**Artifacts Builder**
- Create Claude artifacts
- Interactive components
- Reusable UI elements

### Business & Communication Skills

**Internal Communications**
- Write status reports, newsletters, FAQs
- Consistent company voice
- Template-based generation

**Brand Guidelines**
- Apply brand compliance to documents
- Logo files, color codes, fonts
- Layout templates for consistency

**Weekly Team Updates**
- Structure updates with wins, blockers, priorities
- Consistent format for meetings
- Automated report generation

### Template Skill

A basic template for creating new skills:
- Demonstrates proper structure
- Example YAML frontmatter
- Sample instructions and workflows

## Creating Custom Skills

### Method 1: Using the Skill-Creator (Recommended)

The easiest way to create skills is using Anthropic's built-in "skill-creator" skill:

1. **Invoke the skill-creator**: Ask Claude to help you create a new skill
2. **Answer questions**: Claude asks about your workflow and requirements
3. **Review generated structure**: Claude creates the folder structure and SKILL.md
4. **Add resources**: Bundle any additional files (scripts, templates, docs)
5. **Test the skill**: Verify it works as expected
6. **Deploy**: Upload to Claude.ai or save to skills directory

**Example Conversation:**
```
User: "Help me create a skill for generating weekly sales reports"

Claude: "I'll help you create that skill. Let me ask a few questions:
1. What data sources will the reports use?
2. What sections should the report include?
3. Do you have a preferred format (PDF, Excel, Word)?
4. Are there any specific calculations or metrics?
5. Should it follow any branding guidelines?"

[After answers, Claude generates the complete skill structure]
```

### Method 2: Manual Creation

**Step 1: Create Directory Structure**
```bash
mkdir -p my-skill/scripts
mkdir -p my-skill/templates
mkdir -p my-skill/reference
```

**Step 2: Create SKILL.md**
```markdown
---
name: my-skill
description: Clear description of what the skill does and when to use it
---

# My Skill

## Purpose
[Explain the skill's purpose]

## Instructions
[Step-by-step instructions for Claude]

## Examples
[Optional: Example usage]
```

**Step 3: Add Resources**
- Scripts in `scripts/`
- Templates in `templates/`
- Documentation in `reference/`

**Step 4: Test Locally**
- Place in `~/.claude/skills/` (Claude Code)
- Or upload to Claude.ai
- Test with relevant tasks

**Step 5: Deploy**
- For teams: Add to `.claude/skills/` and commit to git
- For API: Upload via `/v1/skills` endpoint
- For personal use: Keep in `~/.claude/skills/`

### Example: Brand Guidelines Skill

**Folder Structure:**
```
brand-guidelines/
├── SKILL.md
├── resources/
│   ├── logo.png
│   ├── colors.json
│   └── fonts.json
└── templates/
    ├── presentation.pptx
    └── document.docx
```

**SKILL.md:**
```markdown
---
name: brand-guidelines
description: Apply company brand guidelines to all documents and presentations, including official colors, fonts, and logo usage
metadata:
  version: "1.0.0"
  department: "Marketing"
---

# Brand Guidelines Skill

## Purpose
Ensure all company materials follow official brand guidelines.

## Brand Assets
- Logo: resources/logo.png
- Colors: resources/colors.json
- Fonts: resources/fonts.json

## Instructions

### Colors
Use only official brand colors:
- Primary: #0066CC (Blue)
- Secondary: #FF6600 (Orange)
- Accent: #00CC66 (Green)
- Text: #333333 (Dark Gray)

### Typography
- Headings: Montserrat Bold
- Body: Open Sans Regular
- Code: Fira Code

### Logo Usage
- Minimum size: 100px width
- Clear space: Equal to logo height
- Never modify colors or proportions

## Document Templates
- Presentations: templates/presentation.pptx
- Documents: templates/document.docx

## Application Process
1. Start with appropriate template
2. Apply color scheme
3. Use correct typography
4. Add logo with proper spacing
5. Review for compliance
```

**resources/colors.json:**
```json
{
  "primary": "#0066CC",
  "secondary": "#FF6600",
  "accent": "#00CC66",
  "text": "#333333",
  "background": "#FFFFFF"
}
```

### Example: Data Analysis Skill

**Folder Structure:**
```
data-analysis/
├── SKILL.md
├── scripts/
│   ├── analyze.py
│   ├── visualize.py
│   └── export.py
└── reference/
    ├── statistical_methods.md
    └── visualization_guide.md
```

**SKILL.md:**
```markdown
---
name: data-analysis
description: Comprehensive data analysis workflow with statistical testing, visualization, and reporting
allowed-tools:
  - Read
  - Write
  - code_execution
---

# Data Analysis Skill

## Purpose
Perform comprehensive data analysis on CSV, Excel, or JSON datasets.

## Workflow

### 1. Data Loading
Use `scripts/analyze.py` to load and validate data.

### 2. Statistical Analysis
- Descriptive statistics
- Correlation analysis
- Hypothesis testing (refer to reference/statistical_methods.md)

### 3. Visualization
Use `scripts/visualize.py` for:
- Distribution plots
- Correlation heatmaps
- Time series charts
- Custom visualizations (see reference/visualization_guide.md)

### 4. Reporting
Generate comprehensive report with:
- Executive summary
- Key findings
- Statistical results
- Visualizations
- Recommendations

## Output Format
Export results using `scripts/export.py`:
- PDF report for stakeholders
- Excel workbook with detailed data
- PNG charts for presentations
```

## Best Practices

### 1. Clear and Specific Descriptions

The `description` field is critical for skill discovery.

**Bad:**
```yaml
description: Helps with documents
```

**Good:**
```yaml
description: Apply company brand guidelines to documents and presentations, including official colors (Blue #0066CC, Orange #FF6600), Montserrat fonts, and logo placement with proper spacing
```

**Why:** Claude uses the description to determine when to activate the skill. Be specific about:
- What the skill does
- When it should be used
- What inputs it expects
- What outputs it produces

### 2. Single Responsibility Principle

Each skill should have one clear, focused purpose.

**Bad:** "general-helper" skill that does everything

**Good:** Separate skills for:
- `financial-reporting` - Generate quarterly financial reports
- `brand-compliance` - Apply brand guidelines
- `data-visualization` - Create charts and graphs
- `sql-optimizer` - Optimize database queries

### 3. Structure for Progressive Disclosure

Organize content by frequency of use:

**SKILL.md (always loaded):**
- Core workflow
- Common instructions
- Quick reference

**Reference files (loaded on-demand):**
- Detailed API documentation
- Edge cases and troubleshooting
- Comprehensive examples
- Large datasets

### 4. Use Version Control

**For Team Skills:**
```bash
# Add project skills to git
git add .claude/skills/
git commit -m "Add financial reporting skill v1.2"
git push
```

**Benefits:**
- Track skill evolution
- Review changes
- Rollback if needed
- Share with team consistently

### 5. Test Thoroughly

**Testing Checklist:**
- [ ] Test with typical use cases
- [ ] Test with edge cases
- [ ] Test with invalid inputs
- [ ] Test with multiple skills active simultaneously
- [ ] Test resource loading (scripts, templates)
- [ ] Verify token usage is reasonable
- [ ] Confirm output quality

### 6. Document Everything

**Include in SKILL.md or separate README:**
- Purpose and capabilities
- Required setup or dependencies
- Expected inputs and outputs
- Limitations and known issues
- Usage examples
- Troubleshooting guide

### 7. Minimize Tool Access (Claude Code)

Only grant necessary tools via `allowed-tools`:

**For Analysis Skills:**
```yaml
allowed-tools:
  - Read
  - code_execution
```

**For Generation Skills:**
```yaml
allowed-tools:
  - Read
  - Write
  - code_execution
```

**For Review Skills:**
```yaml
allowed-tools:
  - Read
```

### 8. Start with Evaluation

Before building a skill:
1. **Identify specific gaps**: Run Claude on representative tasks and observe where it struggles
2. **Quantify the need**: How often does this task occur?
3. **Define success criteria**: What does good output look like?
4. **Build incrementally**: Start simple, add complexity as needed

### 9. Keep Skills Maintainable

**Good Practices:**
- Use clear, consistent naming
- Comment complex logic
- Include version numbers in metadata
- Document breaking changes
- Provide migration guides when updating

### 10. Optimize for Composability

Design skills to work well with other skills:

**Example:** A quarterly investor deck might use:
- `brand-guidelines` skill for styling
- `financial-reporting` skill for data
- `presentation-formatting` skill for layouts

All three coordinate automatically without manual intervention.

## API Integration

### Skills Management API

The `/v1/skills` endpoint provides programmatic control over custom skill versioning and management.

### API Capabilities

**CRUD Operations:**
- List Anthropic-provided skills
- Upload custom skills
- Update existing skills
- Delete custom skills
- Manage skill versions

### Using Skills with Messages API

Skills are specified using the `container` parameter in the Messages API.

**Basic Structure:**
```python
import anthropic

client = anthropic.Anthropic(api_key="your-api-key")

response = client.messages.create(
    model="claude-sonnet-4.5-20250929",
    max_tokens=4096,
    tools=[{"type": "code_execution_20250825"}],
    container={
        "skills": [
            {
                "type": "anthropic",
                "skill_id": "pptx"
            },
            {
                "type": "custom",
                "skill_id": "brand-guidelines",
                "version": "1.2.0"  # Optional: pin to specific version
            }
        ]
    },
    messages=[{
        "role": "user",
        "content": "Create a Q3 presentation following brand guidelines"
    }],
    betas=["code-execution-2025-08-25"]
)
```

### Skill Parameters

**Required:**
- `type`: Either "anthropic" (for pre-built skills) or "custom"
- `skill_id`: Identifier for the skill

**Optional:**
- `version`: Pin to a specific version (recommended for production)

### Maximum Skills Per Request

**Limit:** Up to 8 skills per Messages API request

**Example with Multiple Skills:**
```python
container={
    "skills": [
        {"type": "anthropic", "skill_id": "xlsx"},
        {"type": "anthropic", "skill_id": "pptx"},
        {"type": "custom", "skill_id": "financial-analysis", "version": "2.1.0"},
        {"type": "custom", "skill_id": "brand-guidelines", "version": "1.5.0"}
    ]
}
```

### Uploading Custom Skills

**Via API:**
```python
# Upload a skill
skill_response = client.skills.create(
    name="financial-analysis",
    description="Comprehensive financial analysis and reporting",
    skill_bundle=open("financial-analysis.zip", "rb")
)

skill_id = skill_response.skill_id
version = skill_response.version
```

**Via Claude Console:**
- Navigate to Skills management
- Upload ZIP file (max 8MB)
- Set version and metadata
- Deploy to production or staging

### Versioning Best Practices

**Semantic Versioning:**
- `1.0.0` - Initial release
- `1.1.0` - New features, backward compatible
- `1.1.1` - Bug fixes
- `2.0.0` - Breaking changes

**Pinning Versions:**
```python
# Development: Use latest
{"type": "custom", "skill_id": "my-skill"}

# Production: Pin specific version
{"type": "custom", "skill_id": "my-skill", "version": "1.2.0"}
```

### Container Reuse

Reuse containers across requests for efficiency:

```python
# First request
response1 = client.messages.create(
    model="claude-sonnet-4.5-20250929",
    max_tokens=1024,
    tools=[{"type": "code_execution_20250825"}],
    messages=[{"role": "user", "content": "Analyze data.csv"}],
    betas=["code-execution-2025-08-25"]
)

container_id = response1.container_id

# Subsequent requests with same container
response2 = client.messages.create(
    model="claude-sonnet-4.5-20250929",
    max_tokens=1024,
    tools=[{"type": "code_execution_20250825"}],
    container_id=container_id,  # Reuse container
    messages=[{"role": "user", "content": "Create visualization"}],
    betas=["code-execution-2025-08-25"]
)
```

### Pricing

**Skills are included in subscription plans** (Pro, Max, Team, Enterprise) at no additional cost.

**API Pricing:**
- Skills follow standard API pricing
- Pay only for tokens consumed during skill execution
- Code Execution Tool: $0.05 per session-hour
- No additional cost for the skills themselves

## Security Considerations

### Sandboxed Execution Environment

All Skills in Claude and the API operate in Claude's secure sandboxed environment.

**Key Security Features:**
- Isolated filesystem (cannot access system files)
- No network access (cannot make external API calls)
- No data persistence between sessions
- OS-level isolation primitives
- Automatic credential protection

### Sandboxing Architecture

**Filesystem Isolation:**
- Skills can only access files within their container
- No access to user credentials (git, SSH keys, signing keys)
- Temporary filesystem cleared after session

**Network Isolation:**
- No outbound network connections
- Cannot call external APIs
- Cannot download packages at runtime

**Permission Controls:**
- Skills specify required tools via `allowed-tools`
- Automatic allowlisting for safe operations
- Blocking of malicious operations
- User permission prompts when needed

### Security Risks

**1. Prompt Injection**
> "Anthropic warns that prompt injection could trick Claude into running untrusted code, since its sandbox inherently supports arbitrary script execution for file generation."

**Mitigation:**
- Only install skills from trusted sources
- Thoroughly audit skills before use
- Review skill code and scripts
- Monitor for unexpected behavior

**2. Configuration Vulnerabilities**
- Misconfigured permissions can degrade isolation
- Broad allowlists increase attack surface
- Vulnerable connectors (e.g., MCPs) can bypass restrictions

**Mitigation:**
- Use minimal `allowed-tools` for each skill
- Regularly audit skill permissions
- Keep skills updated with security patches

**3. Trust Requirements**
> "Allowing AI to execute code requires users to carefully vet which skills they trust."

**Mitigation:**
- Establish skill approval process
- Maintain skill registry with trusted sources
- Review skill changes before updating
- Implement behavioral monitoring

### Security Best Practices

**For Organizations:**
1. **Establish approval workflow** for skill installation
2. **Maintain allowlist** of approved skills
3. **Regular security audits** of installed skills
4. **Monitor skill usage** for anomalies
5. **Version control** all custom skills
6. **Code review** for custom skill scripts
7. **Incident response plan** for skill-related issues

**For Developers:**
1. **Minimize tool access** in `allowed-tools`
2. **Validate all inputs** in skill scripts
3. **Avoid sensitive data** in skill files
4. **Use environment variables** for secrets (not hardcoded)
5. **Test in isolated environment** before deployment
6. **Document security assumptions** in SKILL.md
7. **Follow secure coding practices** in scripts

**For Individual Users:**
1. **Only install from trusted sources** (Anthropic, verified developers)
2. **Review skill contents** before installation
3. **Check for updates** regularly
4. **Remove unused skills** to minimize attack surface
5. **Report suspicious behavior** to Anthropic

### Secure Credential Management

**What's Protected:**
- Git credentials
- SSH keys
- Signing keys
- API tokens
- Environment secrets

**How It Works:**
- Credentials never enter the sandbox
- Code execution isolated from credential storage
- Separate security contexts for different operations

### Compliance Considerations

For regulated industries:
- Skills run in isolated containers (meets isolation requirements)
- No data persistence (meets data retention policies)
- Audit logging available (meets compliance logging)
- Version control and change tracking (meets change management)

## Performance & Cost Benefits

### Token Efficiency

**Comparison to Alternatives:**

| Approach | Initial Context Cost | Loading Behavior |
|----------|---------------------|------------------|
| **Skills** | 30-50 tokens per skill | Progressive, on-demand |
| **MCP** | Thousands of tokens per server | Front-loaded, always in context |
| **Long System Prompts** | Entire prompt size | Always in context |

**Real-World Example:**
- GitHub's MCP: Tens of thousands of tokens
- 5-6 MCPs: Context window nearly full
- 100+ Skills: ~3,000-5,000 tokens total (until activated)

### Cost Reduction

**Included in Subscription Plans:**
- Skills are free for Pro, Max, Team, and Enterprise users
- No additional subscription cost
- Unlimited custom skills
- All Anthropic skills included

**API Cost Structure:**
- Skills follow standard API pricing
- Pay only for tokens consumed during execution
- No separate skill licensing fees
- Code Execution Tool: $0.05 per session-hour

**Reduced Token Costs:**
- Only load what's needed, when it's needed
- Avoid repetitive long prompts
- Share skills across team (no duplication)
- Reuse skills across projects

### Performance Benefits

**1. Faster Response Times**
- Minimal initial context loading
- Parallel skill discovery
- On-demand resource fetching

**2. Consistency**
> "How Anthropic's 'Skills' make Claude faster, cheaper, and more consistent for business workflows"

**Benefits:**
- Repeatable workflows eliminate variability
- Standardized outputs across team
- Reduced need for prompt iteration

**3. Composability**
- Multiple skills work together automatically
- No manual coordination needed
- Complex workflows from simple building blocks

**Example Workflow:**
```
Task: "Create Q3 investor presentation"

Automatically invokes:
1. financial-reporting skill (data analysis)
2. brand-guidelines skill (styling)
3. pptx skill (presentation generation)

All coordinated without manual intervention.
```

**4. Productivity Gains**
- Less time crafting perfect prompts
- Faster onboarding (skills encode team knowledge)
- Automated repetitive tasks
- Focus on high-value work

### Optimization Strategies

**1. Structure Skills Efficiently**
- Core instructions in SKILL.md (always loaded)
- Reference materials in separate files (loaded on-demand)
- Mutually exclusive contexts in different files

**2. Reuse Across Projects**
- User-level skills in `~/.claude/skills/`
- Shared team skills in `.claude/skills/`
- Organization skills via API

**3. Version Control**
- Track skill evolution
- Share improvements across team
- Roll back if performance degrades

**4. Monitor Usage**
- Track which skills are used most
- Identify optimization opportunities
- Remove unused skills

## Comparison with Other Approaches

### Claude Skills vs. MCP (Model Context Protocol)

| Aspect | Claude Skills | MCP |
|--------|---------------|-----|
| **Complexity** | Simple: Markdown + YAML | Complex: JSON-RPC, servers, protocols |
| **Setup** | Drop files in folder or upload ZIP | Configure servers, manage connections |
| **Token Usage** | 30-50 tokens per skill (until used) | Thousands of tokens per server (always) |
| **Context Impact** | Minimal until activated | Front-loaded, always consuming context |
| **Cross-Platform** | Claude.ai, Claude Code, API | Requires MCP-compatible client |
| **Learning Curve** | Low (Markdown knowledge) | High (protocol specification, JSON-RPC) |
| **Maintenance** | Simple file updates | Server maintenance, protocol upgrades |

**When to Use Each:**

**Use Skills for:**
- Repeating structured workflows
- Template-based tasks
- Internal process automation
- Domain-specific expertise
- Minimal setup requirements
- Token-efficient operation

**Use MCP for:**
- Cross-platform tool integration
- Standardized external service connections
- Complex tool ecosystems
- Multi-client scenarios
- Real-time data access needs

**Skills and MCP Together:**
> "A Claude Skill can instruct Claude to call tools exposed via MCP or a platform's native tool-calling. The Skill is the task abstraction; MCP (or a platform's tools) is the integration mechanism."

**Example:**
```markdown
---
name: github-workflow
description: Automated GitHub workflow management using MCP tools
---

# GitHub Workflow Skill

## Instructions
1. Use GitHub MCP to access repository data
2. Analyze pull requests and issues
3. Generate status reports
4. Create automated responses

[Skills provide the workflow, MCP provides the GitHub integration]
```

### Claude Skills vs. Custom GPTs (OpenAI)

| Aspect | Claude Skills | Custom GPTs |
|--------|---------------|-------------|
| **Format** | Filesystem-based (folders) | Configuration-based (web UI) |
| **Portability** | Universal (works everywhere) | Platform-locked (OpenAI only) |
| **Code Execution** | Sandboxed Python/Bash | Limited Actions/Plugins |
| **Composability** | Automatic multi-skill coordination | Single GPT per conversation |
| **Token Efficiency** | Progressive disclosure | Full instructions always loaded |
| **Sharing** | Git, ZIP, API | OpenAI GPT Store |

### Claude Skills vs. Long System Prompts

| Aspect | Claude Skills | Long System Prompts |
|--------|---------------|---------------------|
| **Reusability** | Infinitely reusable across sessions | Copy-paste each time |
| **Composability** | Multiple skills combine automatically | Manual combination needed |
| **Token Cost** | Only when activated | Always in context |
| **Maintenance** | Update once, use everywhere | Update each instance |
| **Sharing** | Version-controlled, team-shared | Manual distribution |
| **Scalability** | Hundreds of skills possible | Limited by context window |

### Claude Skills vs. Agent Sub-Agents

**Note:** Claude Skills and Sub-Agents are complementary, not competing approaches.

**Similarities:**
- Both extend Claude's capabilities
- Both support tool restrictions
- Both enable specialization
- Both use progressive disclosure concepts

**Differences:**

| Aspect | Skills | Sub-Agents |
|--------|--------|------------|
| **Scope** | Task instructions + resources | Complete agent configuration |
| **Execution** | Within main conversation | Separate context/conversation |
| **Context** | Shared with main Claude | Isolated 200k token context |
| **Invocation** | Automatic based on task | Explicit via Task tool |
| **State** | Stateless (files only) | Independent conversation state |

**Using Together:**
```markdown
# Sub-agent that uses Skills
---
name: financial-analyst-agent
description: Financial analysis and reporting
tools: Read, Write, code_execution
---

You are a financial analyst. Use these skills:
- financial-reporting: For quarterly reports
- data-visualization: For charts
- brand-guidelines: For consistent formatting

[Sub-agent provides the agent context, Skills provide the workflows]
```

## Limitations & Constraints

### Size and Upload Limits

**Maximum Skill Size:**
- 8MB total (all files combined)
- Applies to ZIP uploads (Claude.ai, API)

**YAML Frontmatter Limits:**
- Name: 64 characters maximum
- Description: 1024 characters maximum

### Skills Per Request

**API Limit:** Maximum 8 skills per Messages API request

**Workaround:** Design skills to be composable and focused on specific tasks rather than creating many small skills.

### Code Execution Constraints

**Network Restrictions:**
- No outbound network access
- Cannot make external API calls
- Cannot download packages at runtime

**Runtime Environment:**
- Only pre-installed packages available
- No persistent storage between sessions
- Fresh container per request (or reused via container ID)
- Execution timeout limits apply

**Resource Limits:**
- Memory constraints
- CPU limitations
- Disk space limits (temporary)

### Filesystem Constraints

**Isolation:**
- Skills can only access files within their container
- No system file access
- No access to user credentials

**Persistence:**
- No data persists between sessions (unless container is reused)
- Temporary filesystem cleared after session

### Platform-Specific Limitations

**Claude.ai:**
- Skills uploaded as ZIP files only
- Web-based management (no local filesystem)
- Requires manual upload for updates

**Claude Code:**
- Requires local filesystem access
- Must be in specific directories (`~/.claude/skills/` or `.claude/skills/`)

**API:**
- Requires Code Execution Tool enabled
- Programmatic management only
- Version management via Console

### Discovery Limitations

**Name Requirements:**
- Must be valid hyphen-case
- Cannot contain special characters
- Must match directory name

**Description Constraints:**
- No angle brackets (< >)
- Limited to 1024 characters
- Must clearly indicate when skill should be used

### Current Limitations (as of October 2025)

1. **No runtime package installation** - Only pre-installed packages available
2. **No network access in sandbox** - Cannot call external APIs during execution
3. **No cross-skill communication** - Skills cannot directly call each other
4. **No persistent state** - Each invocation starts fresh (unless container reused)
5. **Limited to 8 skills per API request** - May need to carefully select most relevant skills
6. **Manual skill updates** - No automatic skill update mechanism (yet)

### Workarounds for Limitations

**For Network Access:**
- Pre-download required data and include in skill bundle
- Use MCP servers for external integrations
- Provide data via user uploads

**For Package Dependencies:**
- Use only pre-installed packages
- Include necessary code directly in skill
- Request Anthropic to add packages to environment

**For Persistent State:**
- Use file system within session
- Reuse containers via container_id
- Store state externally and pass back in

**For Multi-Skill Coordination:**
- Design skills to work compositionally
- Include cross-references in instructions
- Let Claude coordinate automatically

## Developer Resources

### Official Documentation

**Primary Resources:**
- Main Skills Page: [anthropic.com/news/skills](https://www.anthropic.com/news/skills)
- Engineering Blog: [anthropic.com/engineering/equipping-agents-for-the-real-world-with-agent-skills](https://www.anthropic.com/engineering/equipping-agents-for-the-real-world-with-agent-skills)
- API Documentation: [docs.anthropic.com/en/api/skills-guide](https://docs.anthropic.com/en/api/skills-guide)
- GitHub Repository: [github.com/anthropics/skills](https://github.com/anthropics/skills)

**Help Center:**
- What are Skills: [support.claude.com/en/articles/12512176-what-are-skills](https://support.claude.com/en/articles/12512176-what-are-skills)
- Using Skills: [support.claude.com/en/articles/12512180-using-skills-in-claude](https://support.claude.com/en/articles/12512180-using-skills-in-claude)
- Creating Custom Skills: [support.claude.com/en/articles/12512198-how-to-create-custom-skills](https://support.claude.com/en/articles/12512198-how-to-create-custom-skills)
- Teaching Claude Your Workflows: [support.claude.com/en/articles/12580051-teach-claude-your-way-of-working-using-skills](https://support.claude.com/en/articles/12580051-teach-claude-your-way-of-working-using-skills)

**Technical Specifications:**
- Agent Skills Spec: [github.com/anthropics/skills/blob/main/agent_skills_spec.md](https://github.com/anthropics/skills/blob/main/agent_skills_spec.md)
- Code Execution Tool: [docs.anthropic.com/en/docs/agents-and-tools/tool-use/code-execution-tool](https://docs.anthropic.com/en/docs/agents-and-tools/tool-use/code-execution-tool)

### Example Skills Repository

**Official Examples:**
[github.com/anthropics/skills](https://github.com/anthropics/skills)

**Categories:**
- Document Skills: Excel, PowerPoint, Word, PDF
- Creative Skills: Algorithmic art, canvas design, GIF creator
- Technical Skills: Webapp testing, MCP server generator
- Business Skills: Internal comms, brand guidelines
- Template: Basic skill structure

### Community Resources

**Awesome Claude Skills:**
[github.com/travisvn/awesome-claude-skills](https://github.com/travisvn/awesome-claude-skills)
- Curated list of skills
- Tools and resources
- Best practices
- Community contributions

### API References

**Skills Management API:**
- `/v1/skills` endpoint documentation
- CRUD operations for skills
- Version management
- Programmatic control

**Messages API Integration:**
- Container parameter specification
- Skill selection and activation
- Multiple skills coordination

### Getting Started Guides

**For Claude.ai Users:**
1. Navigate to Settings > Capabilities
2. Enable "Code execution and file creation"
3. Toggle on Anthropic skills
4. Upload custom skills as ZIP files

**For Claude Code Users:**
1. Install via plugins: `/plugin install document-skills@anthropic-agent-skills`
2. Or manually add to `~/.claude/skills/`
3. Skills automatically discovered at startup

**For API Developers:**
1. Enable Code Execution Tool
2. Upload skills via `/v1/skills` endpoint
3. Reference in Messages API via `container` parameter
4. Manage versions through Console

### Learning Path

**Beginner:**
1. Read "What are Skills" guide
2. Try Anthropic-provided skills (Excel, PowerPoint, etc.)
3. Use skill-creator to build first custom skill
4. Test with simple workflows

**Intermediate:**
1. Create skills with multiple resources
2. Include executable scripts
3. Optimize for progressive disclosure
4. Combine multiple skills in workflows

**Advanced:**
1. API integration and automation
2. Version management and deployment
3. Multi-skill orchestration
4. Custom skill frameworks

### Support Channels

**Official Support:**
- Claude Help Center
- Anthropic support team
- API documentation

**Community:**
- GitHub discussions on skills repository
- Developer forums
- Social media (Twitter, LinkedIn)

### Updates and Announcements

**Follow for Latest:**
- Anthropic News: [anthropic.com/news](https://www.anthropic.com/news)
- Engineering Blog: [anthropic.com/engineering](https://www.anthropic.com/engineering)
- GitHub Repository: Watch for updates
- API Changelog: Track API changes

---

## Sources

1. **Main Skills Announcement**: https://www.anthropic.com/news/skills
2. **Engineering Blog - Equipping Agents with Skills**: https://www.anthropic.com/engineering/equipping-agents-for-the-real-world-with-agent-skills
3. **GitHub Repository**: https://github.com/anthropics/skills
4. **Agent Skills Specification**: https://github.com/anthropics/skills/blob/main/agent_skills_spec.md
5. **Claude Help Center - What are Skills**: https://support.claude.com/en/articles/12512176-what-are-skills
6. **Claude Help Center - Using Skills**: https://support.claude.com/en/articles/12512180-using-skills-in-claude
7. **Claude Help Center - Creating Custom Skills**: https://support.claude.com/en/articles/12512198-how-to-create-custom-skills
8. **API Skills Guide**: https://docs.claude.com/en/api/skills-guide
9. **Code Execution Tool Documentation**: https://docs.anthropic.com/en/docs/agents-and-tools/tool-use/code-execution-tool
10. **Simon Willison - Claude Skills vs MCP Analysis**: https://simonwillison.net/2025/Oct/16/claude-skills/
11. **VentureBeat - Skills for Business Workflows**: https://venturebeat.com/ai/how-anthropics-skills-make-claude-faster-cheaper-and-more-consistent-for
12. **Claude Code Sandboxing**: https://www.anthropic.com/engineering/claude-code-sandboxing
13. **Awesome Claude Skills (Community)**: https://github.com/travisvn/awesome-claude-skills

*Last Updated: October 21, 2025*
*Research conducted based on official Anthropic documentation and community resources*
*Note: Features and capabilities may evolve as Claude Skills are actively being developed*
