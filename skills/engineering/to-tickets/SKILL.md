---
name: to-tickets
description: Break a plan, spec, or the current conversation into a set of tracer-bullet tickets, each declaring its blocking edges, published to the configured tracker (edges as text in one file per ticket locally, or native blocking links on a real tracker).
disable-model-invocation: true
---

# To Tickets

Break a plan, spec, or conversation into a set of **tickets**: tracer-bullet vertical slices, each declaring the tickets that **block** it.

The issue tracker and triage label vocabulary should have been provided to you. If not, tell the user to run `/setup-matt-pocock-skills`.

## Process

### 1. Gather context

Work from whatever is already in the conversation context. If the user passes a reference (a spec path, an issue number or URL) as an argument, fetch it and read its full body and comments.

Note which traceability sections the source has: `## User Stories` (`US<n>`), `## Invariants` (`I<n>`), `## Seams` (`S<n>`) and `## Constraints (ADR)`. A plan, a conversation, or an older spec may have none of them; never invent IDs for it.

### 2. Explore the codebase (optional)

If you have not already explored the codebase, do so to understand the current state of the code. Ticket titles and descriptions should use the project's domain glossary vocabulary. Read the ADRs in the area you're touching: an ADR that constrains a ticket but is missing from the spec's `## Constraints (ADR)` still goes on that ticket and is flagged in the quiz, and so is any conflict with an ADR you find here first.

Look for opportunities to prefactor the code to make the implementation easier. "Make the change easy, then make the easy change."

### 3. Draft vertical slices

Break the work into **tracer bullet** tickets.

<vertical-slice-rules>

- Each slice cuts a narrow but COMPLETE path through every layer (schema, API, UI, tests): vertical, NOT a horizontal slice of one layer
- A completed slice is demoable or verifiable on its own
- Each slice is sized to fit in a single fresh context window
- Any prefactoring should be done first

</vertical-slice-rules>

Give each ticket its **blocking edges**: the other tickets that must complete before it can start. A ticket with no blockers can start immediately.

Give each ticket the traceability items it covers, copied from the source:

- **Stories covered**: each relevant story, verbatim, with its `US<n>`.
- **Invariants covered**: each relevant invariant, verbatim, with its `I<n>`.
- **Seams**: each relevant seam, keeping its `S<n>`, restated in one line for this ticket.
- **Constraints (ADR)**: each relevant line from the spec's `## Constraints (ADR)`. You may narrow it to the part this ticket touches, never widen it. A "contradicted, worth reopening" line travels unchanged and adds the acceptance criterion "ADR-NNNN updated or superseded by a new ADR".

A story or invariant that spans several tickets carries the same ID in each, with no partial-coverage markers or sub-IDs.

**Wide refactors are the exception to vertical slicing.** A **wide refactor** is one mechanical change (rename a column, retype a shared symbol) whose **blast radius** fans across the whole codebase, so a single edit breaks thousands of call sites at once and no vertical slice can land green. Don't force it into a tracer bullet; sequence it as **expand–contract**. First expand: add the new form beside the old so nothing breaks. Then migrate the call sites over in batches sized by blast radius (per package, per directory), each batch its own ticket blocked by the expand, keeping CI green batch to batch because the old form still exists. Finally contract: delete the old form once no caller remains, in a ticket blocked by every migrate batch. When even the batches can't stay green alone, keep the sequence but let them share an integration branch that all block a final integrate-and-verify ticket; green is promised only there.

### 4. Quiz the user

Present the proposed breakdown as a numbered list. For each ticket, show:

- **Title**: short descriptive name
- **Blocked by**: which other tickets (if any) must complete first
- **What it delivers**: the end-to-end behaviour this ticket makes work
- **Covers**: the IDs it carries, as `Covers: US1, US3 · I2 · S1 · ADR-0002`

After the list, show one **Coverage gaps** block listing only what no ticket covers, plus "ADR-NNNN not cited by the spec" for each ADR you added in step 2, and each ADR conflict you found in step 2 that the spec doesn't already record. Omit the block when it would be empty.

- An ID is covered when it appears in at least one ticket. Stories, invariants, seams and every `## Constraints (ADR)` line (contradicted ones included) are in scope.
- Leave out `(removed)` items and stories or invariants already in the spec's Out of Scope.
- Stories and invariants on the spec's `Not verified at any seam` line still need a ticket.
- If the source has no traceability sections, print `No traceability IDs in the source: coverage check skipped.` instead of the block. If it has only some, check only those.

