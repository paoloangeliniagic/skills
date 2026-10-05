# 01: Confronto delle patch di tracciabilità già esistenti

Type: research
Status: open
Blocked by: none

## Question

Quali soluzioni concrete esistono già per portare user stories, seam e vincoli ADR da spec a ticket, e quali trade-off mostrano? Analizzare almeno:

- sridhar-3009/skills commit 2922bf2 ("Governing Decisions" nella spec, "Parent spec" nel ticket, mattpocock/skills#613)
- Seekers2001/skills branch `codex/decision-traceability-v2` (decision ledger, puntatore "Delivery", mattpocock/skills#341)
- la patch proposta in mattpocock/skills#959 (source ledger degli impegni della spec)
- la proposta di mattpocock/skills#1078 (campo seam nei ticket)
- i fork con `## User stories addressed` (flh-raouf/SQLab `prd-to-issues`) e il formato `[Story]` di GitHub Spec Kit `tasks-template.md`

Per ognuna: dove vivono gli ID, quali campi aggiunge a spec e ticket, se c'è un controllo di copertura, quanto peso aggiunge ai template. L'output alimenta la decisione tra registro generale e campi mirati (ticket 02).
