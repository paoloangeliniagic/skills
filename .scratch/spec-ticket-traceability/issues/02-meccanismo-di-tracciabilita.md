# 02: Meccanismo di tracciabilità: registro generale o campi mirati

Type: grilling
Status: open
Blocked by: 01

## Question

Come viaggiano gli impegni della spec fino ai ticket?

- (a) un **registro generale degli impegni** nella spec (stories, invarianti, seam, ADR, non-goal), con ID, che ogni ticket cita (stile mattpocock/skills#959)
- (b) **campi mirati** nei template di `to-tickets`: `Stories covered`, `Seams`, `Constraints (ADR)`, con ID stabili in `to-spec` (`US1`, `S1`, ...) come ponte

Decidere anche lo schema degli ID (prefissi, numerazione, stabilità quando la spec cambia). Inclinazione al charting: (b), più leggera e coerente con lo stile del repo.
