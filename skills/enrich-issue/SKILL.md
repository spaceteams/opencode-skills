---
name: enrich-issue
description: Enrich a sparse Jira ticket with a problem, and acceptance criteria through one-question-at-a-time interview and authoritative research, so a dev can pick it up and refine further. Use when user wants to "fill a ticket with life", "refine a ticket" (without breaking it into sub-issues), "flesh out", or "make a ticket actionable". Distinct from refine-issue, which breaks a ticket into vertical-slice sub-issues.
---

# Enrich Issue

Turn a sparse ticket into a pick-up-ready self-contained user story: a dev can read it and start work, or refine it further.
User stories are often expressed in a simple sentence, structured as follows:

“As a [persona], I [want to], [so that].”

Breaking this down:

"As a [persona]": Who are we building this for? We’re not just after a job title, we’re after the persona of the person. Max. Our team should have a shared understanding of who Max is. We’ve hopefully interviewed plenty of Max’s. We understand how that person works, how they think and what they feel. We have empathy for Max.

“Wants to”: Here we’re describing their intent — not the features they use. What is it they’re actually trying to achieve? This statement should be implementation free — if you’re describing any part of the UI and not what the user goal is you're missing the point.

“So that”: how does their immediate desire to do something this fit into their bigger picture? What’s the overall benefit they’re trying to achieve? What is the big problem that needs solving?

For example, user stories might look like:
As a dev, I want to invite my friends, so we can enjoy this service together.
As a product owner, I want to organize my work, so I can feel more in control.
As a manager, I want to be able to understand my colleagues progress, so I can better report our success and failures.

## Process

### 1. Fetch the issue
Use /skill:jira-issue-context. Read the body and recent comments. Also search for other issues that reference the given ticket.

### 2. Explore the codebase (always)
Ground every decision in the current code. Specifically find:
- What the issue *assumes* vs what already exists (the issue may be stale or wrong).
- Code that contradicts the issue's framing — surface it.
- Existing infra/config that already satisfies a stated requirement (don't re-solve solved problems).
- Read CONTEXT.md files to gather domain knowledge

### 3. Confirm the issue language BEFORE writing
Do NOT assume the body language from any skill template. Ask the user, or default to English.

### 4. Interview — one question at a time
Interview me relentlessly about every domain and high level technical aspect of this ticket until we reach a shared understanding. The goal is to pin down the persona, the intent and the desire. Walk down each branch of the design tree, resolving dependencies between decisions one-by-one. A good question:
- Has 2-4 concrete, mutually exclusive options with real trade-offs.
- Surfaces the tension the user may not have noticed.

Order questions so each one unblocks the next. We don't care about specific implementation details. Start with the most fundamental branch.

Update the CONTEXT.md files if assumptions change during the discovery process.

### 5. Research to verify user hypotheses
When the user states a hypothesis about external platform behavior ("can socket mode route per connection?"), do NOT guess and do NOT accept the hypothesis unchecked. Use web_search + web_fetch on authoritative sources (official docs, maintainer issues). Report the verdict with citations before continuing the interview.

### 6. Challenge contradictions explicitly
When a stated goal conflicts with a chosen approach, name the conflict in plain terms and ask how to resolve it. Never silently drop a stated goal and never silently let it stand unresolvable.

### 7. Draft the body
<template>
**As a** [persona]
**I want to** [intent]
**so that** [desire]

A one-liner description of the goal we want to achieve with this ticket.

A short description of the core problem that needs to be solved, describing the current state and gap to the desired goal. Has to be highly structured and easily digestible.

## Acceptance Criteria
- Bullet list of brief behavioral invariants a dev/QA can verify end-to-end (no technical details unless necessary)
  - Indented bullets group optional surfaced edge cases that live under that invariant

## Out of Scope
- Bullet list of features that are explicitly out of scope
</template>

Writing guides:
- No em dashes.
- No semicolons
- No tables unless it's the only way to display data in a sensible way.
- No unrelated references to future ideas that are not to be implemented in the ticket
- No specific code file references, since they will be outdated quickly.
- The consumer of this ticket does not have our conversational context, so the ticket has to be self-explanatory and not reference talking points from the conversation

### 8. Write it back
`cat body.md | jira issue edit ISSUE-KEY --no-input`. Then re-fetch and confirm the body landed as intended.

### 9. Handle out of scopes
For every part we declared as out of scope, check with me whether you should create a sparse ticket.
A sparse ticket has a descriptive title and a few short bullet points as a mental help.

## Do's

- **Ground in the code first.** The code shows what's already there and what is missing
- **Use JQL to check for other tickets.** We have a backlog, maybe a question is already answered there
- **One question at a time, dependency-ordered.** Each answer unblocked the next.
- **Research user hypotheses against authoritative sources.** Rely on web research instead of training data
- **Challenge the user's own framing.** There might be better goals for the users intent

## Don'ts

- **DON'T assume the issue body language.** Ask or default to English. Do not copy a skill template's language.
- **DON'T force sub-issues / vertical-slice breakdown.** If the user wants one enriched ticket, do that.
- **DON'T guess platform behavior.** If unsure how Slack/AWS/etc. route or notify, research it. A wrong claim in a ticket misleads the dev who picks it up.
