---
name: refine-issue
description: Break a issue into independently-grabbable issues using tracer-bullet vertical slices. Use when user wants to refine a issue to sub issues, create implementation tickets, or break down a issue into work items.
---

# Refine Issue

Break a ticket into independently-grabbable sub issues.

## Process

### 1. Fetch the issue

Use /skill:jira-issue-context to fetch the given issue number, read it and read the `CONTEXT.md` in the project root to gain understanding of key domain terms.

### 2. Explore the codebase (optional)

If you have not already explored the codebase, do so using codegraph tools to understand the current state of the code.

### 3. Gain understanding

Interview me relentlessly about every aspect of this ticket until we reach a shared understanding. Walk down each branch of the design tree, resolving dependencies between decisions one-by-one.

Ask one question at a time.

#### Challenge against the glossary

When the user uses a term that conflicts with the existing language in `CONTEXT.md`, call it out immediately. "Your glossary defines 'cancellation' as X, but you seem to mean Y — which is it?"

#### Sharpen fuzzy language

When the user uses vague or overloaded terms, propose a precise canonical term. "You're saying 'account' — do you mean the Customer or the User? Those are different things."

#### Discuss concrete scenarios

When domain relationships are being discussed, stress-test them with specific scenarios. Invent scenarios that probe edge cases and force the user to be precise about the boundaries between concepts.

#### Cross-reference with code

When the user states how something works, check whether the code agrees. If you find a contradiction, surface it: "Your code cancels entire Orders, but you just said partial cancellation is possible — which is right?"

#### Update CONTEXT.md inline

When a term is resolved, update `CONTEXT.md` right there. Don't batch these up — capture them as they happen. Use the format in [CONTEXT-FORMAT.md](./CONTEXT-FORMAT.md).

`CONTEXT.md` should be totally devoid of implementation details. Do not treat `CONTEXT.md` as a spec, a scratch pad, or a repository for implementation decisions. It is a glossary and nothing else.

### 4. Draft vertical slices

Break the issue into **tracer bullet** issues. Each issue is a thin vertical slice that preferably cuts through ALL integration layers end-to-end, NOT a horizontal slice of one layer. If the ticket is so small that vertical slices are not recommended, do communicate that.

<vertical-slice-rules>
- Each slice delivers a narrow but COMPLETE path through every layer (schema, API, UI, tests)
- A completed slice is demoable or verifiable on its own
- Prefer many thin slices over few thick ones
- e2e testing everything is NOT a vertical slice
- Before introducting new dependencies, check if they are well maintained and compatible with the current tech stack and their versions
</vertical-slice-rules>

### 5. Ask the user

Present the proposed breakdown as a numbered list. For each slice, show:

- **Title**: short descriptive name
- **Acceptance covered**: which acceptance criteria from the main issue this addresses

Ask the user:

- Does the granularity feel right? (too coarse / too fine)
- Should any slices be merged or split further?

Iterate until the user approves the breakdown.

### 6. Create the sub issues

For each approved slice, create a sub issue using the jira cli using the create-jira-issue skill. Use the issue body template below.

Create issues in dependency order (blockers first) so you can reference real issue numbers in the "Blocked by" field.

<issue-template>
A concise description of this vertical slice in german language. Describe the end-to-end behavior, not layer-by-layer implementation. Reference specific sections of the parent PRD rather than duplicating content. Leave out tests

## Akzeptanzkriterien

- Criterion 1
- Criterion 2
- Criterion 3

</issue-template>

Connect issues that this issue is blocked by (if any)

Do NOT close or modify the parent issue.