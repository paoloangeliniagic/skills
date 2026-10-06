---
"mattpocock-skills": minor
---

Carry user stories, invariants, seams and ADR constraints from the spec into every ticket, by stable ID plus text, so `implement` and `implement-spec` can build a ticket without rereading the spec and no commitment falls between the slices.

- **`to-spec`** writes the final numbered user stories (`US<n>`) and, where they fit, numbered invariants (`I<n>`) first, then derives the seams from them. A new `## Seams` section names the stories and invariants each seam (`S<n>`) verifies and declares any item verified at no seam with a reason. A new `## Constraints (ADR)` section lists, one line plus a link, only the ADRs that rule out an option. One checkpoint covers seams, gaps, the story or invariant classification, and ADR conflicts. IDs are appended only and never reused. A refactor spec can now use `## Invariants` instead of inventing stories.
- **`to-tickets`** copies the relevant items into each ticket's `## Stories covered`, `## Invariants covered`, `## Seams` and `## Constraints (ADR)` sections, in both the issue and the local template. Local tickets gain a `**Parent:**` line and an `## Acceptance criteria` heading. The quiz shows a `Covers:` line per ticket and a `Coverage gaps` block; every gap gets an explicit answer (cover, out of scope, accept) without blocking publication, and the non-cover answers are recorded in one `Coverage` comment on the parent spec. A source with no IDs skips the check with one line.
- **`implement`** reads the four traceability sections, treats the listed seams as pre-agreed and the covered stories and invariants as what its tests verify, pins preserved invariants before changing code, reports a missing seam instead of adding one when no user is present, and checks the ADR lines before `/code-review`.
- **`implement-spec`** gives each implementer subagent the same traceability rules (it never adds a seam), and its final report lists every seam gap the implementers reported. Its orchestration is otherwise unchanged.
- **`tdd`** treats seams agreed upstream, such as a ticket's `## Seams` section, as pre-agreed and confirms only new ones.
