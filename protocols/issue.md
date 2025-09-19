# GitHub Issue Protocol

This protocol defines the standardized format and processes for GitHub issues. It covers issue creation, updating, comment structure, and synchronization with KB documentation.

## Issue Structure Overview

GitHub issues follow a minimalist structure with three key components:

1. **Issue Title**: Clear, descriptive name with issue number
2. **Issue Description**: One-line summary with KB documentation links and metadata
3. **Current State Comment**: Status indicator, KB state link, and latest progress update

This minimalist structure keeps GitHub issues clean while storing detailed documentation in the Knowledge Base.

## Issue Creation Requirements

When creating a new GitHub issue:

### 1. Issue Title Format

```
#123 Implement User Onboarding Wizard
```

- Include the issue number at the beginning
- Use clear, descriptive text for the issue title
- Focus on the primary goal or feature
- Keep to 5-10 words when possible

### 2. Issue Description Format

```markdown
Create an interactive onboarding wizard to guide new users through profile setup.

📁 **Tasks**: 
- _ai/tasks/123-wizard-navigation/ 
- _ai/tasks/123-profile-setup/
🔄 **Dependencies**: #120, #121
👥 **Stakeholders**: @product-manager, @design-lead
🔍 **Priority**: High
```

The issue description MUST include:
- One-line summary of the issue purpose
- Task folders with format: `📁 **Tasks**: _ai/tasks/{issue-id}-{task-name}/`
- Dependencies with format: `🔄 **Dependencies**: #{numbers}` (or "None" if none exist)
- Stakeholders with format: `👥 **Stakeholders**: @mentions`
- Priority with format: `🔍 **Priority**: High/Medium/Low`

### 3. Current State Comment Format

```markdown
## Current State

**Status**: In Progress
**Active Tasks**: 
- _ai/tasks/123-wizard-navigation/ (in-progress)
- _ai/tasks/123-step-content/ (planning)
**Updated**: 2023-08-15 14:30
**Blockers**: Waiting for design assets from @design-lead
```

The Current State comment MUST include:
- Status indicator (Planning/In Progress/Review/Complete)
- List of active task folders with status
- Last update timestamp
- Current blockers (if any, otherwise "None")

## Issue Update Process

When updating GitHub issues:

1. **Update KB Documents First**
   - Make changes to relevant KB files
   - Update current state documentation
   - Document progress clearly

2. **Generate Summary from KB**
   - Extract key updates from KB documentation
   - Create concise progress summary

3. **Update GitHub Issue**
   - Keep issue description for permanent information
   - Update issue title only if scope changes significantly

4. **Update Current State Comment**
   - Modify the original Current State comment
   - Update status, timestamp, and blockers
   - NEVER create new Current State comments

## Sub-ticket Requirements

For issues that are sub-tickets of larger issues:

1. **Reference Parent Issue**
   - Include clear reference to parent ticket in title
   - Format: `#123.1 Implement Navigation Component for Onboarding Wizard`

2. **Issue Description Format**
   - Include link to parent issue: `🔗 **Parent Issue**: #123`
   - Follow same minimal structure as parent issues

3. **KB Documentation**
   - Link to sub-task specific KB document
   - Ensure KB documentation links back to parent issue

## Content Guidelines

### What NOT to Include in GitHub Issues

Store in task folders, not GitHub issues:
1. Detailed planning, technical plans, and task breakdowns
2. TDD evidence (images and artifacts)
3. Status history and detailed progress tracking
4. Implementation details and code snippets
5. Extended discussions and decision records

### What SHOULD Be in GitHub Issues

1. Clear, concise issue statement
2. Links to task folders where details are stored
3. Essential metadata (dependencies, stakeholders, priority)
4. Current status and blockers
5. Brief progress updates in Current State comment

## Issue Examples

### Feature Issue Example

```markdown
Title: #124 Implement Password Reset Flow

Description:
Create secure password reset functionality with email verification.

📁 **Tasks**: _ai/tasks/124-password-reset/
🔄 **Dependencies**: #118
👥 **Stakeholders**: @security-team, @product-manager
🔍 **Priority**: High
```

### Bug Fix Issue Example

```markdown
Title: #125 Fix Login Error on Safari

Description:
Address authentication failure affecting Safari browsers.

📁 **Tasks**: _ai/tasks/125-safari-login-fix/
🔄 **Dependencies**: None
👥 **Stakeholders**: @front-end-lead
🔍 **Priority**: Critical
```

### Sub-ticket Example

```markdown
Title: #123.2 Create Step Content Components for Onboarding

Description:
Implement individual step content components for the onboarding wizard.

📁 **Tasks**: _ai/tasks/123-step-content/
🔗 **Parent Issue**: #123
🔄 **Dependencies**: #123.1
👥 **Stakeholders**: @ux-designer
🔍 **Priority**: High
```

## GitHub-KB Synchronization

For complete GitHub-KB synchronization processes:

1. **GitHub → KB**: See [KB Sync Extract Protocol](_ai.dev/protocols/kb-sync/github-extraction.md)
2. **KB → GitHub**: See [KB Sync Update Protocol](_ai.dev/protocols/kb-sync/kb-github-sync.md)

## Accessing GitHub Issues

When accessing GitHub issues, use GitHub CLI:

```bash
# View basic issue details
gh issue view <issue-number> --repo <owner>/<repo-name>

# View issue with comments
gh issue view <issue-number> --comments --repo <owner>/<repo-name>

# Get structured JSON output
gh issue view <issue-number> --json title,body,comments --repo <owner>/<repo-name>
```

Never attempt to find GitHub issues by searching local directories. GitHub issues exist in the GitHub repository and must be accessed using GitHub CLI commands or the API.

## When to Update Issues

Update GitHub issues at these key points:

1. **When task status changes**
   - Moving from Planning to In Progress
   - Moving from In Progress to Review
   - Moving from Review to Complete

2. **When significant progress occurs**
   - Completing major components or milestones
   - Reaching important implementation checkpoints
   - At least once per day when actively working

3. **When blockers arise or are resolved**
   - New dependencies or blockers discovered
   - Existing blockers resolved
   - Need for additional stakeholder input

4. **When scope changes**
   - Adding or removing planned features
   - Significant changes to implementation approach
   - Adding or removing dependencies

By following this GitHub Issue Protocol, you will maintain a clean, consistent approach to issue tracking while storing detailed documentation in the Knowledge Base.