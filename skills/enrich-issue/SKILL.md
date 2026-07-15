---
name: enrich-issue
description: Enrich a sparse Jira ticket with a problem, and acceptance criteria through one-question-at-a-time interview and authoritative research, so a dev can pick it up and refine further. Use when user wants to "fill a ticket with life", "refine a ticket" (without breaking it into sub-issues), "flesh out", or "make a ticket actionable". Distinct from refine-issue, which breaks a ticket into vertical-slice sub-issues.
---

# Enrich Issue

Turn a sparse ticket into a pick-up-ready ticket: a dev can read it and start work, or refine it further.

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
Interview me relentlessly about every domain and high level technical aspect of this ticket until we reach a shared understanding. Walk down each branch of the design tree, resolving dependencies between decisions one-by-one. A good question:
- Has 2-4 concrete, mutually exclusive options with real trade-offs.
- Surfaces the tension the user may not have noticed.

Order questions so each one unblocks the next. We don't care about specific implementation details. Start with the most fundamental branch.

Update the CONTEXT.md files if assumptions change during the discovery process.

### 5. Research to verify user hypotheses
When the user states a hypothesis about external platform behavior ("can socket mode route per connection?"), do NOT guess and do NOT accept the hypothesis unchecked. Use web_search + web_fetch on authoritative sources (official docs, maintainer issues). Report the verdict with citations before continuing the interview.

### 6. Challenge contradictions explicitly
When a stated goal conflicts with a chosen approach, name the conflict in plain terms and ask how to resolve it. Never silently drop a stated goal and never silently let it stand unresolvable.

### 7. Draft the body
Write in two sections:

- **Goal** — A one liner that summarizes the goal of this ticket.
- **Problem** — briefly explain the core problem that needs to be solved, describing the current state and gap to the desired goal.
- **Acceptance Criteria** — bullet list of brief behavioral outcomes a dev/QA can verify end-to-end (no technical details unless necessary).
- **Out of Scope** — Things a dev might want to do in this ticket, but are explicitly out of scope presented in bullet points and references to other tickets if applicable.

Writing guides:
- No em dashes, sentences have to be concise.
- No bold and italic formatting
- No semicolons
- No unrelated references to future ideas that are not to be implemented in the ticket
- No specific code file references, since they will be outdated quickly.

### 8. Write it back
`cat body.md | jira issue edit ISSUE-KEY --no-input`. Then re-fetch and confirm the body landed as intended.

## Do's (what made the reference session work)

- **Ground in the code first.** Finding "socket mode already enabled" and "Fargate already wired" prevented re-solving solved problems and made the real gap (self-install + dev/prod isolation) obvious.
- **One question at a time, dependency-ordered.** Tenancy → install flow → token source → deploy trigger. Each answer unblocked the next.
- **Research user hypotheses against authoritative sources.** Slack's own docs settled the socket-mode routing question definitively; a maintainer issue confirmed it.
- **Challenge the user's own framing.** Surfacing "install itself contradicts pure socket mode" forced a real decision instead of a silently broken ticket.

## Don'ts (explicitly rejected or not important — do not propose these)

- **DON'T assume the issue body language.** Ask or default to English. Do not copy a skill template's language.
- **DON'T force sub-issues / vertical-slice breakdown.** If the user wants one enriched ticket, do that.
- **DON'T pursue "self-install" / OAuth HTTP bootstrap for a socket-mode app.** Socket mode serves no HTTP; OAuth needs an HTTP callback. Install is manual (e.g. `slack install`). Only revisit if the user explicitly accepts an ALB/HTTP endpoint.
- **DON'T propose multi-tenant installation for a single-org internal tool.** Single-tenant is the default for internal company apps.
- **DON'T add startup token validation if the framework already fails fast.** If Bolt/`app.start()` errors on missing tokens, that IS the fail signal — no extra `auth.test` check needed.
- **DON'T introduce AWS Secrets Manager / DB InstallationStore when GitHub Secrets already work.** Keep the simplest token source unless the user asks for better secret hygiene.
- **DON'T assume prod deploys auto on `main`.** For a customer-facing bot, confirm the deploy trigger explicitly — prod may need a manual gate while dev auto-deploys.
- **DON'T propose dropping dev-Fargate just because local dev is per-dev.** A shared dev/staging Fargate may stay auto-deployed; local dev isolates via separate apps, not by removing shared infra.
- **DON'T guess platform behavior.** If unsure how Slack/AWS/etc. route or notify, research it. A wrong claim in a ticket misleads the dev who picks it up.
