# GitHub to Knowledge Base Synchronization

This document outlines the process for re-instantiating documentation from GitHub issues to the Knowledge Base.

## Process Overview

When working with existing GitHub issues or when documentation must be extracted from GitHub:

1. Identify the GitHub issue to synchronize
2. Extract content from issue description and comments
3. Create or update corresponding KB documents
4. Establish bidirectional links between GitHub and KB
5. Verify synchronization completeness

## Step-by-Step Implementation

### 1. Extract GitHub Issue Content

Use GitHub CLI to retrieve the issue content:

```bash
# Get basic issue information
gh issue view $ISSUE_NUMBER --repo $REPO_NAME > issue_details.txt

# Get issue with all comments
gh issue view $ISSUE_NUMBER --comments --repo $REPO_NAME > issue_with_comments.txt

# Get issue in JSON format for structured processing
gh issue view $ISSUE_NUMBER --json number,title,body,comments --repo $REPO_NAME > issue.json
```

### 2. Parse and Categorize Content

Process the issue content to categorize the information:

- **Problem Statement**: Extract from issue description or first comment
- **Technical Plan**: Extract from second comment (if exists)
- **Current State**: Extract from third comment (if exists)
- **Discussion**: Extract relevant insights from other comments

Example parser (pseudocode):
```python
def parse_github_issue(issue_json):
    issue_data = json.loads(issue_json)
    
    # Extract core information
    issue_number = issue_data['number']
    issue_title = issue_data['title'].replace('#' + str(issue_number), '').strip()
    
    # Initialize content containers
    problem_statement = issue_data['body']
    technical_plan = ""
    current_state = ""
    discussions = []
    
    # Process comments
    comments = issue_data['comments']
    
    if len(comments) >= 1:
        # First comment is typically Problem Statement in the old format
        if "Problem Statement" in comments[0]['body']:
            problem_statement = comments[0]['body']
            
    if len(comments) >= 2:
        # Second comment is typically Technical Plan
        if "Technical Plan" in comments[1]['body']:
            technical_plan = comments[1]['body']
    
    if len(comments) >= 3:
        # Third comment is typically Current State
        if "Current State" in comments[2]['body']:
            current_state = comments[2]['body']
    
    # Remaining comments are discussions
    if len(comments) > 3:
        for comment in comments[3:]:
            discussions.append({
                'author': comment['author']['login'],
                'created_at': comment['createdAt'],
                'body': comment['body']
            })
    
    return {
        'number': issue_number,
        'title': issue_title,
        'problem_statement': problem_statement,
        'technical_plan': technical_plan,
        'current_state': current_state,
        'discussions': discussions
    }
```

### 3. Create Knowledge Base Documents

Generate KB documents based on the parsed content:

```bash
# Create the issue documentation file
mkdir -p _ai/issues
cat << EOF > _ai/issues/${ISSUE_NUMBER}-${ISSUE_SLUG}.md
# ${ISSUE_TITLE}

## Problem Statement
${PROBLEM_STATEMENT}

## Technical Plan
${TECHNICAL_PLAN}

## Related
- GitHub Issue: #${ISSUE_NUMBER}
- Tasks:
  - _ai/tasks/${ISSUE_NUMBER}-task1.md
  - _ai/tasks/${ISSUE_NUMBER}-task2.md
EOF

# Create the current state file
mkdir -p _ai/states
cat << EOF > _ai/states/${ISSUE_NUMBER}-current-state.md
# Current State: ${ISSUE_TITLE}

${CURRENT_STATE_CONTENT}

## Extracted from GitHub on: $(date)
EOF

# Create task files if identified in the technical plan
mkdir -p _ai/tasks
# Parse tasks from the technical plan and create individual files
```

### 4. Extract TDD Evidence

If TDD evidence exists in the GitHub issue (as links or descriptions):

```bash
# Create directory for TDD evidence
mkdir -p _ai/tdd/${ISSUE_NUMBER}-tdd-evidence

# For each piece of evidence found in the issue:
# 1. Extract image URLs or descriptions
# 2. Download images if available
# 3. Create placeholder files for descriptions
# 4. Update references in KB documents
```

### 5. Create Simplified GitHub Issue

After KB documents are created, update the GitHub issue to the new simplified format:

