---
name: create-jira-issue
description: Create Jira issues and link them to parent issues using the jira CLI. Use when user mentions "create Jira issue", "create ticket", "create sub-task", "Jira issue", or wants to break down work into sub-issues.
---

# Create Jira Issue

## Quick start

```bash
# Basic issue creation
jira issue create -t <type> -s "Summary" -b "Description"

# Create a sub-task linked to a parent
jira issue create -t Subtask -P RISE-123 -s "Sub-task summary" -b "Description"
```

## Workflows

### 1. Discover issue types

Issue type names must match the project exactly. Check available types first:

```bash
# List configured issue types for the current project
cat ~/.config/.jira/.config.yml | grep -A 3 "types:"
```

Or look at the Jira UI. Common types: `Story`, `Task`, `Bug`, `Subtask`.

### 2. Create a standalone issue

```bash
jira issue create -t Task -s "Implement login" -b "Details go here"
```

### 3. Create a sub-task / child issue

```bash
jira issue create -t Subtask -P PARENT-KEY -s "Child summary" -b "Child description"
```

**Flags:**
- `-t, --type` — Issue type (must match project config exactly)
- `-P, --parent` — Parent issue key (required for sub-tasks)
- `-s, --summary` — Issue title
- `-b, --body` — Issue description (supports `$'...'` for newlines)
- `-p, --project` — Override default project

### 4. Create with multi-line description

```bash
jira issue create -t Subtask -P RISE-508 -s "Slice 1" -b $'## What to build\n\nDescription here.'
```

## Tips

- If `issuetype` error occurs, double-check the exact type name in the config (case-sensitive).
- The CLI outputs the created issue key (e.g. `RISE-514`).
- Use `--no-input` to skip prompts for non-required fields.
