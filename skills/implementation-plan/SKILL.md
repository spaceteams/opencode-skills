---
name: implementation-plan
description: Create an implementation plan and execute it using strict TDD (red-green-refactor), John Ousterhout's deep-module philosophy, and the Boy Scout rule. Use when the user asks to implement a feature, build something, or execute a plan.
---

Create an implementation plan and execute it using **strict TDD**, **deep-module interface design**, and the **Boy Scout rule**.

### TDD
Every behavior change starts with a failing test. Write **one test at a time**, make it pass with minimal code, then refactor. Horizontal slicing (writing all tests first, then all code) produces imaginary tests that test structure, not behavior. This is not good. Vertical slicing (one tracer bullet at a time) produces tests that are specifications and implementations that live longer.

### Deep Modules (Ousterhout)
> "The best modules are deep: they have a simple interface and hide a great deal of complexity underneath."

Every time you touch a module, ask:
- Can I **reduce the interface** (fewer parameters, fewer exported functions)?
- Can I **hide more information** (move decisions inward, remove leaked abstractions)?
- Is this module **shallow** (thin wrapper, pass-through, trivial delegation)? If so, can I eliminate it or absorb it into its caller?
- Can I **pull complexity downward** (make callers simpler by making the module do more work)?
- Am I introducing **general-purpose code** where a **special-purpose** solution would be simpler?

**Warning signs of shallow modules:**
- Functions that are just `if/else` wrappers around another function
- Classes with 1–2 methods that do nothing but forward arguments
- Interfaces with many methods, but each implementation is trivial
- "Utility" files that are grab-bags of unrelated operations

## Process

### 1. Explore, Understand & Design
Before planning, understand the codebase prefering `cymbal_*` tools over grep. Clarify ambiguities with me one question at a time until we have a sharp plan.

The type system is the Domain Documentation.

- [ ] Read the ticket/issue description
- [ ] Find the **closest analogous feature** already implemented (e.g., "Endabrechnung already does X, we want Vorabentgelt to do X")
- [ ] Read the analogous code end-to-end: schema → repository → domain → container → UI → tests
- [ ] Design the **public interfaces** you will need to create or change
- [ ] Note existing patterns for: error handling, auth, transactions, audit events, task history, interface design
- [ ] Clarify the UX/UI flow
- [ ] Look for **pre-existing shallow modules** in the area you are touching (candidates for deepening)

### 2. Draft the Implementation Plan

Write a `PLAN.md` in the project root. Use this structure:

```markdown
# Implement: <Ticket Title>

## Context
What we learned from exploration. Which existing feature is the analog if any.

## Approach
High-level strategy in 2–3 sentences.

## Explicit Interfaces / Types design
- What interfaces, types and schemas need to change or be added, why and how. Be explicit in the design
- Use the DDD principles present in the codebase
- Respect SOLID principles
- Look for opportunities to deepen modules, instead off adding a new shallow module.
- Good Interfaces & Types are the representation of the domain and ensure maintainability

## Interfaces to modify
Communicate the changes to types and interfaces in code.

```diff
// path/to/file.ts

type Interface = {
+ addProp: number
- removeProp: string
}
```

## Reuse
- Analogous feature: `path/to/analog.ts#feature` – pattern for X
- Helper function: `path/to/helper.ts#function` – reuse for Y

## Steps (TDD – Red, Green, Refactor)

### Cycle 1 – <Split cycles into distinct behaviors>
- [ ] **Red**: Write test `path/to/feature.test.ts`
  - Test: <one sentence describing the behavior>
- [ ] **Green**: Minimal implementation
  - Files: `path/to/feature.ts`, `path/to/repo.ts`
- [ ] **Refactor**: <specific cleanup>

### Cycle 2 – <Next Behavior>
...

### Cycle N – UI / Integration
...

## UI Flow Verification
- Happy paths
- Validation error path
- etc.

## Project specific Verification
- tests pass
- typecheck passes
- linting passes
```

**Rules for the plan:**
- Each cycle is **verifiable** on its own
- Label cycles explicitly: **Red**, **Green**, **Refactor**

### 3. Get Approval

Present the plan to the user.
1. Point out trade-offs in the interface type design
2. Are the cycles distinct enough?

Do not start coding until the user approves.

## Common Pitfalls

**"I'll write all the repository functions first, then the domain, then the UI."**
→ Horizontal slice. Wrong. One behavior at a time, end-to-end.

**"I'll refactor everything at the end."**
→ You won't. Refactor immediately after each green step while the context is hot.

**"This module is already shallow, but I don't want to change existing code."**
→ The Boy Scout rule applies. If you touch the file, deepen it or at least leave it cleaner.

**"I need to generalize this for future use cases."**
→ Ousterhout's warning: general-purpose code is often more complex and less useful than special-purpose code. Build for today's need. If tomorrow's needs are different, the deep module's simple interface makes changing it easy.