Every gap needs an explicit answer before publishing, but none blocks it:

- **Cover**: add the ID to an existing ticket or create one, then recalculate the breakdown.
- **Out of scope** (stories and invariants only), with a reason.
- **Accept**, with a reason: nothing will be built for it. For seams and ADRs this is the only alternative to cover.

For an ADR conflict, the choices are to adapt the ticket or to reopen the ADR (the ticket's line then reads "contradicted, worth reopening because <reason>").

Ask the user:

- Does the granularity feel right? (too coarse / too fine)
- Are the blocking edges correct: does each ticket only depend on tickets that genuinely gate it?
- Should any tickets be merged or split further?
- How should each coverage gap be answered, if there are any?

Iterate until the user approves the breakdown and every gap has an answer.

### 5. Publish the tickets to the configured tracker

Publish the approved tickets. **How** depends on the tracker `/setup-matt-pocock-skills` configured; the tickets are the same either way, only the shape of the blocking edges changes:

- **Local files** → write one file per ticket under `.scratch/<feature-slug>/issues/<NN>-<slug>.md`, numbered from `01` in dependency order (blockers first). Each file's "Blocked by" lists the numbers/titles it depends on. Use the per-ticket file template below: one ticket per file, never a single combined file.
- **A real issue tracker (GitHub, Linear, …)** → publish one issue per ticket in dependency order (blockers first) so each ticket's blocking edges can reference real identifiers. Use the platform's native blocking / sub-issue relationship where it has one; otherwise set each ticket's "Blocked by" to the blocking issues. Apply the `ready-for-agent` triage label unless instructed otherwise; the tickets are agent-grabbable by construction.

Work the **frontier**: any ticket whose blockers are all done. For a purely linear chain that means top to bottom.

After publishing, if any gap was answered out of scope or accept, record the answers in one `Coverage` comment on the parent spec, one line per gap:

```
Coverage
US9: out of scope, <reason>
S2: accepted, <reason>
ADR-0004: accepted, <reason>
```

On the local tracker, append it under `## Comments` at the end of the spec file. When every gap was covered, post no comment.

Do NOT close the parent issue or edit its body: the `Coverage` comment is the only write to it.

<local-ticket-template>

# <NN>: <Ticket title>

**Parent:** the path to the spec file (omit if the source isn't a file).

**What to build:** the end-to-end behaviour this ticket makes work, from the user's perspective, not a layer-by-layer implementation list.

**Blocked by:** the numbers/titles of the tickets that gate this one, or "None (can start immediately)".

**Status:** ready-for-agent

## Stories covered

- US3: As a ..., I want ..., so that ...

## Invariants covered

- I2: <property>

## Seams

- S1: <one line, restated for this ticket>

## Constraints (ADR)

- [ADR-0002](/docs/adr/0002-x.md): <one-line constraint, narrowed to this ticket>

## Acceptance criteria

- [ ] Acceptance criterion 1
- [ ] Acceptance criterion 2

</local-ticket-template>

<issue-template>

## Parent

A reference to the parent issue on the tracker (if the source was an existing issue, otherwise omit this section).

## What to build

The end-to-end behaviour this ticket makes work, from the user's perspective, not layer-by-layer implementation.

## Stories covered

- US3: As a ..., I want ..., so that ...

## Invariants covered

- I2: <property>

## Seams

- S1: <one line, restated for this ticket>

## Constraints (ADR)

- [ADR-0002](link): <one-line constraint, narrowed to this ticket>

## Acceptance criteria

- [ ] Criterion 1
- [ ] Criterion 2

## Blocked by

- A reference to each blocking ticket, or "None (can start immediately)".

</issue-template>

In either form, omit any of `## Stories covered`, `## Invariants covered`, `## Seams` and `## Constraints (ADR)` that has no entries. ADR links are the full URL to the ADR file on the default branch on a real tracker, and a path from the repo root with a leading `/` on the local tracker.

In either form, avoid specific file paths or code snippets: they go stale fast. ADR links are allowed: ADRs are superseded, not moved, so the link doesn't go stale. Exception: if a prototype produced a snippet that encodes a decision more precisely than prose can (state machine, reducer, schema, type shape), inline it and note briefly that it came from a prototype. Trim to the decision-rich parts, not a working demo, just the important bits.
