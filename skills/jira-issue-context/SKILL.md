---
name: jira-issue-context
description: Load Jira issue details into context using the jira CLI. Use whenever the user mentions a Jira issue key (e.g. RISE-123, PROJ-456), asks to work on an issue, references a ticket, or says "the issue", "that ticket", "the story", or similar. Always load the issue before responding.
---

# Jira Issue Context

## When to activate

Load this skill **before responding** whenever the user's message contains or references a Jira issue key (pattern `[A-Z]+-\d+`), or uses phrases like "the issue", "that ticket", "the story", "the bug", "the task" when the issue key is inferable from context.

## Workflow

1. **Extract the issue key** from the user's message (e.g. `RISE-123`).
2. **Fetch the issue** using the CLI:
   ```sh
   jira issue view ISSUE-KEY --comments 5
   ```
3. **Read the output** into your context — it contains the summary, description, status, assignee, labels, and recent comments.
4. **Proceed** with the user's request, now fully informed about the issue.

## Commands reference

| Goal | Command |
|---|---|
| View issue details | `jira issue view ISSUE-KEY --comments 5` |
| View raw JSON | `jira issue view ISSUE-KEY --raw` |
| List open issues | `jira issue list -a` |
| List issues in sprint | `jira sprint list` then `jira issue list -sSPRINT-NAME` |
| Comment on issue | `jira issue comment ISSUE-KEY -b "comment text"` |
| Transition issue | `jira issue move ISSUE-KEY STATUS` |
| Assign issue | `jira issue assign ISSUE-KEY USER` |

## Rules

- **Always fetch before acting.** Never guess what an issue contains.
- If `jira issue view` fails, check that the project is configured (`jira init`) and the key is correct.
- Use `--raw` when you need structured data (e.g. custom fields).
- The default project is set in `~/.config/.jira/.config.yml` — override with `-p PROJECT` if the issue belongs to a different project.
