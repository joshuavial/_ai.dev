# GitHub Issue Structure

This document outlines the standardized structure for GitHub issues, which is referenced by all workflows that interact with GitHub issues, including planning, execution, and QA workflows.

## Standard Issue Format

Every GitHub issue should follow a standardized format:

1. **Issue Description (Main Body):**
   - Title: Clear, concise description of the feature/bug/task
   - Summary/Overview: Brief explanation of what needs to be done
   - References to related issues/PRs (if applicable)
   - Labels, assignees, and other metadata

2. **First Comment - Product/Problem Statement:**
   - Link to relevant PRD document (if applicable)
   - Problem statement: What problem are we solving?
   - Business value: Why is this important?
   - Acceptance criteria: Clear, measurable outcomes
   - User stories or use cases (if applicable)
   - Screenshots/mockups (if applicable)

3. **Second Comment - Technical Plan:**
   - Technical approach with clear steps
   - Required changes to existing components
   - New components to be created
   - Data model changes (if any)
   - API changes (if any)
   - Package/library analysis (when applicable)
   - Potential risks or challenges
   - Testing strategy
   - Implementation checklist

4. **Third Comment - Current State:**
   - Implementation progress tracking
   - Checklist of completed and pending tasks from the Technical Plan
   - Current blockers or challenges
   - Recent discoveries or learnings
   - Next immediate steps
   - Updated timeline (if applicable)

## Comment Update Process

When changes are needed to the issue:

- **For product/scope changes:**
  - Update the first comment (Product/Problem Statement)
  - Add a new comment indicating what changed and why
  - Tag relevant stakeholders in the new comment

- **For technical approach changes:**
  - Update the second comment (Technical Plan)
  - Add a new comment explaining the technical changes
  - Reference any new discoveries that led to the changes

- **For implementation progress updates:**
  - Update the third comment (Current State)
  - Check off completed tasks
  - Add newly discovered tasks if needed
  - Update blockers and next steps

**IMPORTANT:** Never create new comments for information that should be in the first three comments. Always update the original comments to ensure the most current information is in the standard location. Any additional comments (4th and beyond) should be for discussions, questions, or other communication not fitting into the structured format of the first three comments.

## Accessing GitHub Issues

### GitHub CLI Commands for Issue Access

When referencing GitHub issues (for example, when starting work on issue #2):

1. **Use GitHub CLI to access issues:**
   - For basic issue details: `gh issue view <issue-number> --repo <owner>/<repo-name>`
   - To view issue with comments: `gh issue view <issue-number> --comments --repo <owner>/<repo-name>`
   - For JSON output to parse specific fields: `gh issue view <issue-number> --json title,body,comments --repo <owner>/<repo-name>`

2. **Access specific issue comments:**
   - Remember that comments in GitHub issues are indexed from the first comment (0)
   - Comment #0: Main issue description (for planning, this holds key requirements)
   - Comment #1: First comment - usually contains Product/Problem Statement
   - Comment #2: Second comment - contains Technical Plan
   - Comment #3: Third comment - contains Current State tracking

> **IMPORTANT:** Do not attempt to locate GitHub issues by searching local directories. GitHub issues exist in the GitHub repository and must be accessed using GitHub CLI commands like `gh issue view` or the GitHub API.

### Example: Retrieving an Issue for Implementation

```bash
# Get basic issue information
gh issue view 42 --repo owner/repo-name

# Get issue with all comments to see Technical Plan (comment #2)
gh issue view 42 --comments --repo owner/repo-name

# Extract specific information in JSON format
gh issue view 42 --json number,title,body,comments --repo owner/repo-name
```

### Updating Issues

To update issue comments (such as the Current State in comment #3):

```bash
# For example, to update the third comment (index 2)
gh api --method PATCH repos/owner/repo-name/issues/comments/COMMENT_ID -f body="Updated comment content"
```

Note: You'll need to first get the comment ID by viewing the issue with comments.

## Sub-tickets

When a task requires decomposition into smaller pieces:

1. **Create sub-tickets with:**
   - Clear reference to the parent ticket in the title and description
   - Focused problem statement in the first comment
   - Specific acceptance criteria for this sub-task only
   - Reduced technical scope compared to the parent ticket

2. **Sub-ticket structure:**
   - First comment contains the problem statement and success criteria
   - Technical details are minimal and focused only on the specific sub-task
   - Current state tracking is still maintained in the third comment

3. **Link sub-tickets to parent:**
   - Use GitHub's issue linking functionality
   - Update the parent ticket's Current State to reference all sub-tickets
   - Track overall completion through sub-ticket statuses

This structure enables parallel work on different components while maintaining clear documentation and relationships between tasks.