---
name: create-issue
description: Turn a grill-me session into a new ready-to-pick-up Jira ticket. Drafts a Problem / Proposed Solution / Acceptance Criteria body from the resolved decision tree, then creates the issue via `jira issue create`. Use when the user ran /skill:grill-me (or wants to stress-test a plan) and then asks to create a ticket from it.
---

# Grilled Issue

Turn the conclusions of a grill-me session into a fresh, pick-up-ready Jira ticket. A dev can read it and start work, or refine it further.

## Process

### Explore the codebase (always)
Ground every decision in the current code. Specifically find:
- What the plan *assumes* vs what already exists (the plan may be stale or wrong).
- Code that contradicts the user's framing — surface it before writing.
- Existing infra/config that already satisfies a stated requirement (don't re-solve solved problems).

### Challenge contradictions explicitly
When a stated goal conflicts with a chosen approach, name the conflict in plain terms and ask how to resolve it. Never silently drop a goal and never let an unresolvable tension stand in the ticket.

### Confirm language BEFORE writing
Do NOT assume the body language from any skill template. Ask the user.

### Confirm ticket metadata
Before creating, confirm with the user:
- **Summary** — one-line title (propose one from the resolved plan).
- **Issue type** — must match the project exactly. Check config: `cat ~/.config/.jira/.config.yml | grep -A 3 "types:"` (common: Story, Task, Bug).
- **Parent** — optional, only if this ticket is genuinely a sub-issue. If so, get the parent key and the correct sub-task type name.
- **Project** — only override `-p` if the issue belongs to a non-default project.

### Draft the body
Write in three sections, framed as a *proposal* the next dev may refine:

- **Problem** — the concrete pain, with the root cause (cite docs/code that prove it).
- **Proposed Solution** — the chosen topology/approach
- - **Acceptance Criteria** — checkable outcomes a dev/QA can verify end-to-end.

### 7. Create the issue
Use the /skill:create-jira-issue workflow.

### 8. Verify it landed
Re-fetch and confirm:

```bash
jira issue view CREATED-KEY --comments 5
```
