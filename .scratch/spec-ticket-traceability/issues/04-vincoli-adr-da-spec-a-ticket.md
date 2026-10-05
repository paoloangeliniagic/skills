# 04: Vincoli ADR da spec a ticket

Type: grilling
Status: open
Blocked by: 02

## Question

Oggi `to-spec` e `to-tickets` dicono solo di "rispettare gli ADR nell'area toccata". Già deciso: nel ticket il vincolo va riformulato in una riga applicabile, più il link all'ADR. Resta da decidere:

- `to-spec` raccoglie gli ADR applicabili in una sezione propria (per es. "Governing decisions") o dentro `Implementation Decisions`?
- Chi scrive la riformulazione in una riga: `to-spec` (una volta, nella spec) o `to-tickets` (per ticket, solo la parte che quel ticket tocca)?
- Come si trovano gli ADR rilevanti (`docs/adr/`, `CONTEXT-MAP.md` multi-contesto) e cosa succede se un ticket contraddice un ADR?