```bash
# Construct the simplified issue description
SIMPLE_DESCRIPTION="$(head -n 1 <<< \"$PROBLEM_STATEMENT\")

📝 **Documentation**: _ai/issues/${ISSUE_NUMBER}-${ISSUE_SLUG}.md
🔄 **Dependencies**: ${DEPENDENCIES}
👥 **Stakeholders**: ${STAKEHOLDERS}
🔍 **Priority**: ${PRIORITY}"

# Update the GitHub issue
gh issue edit $ISSUE_NUMBER --body "$SIMPLE_DESCRIPTION" --repo $REPO_NAME

# Update current state comment if it exists
if [[ -n "$CURRENT_STATE_COMMENT_ID" ]]; then
  SIMPLE_STATE="## Current State

**Status**: ${STATUS}
**KB State**: _ai/states/${ISSUE_NUMBER}-current-state.md
**Updated**: $(date +%Y-%m-%d\ %H:%M)
**Blockers**: ${BLOCKERS}"

  gh api --method PATCH repos/$REPO_OWNER/$REPO_NAME/issues/comments/$CURRENT_STATE_COMMENT_ID \
     -f body="$SIMPLE_STATE"
else
  # Create new current state comment
  gh issue comment $ISSUE_NUMBER --body "$SIMPLE_STATE" --repo $REPO_NAME
fi
```

### 6. Verify Synchronization

Check that all content has been properly synchronized:

```bash
# Verify KB documents exist
check_file_exists "_ai/issues/${ISSUE_NUMBER}-${ISSUE_SLUG}.md"
check_file_exists "_ai/states/${ISSUE_NUMBER}-current-state.md"

# Verify GitHub issue has been updated
gh issue view $ISSUE_NUMBER --repo $REPO_NAME | grep -q "_ai/issues/${ISSUE_NUMBER}"

# Generate synchronization report
echo "Synchronization completed for issue #${ISSUE_NUMBER}"
echo "- KB Documents: $(find _ai -name \"${ISSUE_NUMBER}-*\" | wc -l) created"
echo "- GitHub Issue: Successfully updated to new format"
```

## Script Implementation

Create a `github-to-kb.sh` script that implements this process:

```bash
#!/bin/bash
# GitHub to KB Synchronization Script
# Usage: ./github-to-kb.sh [ISSUE_NUMBER] [REPO_OWNER/REPO_NAME]

ISSUE_NUMBER=$1
REPO=$2

if [[ -z "$ISSUE_NUMBER" || -z "$REPO" ]]; then
  echo "Usage: ./github-to-kb.sh [ISSUE_NUMBER] [REPO_OWNER/REPO_NAME]"
  exit 1
fi

echo "Extracting issue #${ISSUE_NUMBER} from ${REPO}..."

# Get issue content
gh issue view $ISSUE_NUMBER --json number,title,body,comments --repo $REPO > issue_${ISSUE_NUMBER}.json

# Process with a JSON parser (e.g., jq or a Python script)
# This example uses jq for simplicity
TITLE=$(jq -r '.title' issue_${ISSUE_NUMBER}.json | sed "s/#${ISSUE_NUMBER}//g" | tr -d '"' | xargs)
ISSUE_SLUG=$(echo "$TITLE" | tr '[:upper:]' '[:lower:]' | tr ' ' '-' | tr -cd '[:alnum:]-')
BODY=$(jq -r '.body' issue_${ISSUE_NUMBER}.json)
COMMENTS=$(jq -r '.comments' issue_${ISSUE_NUMBER}.json)

# Extract problem statement, technical plan, and current state
# This is simplified; a real implementation would need more robust parsing
PROBLEM_STATEMENT=$BODY
TECHNICAL_PLAN=$(echo "$COMMENTS" | jq -r '.[1].body // ""')
CURRENT_STATE=$(echo "$COMMENTS" | jq -r '.[2].body // ""')
CURRENT_STATE_COMMENT_ID=$(echo "$COMMENTS" | jq -r '.[2].id // ""')

# Extract status and blockers from current state
STATUS=$(echo "$CURRENT_STATE" | grep -oP '(?<=\*\*Status:\*\* )(.+)$' || echo "Unknown")
BLOCKERS=$(echo "$CURRENT_STATE" | grep -oP '(?<=\*\*Blockers:\*\* )(.+)$' || echo "None")

# Create KB directories
mkdir -p _ai/issues _ai/states _ai/tasks _ai/tdd/${ISSUE_NUMBER}-tdd-evidence

# Create KB documents
# [Implementation as shown in step 3 above]

# Update GitHub issue
# [Implementation as shown in step 5 above]

echo "Synchronization completed for issue #${ISSUE_NUMBER}"
```

