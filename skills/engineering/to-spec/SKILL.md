---
name: to-spec
description: "Turn the current conversation into a spec and publish it to the project issue tracker: no interview, just synthesis of what you've already discussed."
disable-model-invocation: true
---

This skill takes the current conversation context and codebase understanding and produces a spec. Do NOT interview the user; just synthesize what you already know.

The issue tracker and triage label vocabulary should have been provided to you. If not, tell the user to run `/setup-matt-pocock-skills`.

## Process

1. Explore the repo to understand the current state of the codebase, if you haven't already. Use the project's domain glossary vocabulary throughout the spec. Read the ADRs in the area you're touching, found through the project's domain-doc rules.

2. Write the final numbered user stories and/or invariants, but don't publish yet. Classify each item with one rule: a behaviour an actor sees and asks for is a user story; a property a caller relies on at an interface is an invariant. A feature spec has stories and may add invariants; a refactor or module-boundary spec may have invariants only.

3. Derive the seams from those items. Start from the highest existing seam that can verify them, and add a new seam, as high as possible, only when an item can't be verified at the seams already chosen. Every seam must verify at least one item. The fewer seams across the codebase, the better: the ideal number is one. Then pick the ADRs that rule out an option the implementer could otherwise choose, and note any the spec contradicts.

4. Check with the user once, in a single checkpoint, showing:
   - each seam and the items it verifies;
   - each item verified at no seam, with three choices: add or raise a seam, accept it with a reason (it goes on the gap line), or move it to Out of Scope;
   - how you classified each item as a story or an invariant;
   - each ADR conflict, with two choices: adapt the spec, or reopen the ADR (its line then reads "contradicted, worth reopening because <reason>"; leave the ADR file itself untouched).

   This is still not an interview about the stories: only these items are up for correction.

5. Write the rest of the spec using the template below, then publish it to the project issue tracker. Apply the `ready-for-agent` triage label - no need for additional triage.

<spec-template>

## Problem Statement

The problem that the user is facing, from the user's perspective.

## Solution

The solution to the problem, from the user's perspective.

## User Stories

A LONG, numbered list of user stories. Each story is referenced elsewhere as `US<n>`, where `n` is its number in this list. Each user story should be in the format of:

1. As an <actor>, I want a <feature>, so that <benefit>

<user-story-example>
1. As a mobile bank customer, I want to see balance on my accounts, so that I can make better informed decisions about my spending
</user-story-example>

This list of user stories should be extremely extensive and cover all aspects of the feature.

## Invariants

A numbered list of properties a caller relies on at an interface, each referenced elsewhere as `I<n>`:

1. <property that must hold>

At least one of `## User Stories` and `## Invariants` must be present. Omit whichever is empty.

## Seams

One numbered line per seam, referenced elsewhere as `S<n>`, naming the stories and invariants it verifies. An item may appear under more than one seam.

S1: <seam in one line> (existing|new). Verifies: US1, US3, I2
Not verified at any seam: US9 (<reason>), I4 (<reason>)

Omit the last line when every item is verified at some seam.

IDs (`US<n>`, `I<n>`, `S<n>`) are appended only and never reused: a removed item stays in place, struck through and marked `(removed)`.

## Constraints (ADR)

One line per ADR that rules out an option the implementer could otherwise pick, applied to this spec. Omit the section when no ADR binds the spec.

- [ADR-0002](link): <constraint in one line, applied to this spec>
- [ADR-0007](link): contradicted, worth reopening because <reason>

On a real issue tracker, the link is the full URL to the ADR file on the default branch. On the local tracker, it is a path from the repo root with a leading `/`.

## Implementation Decisions

A list of implementation decisions that were made. This can include:

- The modules that will be built/modified
- The interfaces of those modules that will be modified
- Technical clarifications from the developer
- Architectural decisions
- Schema changes
- API contracts
- Specific interactions

Do NOT include specific file paths or code snippets. They may end up being outdated very quickly. ADR links are allowed: ADRs are superseded, not moved, so the link doesn't go stale.

Exception: if a prototype produced a snippet that encodes a decision more precisely than prose can (state machine, reducer, schema, type shape), inline it within the relevant decision and note briefly that it came from a prototype. Trim to the decision-rich parts, not a working demo, just the important bits.

## Testing Decisions

A list of testing decisions that were made. Include:

- A description of what makes a good test (only test external behavior, not implementation details)
- What will be tested where: see `## Seams`
- Prior art for the tests (i.e. similar types of tests in the codebase)

## Out of Scope

A description of the things that are out of scope for this spec.

## Further Notes

Any further notes about the feature.

</spec-template>
