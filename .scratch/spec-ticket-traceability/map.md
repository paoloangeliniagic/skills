# Map: tracciabilità da spec a ticket (user stories, seam, vincoli ADR)

Label: wayfinder:map

## Destination

Una spec, pronta per `/to-tickets`, delle modifiche a `to-spec` e `to-tickets` in questo fork. Con quelle modifiche user stories, seam e vincoli ADR arrivano in modo esplicito e verificabile in ogni ticket, così `implement`/`tdd` li consumano senza dover rileggere la spec.

## Notes

- Dominio: skill di questo repo (`skills/engineering/to-spec`, `skills/engineering/to-tickets`). `implement` e `tdd` sono solo consumatori da verificare, non da riprogettare.
- Fonte del problema: report di ricerca "User story traceability from spec to tickets" (topic pablito/research#24). Issue upstream collegate: mattpocock/skills#1078 (seam persi), mattpocock/skills#613 (ADR e parent spec), mattpocock/skills#924 e mattpocock/skills#959 (impegni orfani), mattpocock/skills#328 (user stories nei ticket).
- Già concordato durante il charting:
  - I seam non tengono conto delle stories per due motivi: **ordine** (in `to-spec` i seam si abbozzano al passo 2, prima delle stories) e **tracciabilità** (nessun artefatto dice quale story è verificata a quale seam). Vanno corretti entrambi.
  - Nei ticket i vincoli ADR vanno **riformulati in una riga** applicabile, più il link all'ADR (l'ADR resta l'unica fonte di verità).
  - Il controllo di copertura in `to-tickets` **segnala** nel quiz e non blocca: l'utente decide se coprire, dichiarare fuori scope o accettare.
- Skill da consultare: "grilling" + "domain-modeling" per i ticket di grilling; "codebase-design" per il vocabolario dei seam; `CONTEXT.md` per i termini (Issue, Decision ticket).
- Regole del repo da rispettare nella spec finale: niente em-dash; docs page in `docs/engineering/` da risincronizzare (`.agents/writing-docs.md`); aggiornare `ask-matt` se cambia il flusso; changeset.
- Tracker: markdown locale (issue GitHub disabilitate su questo repo).

## Decisions so far

<!-- one line per closed ticket -->

## Not yet specified

- **Lato consumatori**: come `implement` e `tdd` leggono i nuovi campi del ticket (per es. "test only at pre-agreed seams" deve puntare ai seam del ticket). Si chiarisce solo quando forma e nomi dei campi sono decisi.
- **Parità tra i template**: template locale e template issue di `to-tickets` divergono già. Da capire se i nuovi campi vanno in entrambi in forma identica e come si esprimono le stories/seam nella variante locale.
- **Spec senza user stories**: i FAQ di `to-spec` riconoscono che per refactor e confini di modulo le stories sono la forma sbagliata. Cosa traccia la copertura in quel caso (decisioni di implementazione? invarianti?) dipende dal meccanismo scelto.
- **Documentazione e router**: impatto su `docs/engineering/to-spec.md`, `docs/engineering/to-tickets.md`, `ask-matt` e changeset. Diventa specificabile quando le modifiche alle skill sono decise.

## Out of scope

- Proporre le modifiche upstream a mattpocock/skills: possibile passo successivo, separato da questa mappa.
- `wayfinder` e i suoi ticket di decisione (anche se non riportano gli ADR): fuori dal perimetro scelto.
- Misure di successo a livello di outcome (mattpocock/skills#721) e separazione tra autore del lavoro e autore dei controlli (mattpocock/skills#791): problemi vicini ma diversi da questa destinazione.