## Batch Processing

For migrating multiple issues at once, create a batch processing script:

```bash
#!/bin/bash
# Batch GitHub to KB Synchronization
# Usage: ./batch-github-to-kb.sh [REPO_OWNER/REPO_NAME] [ISSUE_NUMBERS_LIST]

REPO=$1
ISSUES_FILE=$2

if [[ -z "$REPO" || -z "$ISSUES_FILE" || ! -f "$ISSUES_FILE" ]]; then
  echo "Usage: ./batch-github-to-kb.sh [REPO_OWNER/REPO_NAME] [ISSUE_NUMBERS_LIST]"
  exit 1
fi

echo "Batch processing issues from ${REPO}..."

# Process each issue in the list
while read -r ISSUE_NUMBER; do
  echo "Processing issue #${ISSUE_NUMBER}..."
  ./github-to-kb.sh $ISSUE_NUMBER $REPO
done < "$ISSUES_FILE"

echo "Batch processing complete"
```

## Handling Special Cases

### 1. Missing Technical Plan

If an issue lacks a proper technical plan:

```bash
# Check if technical plan exists
if [[ -z "$TECHNICAL_PLAN" ]]; then
  echo "Warning: No technical plan found in issue #${ISSUE_NUMBER}"
  
  # Create a basic template
  TECHNICAL_PLAN="## Technical Plan

*This plan was automatically generated as the original issue lacked a technical plan.*

### Approach
- Implement based on problem statement
- Follow standard practices
- Create tests as appropriate

### Components
- TBD based on implementation

### Testing Strategy
- Follow standard TDD approach"
fi
```

### 2. Inconsistent Comment Structure

If comments don't follow the standard pattern:

```bash
# Try to identify comments by content patterns
for i in $(seq 0 $(jq '.comments | length - 1' issue_${ISSUE_NUMBER}.json)); do
  COMMENT=$(jq -r ".comments[$i].body" issue_${ISSUE_NUMBER}.json)
  
  if [[ "$COMMENT" == *"Problem Statement"* && -z "$PROBLEM_STATEMENT" ]]; then
    PROBLEM_STATEMENT=$COMMENT
  elif [[ "$COMMENT" == *"Technical Plan"* && -z "$TECHNICAL_PLAN" ]]; then
    TECHNICAL_PLAN=$COMMENT
  elif [[ "$COMMENT" == *"Current State"* && -z "$CURRENT_STATE" ]]; then
    CURRENT_STATE=$COMMENT
    CURRENT_STATE_COMMENT_ID=$(jq -r ".comments[$i].id" issue_${ISSUE_NUMBER}.json)
  fi
done
```

### 3. Extracting Tasks from Technical Plan

To identify and extract tasks from the technical plan:

```python
def extract_tasks(technical_plan):
    tasks = []
    
    # Look for checklist items
    checklist_pattern = r'- \[([ xX])\] (.+)'
    matches = re.findall(checklist_pattern, technical_plan)
    
    for match in matches:
        completed = match[0].lower() == 'x'
        task_name = match[1].strip()
        tasks.append({
            'name': task_name,
            'completed': completed
        })
    
    # Also look for sections that might indicate components
    section_pattern = r'#+\s+(Component|Task|Implementation):\s+(.+)'
    section_matches = re.findall(section_pattern, technical_plan)
    
    for match in section_matches:
        task_name = match[1].strip()
        if task_name not in [t['name'] for t in tasks]:
            tasks.append({
                'name': task_name,
                'completed': False
            })
    
    return tasks
```

## Recommended Implementation Approach

1. Create a Python script for robust processing of GitHub issue content
2. Use GitHub CLI for fetching data and updating issues
3. Use templates for generating KB documents
4. Add validation to ensure complete data transfer
5. Implement logging for tracking synchronization issues
6. Create a rollback mechanism for failed synchronizations

This comprehensive approach ensures that existing GitHub issues can be efficiently migrated to the new KB-centric system while maintaining all critical information.