---
name: implement
description: "Implement a piece of work based on a spec or set of tickets."
disable-model-invocation: true
---

Implement the work described by the user in the spec or tickets.

Read the ticket's `## Stories covered`, `## Invariants covered`, `## Seams` and `## Constraints (ADR)`, where it has them, before starting.

Use /tdd where possible, at pre-agreed seams. The seams listed under `## Seams` are pre-agreed, and the stories and invariants the ticket covers are the behaviours those tests verify. Keep their IDs out of test names.

For an invariant that must be preserved, write its test at the listed seam before changing anything and check it passes on the current code; it must stay green after the change. An invariant that changes goes through the normal red-green loop.

If a story or invariant can't be verified at the listed seams, ask the user if one is present. Otherwise don't add a seam: report the gap in your final summary and as a comment on the ticket.

Run typechecking regularly, single test files regularly, and the full test suite once at the end.

Before /code-review, check that every `## Constraints (ADR)` line still holds.

Once done, use /code-review to review the work.

Commit your work to the current branch.
