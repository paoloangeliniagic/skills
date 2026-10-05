# Confronto delle patch di tracciabilità già esistenti

Ricerca per il ticket [paoloangeliniagic/skills#2](https://github.com/paoloangeliniagic/skills/issues/2), figlio della mappa [#1](https://github.com/paoloangeliniagic/skills/issues/1). Alimenta la decisione di [#3](https://github.com/paoloangeliniagic/skills/issues/3) (registro generale o campi mirati), che resta aperta: questo documento riporta fatti, non sceglie.

Fonti consultate il 2026-10-06. I link a codice esterno puntano a commit SHA fissi.

## TL;DR

- Nessuna delle soluzioni esistenti copre insieme user stories, seam e vincoli ADR. Ognuna ne prende uno (o una categoria più generica): sridhar copre gli ADR, #1078 copre i seam, SQLab e Spec Kit coprono le stories, Seekers e #959 coprono "decisioni" o "impegni" generici.
- Gli **ID stabili** esistono già in tre forme: numerazione delle stories della spec ("User story 3", SQLab e il vecchio `prd-to-issues` upstream), etichette `[US1]` derivate dai titoli delle stories (Spec Kit), ID di registro `D-001` (Seekers v1). Nessuna soluzione assegna ID ai seam.
- Il **controllo di copertura** compare in Seekers v1 (regola più domanda nel quiz), #959 (quiz "Spec commitments carried"), Spec Kit (`/analyze`, report di copertura separato e non distruttivo) e #818 (audit senza campi nuovi). sridhar, Seekers v2, SQLab e #1078 non ne hanno.
- **Riferire o copiare**: sridhar, Seekers v1 e v2 e #613 sono solo riferimento (link a monte, niente duplicazione); #959 copia gli impegni nel ticket; #1078 vuole il seam scritto nel ticket perché `implement` legge solo il ticket.
- Upstream aveva già un campo mirato per le stories: `## User stories addressed` nel template e "User stories covered" nel quiz di `prd-to-issues`. Il campo del template è sparito il 2026-04-17, la riga del quiz il 2026-07-02, entrambi in commit di rinomina o unificazione che non motivano la rimozione.
- Nessuna patch è stata integrata upstream. Più autori riportano che mattpocock/skills non accetta PR da account esterni (#341, #818, #959).

## Baseline: cosa perde oggi il passaggio spec → ticket

- `to-spec` abbozza i seam al passo 2 e li conferma con l'utente, prima di scrivere la spec; il template chiede poi "A LONG, numbered list of user stories" e una sezione `Testing Decisions` ([skills/engineering/to-spec/SKILL.md](../skills/engineering/to-spec/SKILL.md), righe 13-17 e 31-41). Gli ADR vanno "rispettati" ma non citati: la docs page lo dice esplicitamente ("It reads and respects the ADRs covering the area it touches, but it doesn't link them", [docs/engineering/to-spec.md](../docs/engineering/to-spec.md), righe 59-60).
- `to-tickets` ha due template: locale (`What to build`, `Blocked by`, `Status`, criteri) e issue (`Parent`, `What to build`, `Acceptance criteria`, `Blocked by`). Nessuno ha campi per stories, seam o ADR ([skills/engineering/to-tickets/SKILL.md](../skills/engineering/to-tickets/SKILL.md), righe 69-103).
- `implement` dice "Use /tdd where possible, at pre-agreed seams" e `tdd` non scrive test a seam non confermati ([implement](../skills/engineering/implement/SKILL.md) riga 9, [tdd](../skills/engineering/tdd/SKILL.md) riga 22).

### Storia upstream del campo stories

- [`8e51ff7`](https://github.com/mattpocock/skills/commit/8e51ff765eb242b65324e9b770526cc53e9db767) (2026-02-20): `prd-to-issues` nasce con "**User stories covered**: which user stories from the PRD this addresses" nel quiz e una sezione `## User stories addressed` ("Reference by number from the parent PRD: User story 3, User story 7") nel template issue.
- [`aaf3050`](https://github.com/mattpocock/skills/commit/aaf3050857a8d00c710c382c87a29b81341370aa) (2026-04-17, "Updated write-a-prd to to-prd"): rinomina in `to-issues`, generalizza la sorgente da PRD a "plan", **rimuove** `## User stories addressed` dal template e rende la riga del quiz condizionale "(if the source material has them)".
- [`386d4ff`](https://github.com/mattpocock/skills/commit/386d4ff719a7c420ad1454232d0436b01f1b8c17) (2026-07-02, "refactor: unify planning skills into /to-spec + /to-tickets"): unifica `to-plan` e `to-issues` in `to-tickets` e rimuove anche la riga "User stories covered" dal quiz.

Nessuno dei due messaggi di commit spiega perché il campo è stato tolto; la rimozione coincide con la generalizzazione a sorgenti senza stories.

## Soluzioni analizzate

### 1. sridhar-3009/skills, commit 2922bf2 (ADR nella spec, parent spec nel ticket)

Fonte: [commit `2922bf2`](https://github.com/sridhar-3009/skills/commit/2922bf2e0ff9be831d5127aa04748d16208b1b68) (2026-07-20), risposta a [mattpocock/skills#613](https://github.com/mattpocock/skills/issues/613) ([commento](https://github.com/mattpocock/skills/issues/613#issuecomment-5021902569)).

- **Dove vivono gli ID**: non introduce ID propri. Riusa il numero dell'ADR (`ADR-NNN title`) e il path o riferimento issue della spec.
- **Spec**: sezione opzionale `## Governing Decisions` in fondo al template: elenco di ADR "that materially constrain this spec, with a one-line note on what each one rules in or out". Solo ADR "load-bearing", non quelli di contorno; omessa se non ci sono ADR.
- **Ticket**: campo `**Parent spec:**` solo nel template locale (il template issue ha già `## Parent`). Nessun riferimento ad ADR nel ticket.
- **Copertura**: nessun controllo. Il commit lo dichiara: "reference-only, no reverse links, no sync requirement".
- **Peso**: +6 righe in `to-spec`, +2 in `to-tickets`; una sezione opzionale e un campo opzionale.
- **Seam**: non trattati.
- **ADR**: è l'unica soluzione con una forma esplicita per gli ADR: link più una riga su cosa l'ADR ammette o esclude. Però la riga vive solo nella spec; il ticket ci arriva solo indirettamente, tramite il puntatore alla spec.

### 2. Seekers2001/skills, branch `codex/decision-traceability` (v1, decision ledger)

Fonte: [commit `395ff0b`](https://github.com/Seekers2001/skills/commit/395ff0b461d5e73d8aa860b0b364a6773cc6fd83) (2026-08-02), presentato in [#341](https://github.com/mattpocock/skills/issues/341#issuecomment-5157604562). Costruisce sulla proposta di ledger di [tuterx](https://github.com/mattpocock/skills/issues/341#issuecomment-4764096466), che Matt Pocock aveva commentato così: "Tagging answers by a specific tag and linking them through the whole process gives the agent a leitwort to trace throughout the entire process. Love it." ([commento](https://github.com/mattpocock/skills/issues/341#issuecomment-4727749141)).

- **Dove vivono gli ID**: in un **decision ledger** per effort, scritto da `grill-with-docs`: un'issue sul tracker reale oppure `.scratch/<feature-slug>/decisions.md`. ID locali `D-001`, `D-002`; ogni record ha nome, domanda e risposta, requisito normalizzato e testabile, vincoli e requisiti negativi, stato (`current`, `revised`, `deferred`, `superseded`), puntatore alla fonte. I ticket Wayfinder risolti valgono come fonti con la loro identità sul tracker. Due termini nuovi in `CONTEXT.md`: **Decision ledger** e **Source decision**.
- **Spec**: nuova sezione `## Source Decisions` (dopo Solution) che deve rendere conto di **ogni** decisione `current`: per ciascuna, link al record più "covered by <parte della spec>" oppure "N/A: <motivo>". Regola: "Do not silently omit, weaken, or renumber a current decision".
- **Ticket**: template locale con `**Parent spec:**` e `**Source decisions:**`; template issue con `## Source decisions`. Solo link alle fonti canoniche, niente copia, niente link inversi.
- **Copertura**: sì. In `to-tickets` passo 3: "every current source decision must be covered by at least one ticket or explicitly marked N/A in the parent spec"; nel quiz una domanda in più: "Does each source decision belong to the right ticket, with no current decision missing from the complete set?". A valle, `implement` carica parent spec e fonti e si ferma se mancano o confliggono; l'asse Spec di `code-review` segnala fonti "missing, weakened, stale, or contradicted".
- **Peso**: il più alto tra le patch analizzate. 16 file: `to-spec` +19, `to-tickets` +19/-1, `grill-with-docs` +24, `code-review` +13/-6, `implement` +8/-1, `wayfinder` +8, `CONTEXT.md` +10, 7 docs page, changeset. Introduce un artefatto nuovo (il ledger) e un passo di scrittura in `grill-with-docs`.
- **Seam**: non trattati come categoria. Un seam entra solo se qualcuno lo registra come decisione nel ledger.
- **ADR**: tenuti fuori di proposito ("stays separate from #613's ADR links"); il ledger dichiara che `CONTEXT.md` resta glossario e che una decisione diventa ADR solo alla soglia di `domain-modeling`.

### 3. Seekers2001/skills, branch `codex/decision-traceability-v2` (puntatore "Delivery")

Fonte: [commit `41e8e6e`](https://github.com/Seekers2001/skills/commit/41e8e6ec5b9d627da0865e0bc293758a9e86a1c4) (2026-08-09), presentato in [#341](https://github.com/mattpocock/skills/issues/341#issuecomment-5229893473) come versione "narrowed to the artifact-lineage slice".

- **Dove vivono gli ID**: rinuncia esplicitamente a una numerazione globale e al ledger. Restano le identità del tracker (mappa, decision ticket, spec, issue) e i `NN` dei ticket locali.
- **Spec**: nuova sezione `## Source map` (link alla mappa Wayfinder, omessa altrimenti). In locale la spec va in `.scratch/<effort>/spec.md`, nella stessa cartella della mappa.
- **Mappa Wayfinder**: nuova sezione `## Delivery`, riempita da `to-spec` con il link alla spec. Navigazione nei due sensi senza duplicare contenuto.
- **Ticket**: `**Parent spec:**` nel template locale; `to-tickets` deve conservare il riferimento alla spec come parent di ogni ticket e riusare la cartella dell'effort.
- **Copertura**: nessun controllo.
- **Peso**: basso sui template: `to-tickets` +4/-2, `to-spec` +16 (in gran parte istruzioni di passo), `wayfinder` +18; 9 file in totale.
- **Seam** e **ADR**: non trattati. Risolve la navigazione tra artefatti, non il contenuto del ticket.

### 4. mattpocock/skills#959 (source ledger degli impegni della spec)

Fonte: [issue #959](https://github.com/mattpocock/skills/issues/959) (2026-08-26). La patch esiste solo come descrizione: l'autore scrive che il branch "could not be pushed to `mattpocock/skills` because GitHub denied write permission"; non c'è un fork pubblico `mingjitianming/skills` né un compare linkato (verificato via API: repo inesistente; i soli riferimenti incrociati sono #976 e le issue di questo fork).

- **Dove vivono gli ID**: non specificato. Il "source ledger" viene estratto da `to-tickets` prima dello slicing, non salvato nella spec, e la proposta non menziona ID.
- **Spec**: nessun campo nuovo.
- **Ticket**: sezione `Spec commitments` in entrambi i template. Gli impegni vengono **copiati**: "carry every applicable commitment into each generated ticket, repeating shared commitments where needed instead of relying on the parent issue". In `ask-matt` "self-contained" viene definito come "carrying the spec commitments needed for their slice, not just a parent link".
- **Categorie del ledger**: behaviours, invariants, architecture choices, non-goals, compatibility requirements, UX/content decisions, security/privacy constraints, performance expectations, validation evidence, rollout constraints, definitions.
- **Copertura**: sì, nel quiz: mostrare "Spec commitments carried" e chiedere se un impegno è sparito, si è indebolito o è finito nel ticket sbagliato. In `implement`, un "input audit" che confronta ticket e spec e si ferma sui conflitti.
- **Peso**: `ask-matt`, `to-tickets`, `implement` più changeset (righe non verificabili, la patch non è pubblica). Il peso maggiore è nel ticket stesso, che cresce con ogni impegno ripetuto.
- **Seam**: non nominati; il più vicino è "validation evidence".
- **ADR**: non nominati; ricadono in "architecture choices" o "security/privacy constraints", senza link all'ADR.

Correlato: [#924](https://github.com/mattpocock/skills/issues/924) è il caso reale (PRD con circa 85 user stories, 16 ticket diventati 27, un invariante critico rimasto senza ticket) che motiva il controllo di copertura, senza proporre un formato.

### 5. mattpocock/skills#1078 (campo seam nei ticket)

Fonte: [issue #1078](https://github.com/mattpocock/skills/issues/1078) (2026-09-13). Evidenza: su 1125 sessioni di un utente, 17 run di `/implement` e 0 invocazioni di `/tdd`; dove a `implement` arrivava il corpo di una spec i seam c'erano, dove arrivava un ticket no.

- **Dove vivono gli ID**: nessun ID.
- **Proposte** (dichiarate "not prescriptive"): (1) sezione `## Seams` in entrambi i template di `to-tickets`, riempita dai `Testing Decisions` della spec; (2) `to-tickets` rifiuta di pubblicare un ticket derivato da una spec senza seam, come `tdd` rifiuta un seam non confermato; (3) in alternativa `implement` legge la spec parent tramite il campo `Parent`.
- **Implementazione**: l'autore l'ha messa in pratica nel proprio repo come istruzione globale, non come modifica al template: [fabiandistler/agents#158](https://github.com/fabiandistler/agents/pull/158) aggiunge un ADR-0004 e le regole "When breaking a spec into tickets, carry the seams into every ticket. Add a `## Seams` section naming the boundary each ticket is tested at, and state explicitly what is deliberately not a seam" e "A ticket with no seam field is not ready for implementation". La PR è stata chiusa senza merge il 2026-09-13 e l'issue collegata [#157](https://github.com/fabiandistler/agents/issues/157) chiusa come `not_planned` il 2026-09-28; quell'ADR non è presente nel `docs/adr` attuale del repo.
- **Copertura**: nessun controllo di copertura delle stories; c'è invece un **gate** (ticket senza seam non pronto), cioè blocca invece di segnalare.
- **Peso**: una sezione per template.
- **Seam**: è l'unica proposta dedicata ai seam. Include anche ciò che "deliberately is not a seam". Domanda aperta nel thread: "was an explicit Seams field enough, or did you also need traceability back to the parent spec to detect later drift?" ([commento](https://github.com/mattpocock/skills/issues/1078#issuecomment-5760083364)), senza risposta.
- **ADR**: non trattati.

### 6. flh-raouf/SQLab, `.agents/skills/prd-to-issues/SKILL.md` (`## User stories addressed`)

Fonte: [SKILL.md @ `1089f19`](https://github.com/flh-raouf/SQLab/blob/1089f19cd9be3a5f79d3699a4a899abf835140fe/.agents/skills/prd-to-issues/SKILL.md) (2026-06-04). È un adattamento del `prd-to-issues` originale di upstream (vedi la storia sopra): la sezione `## User stories addressed` è identica riga per riga a quella di `8e51ff7`.

- **Dove vivono gli ID**: nella numerazione delle user stories del PRD. Il ticket le cita per numero ("User story 3").
- **Spec**: nessun campo nuovo; basta che le stories siano numerate.
- **Ticket**: `## Parent PRD`, `## User stories addressed`, più `## Related backend issue` e `## Notes / Implementation discussion` specifici del progetto. "What to build" dice "Reference specific sections of the parent PRD rather than duplicating content".
- **Copertura**: nessuna. Rispetto all'originale upstream, il quiz con "User stories covered" è stato sostituito da una discussione per issue.
- **Peso**: una sezione di 3-5 righe nel ticket.
- **Seam**: no; i test sono "recommended but not required".
- **ADR**: no.

### 7. GitHub Spec Kit (formato `[Story]`)

Fonti, tutte @ [`2dda047`](https://github.com/github/spec-kit/tree/2dda047809dd17fa56200408ce0228a2cfe08be7): [`templates/tasks-template.md`](https://github.com/github/spec-kit/blob/2dda047809dd17fa56200408ce0228a2cfe08be7/templates/tasks-template.md), [`templates/spec-template.md`](https://github.com/github/spec-kit/blob/2dda047809dd17fa56200408ce0228a2cfe08be7/templates/spec-template.md), [`templates/plan-template.md`](https://github.com/github/spec-kit/blob/2dda047809dd17fa56200408ce0228a2cfe08be7/templates/plan-template.md), [`templates/commands/tasks.md`](https://github.com/github/spec-kit/blob/2dda047809dd17fa56200408ce0228a2cfe08be7/templates/commands/tasks.md), [`templates/commands/analyze.md`](https://github.com/github/spec-kit/blob/2dda047809dd17fa56200408ce0228a2cfe08be7/templates/commands/analyze.md), [`templates/commands/taskstoissues.md`](https://github.com/github/spec-kit/blob/2dda047809dd17fa56200408ce0228a2cfe08be7/templates/commands/taskstoissues.md).

- **Dove vivono gli ID**: nella spec. Ogni story è un titolo `### User Story N - [Title] (Priority: Pn)` con "Why this priority", "**Independent Test**" e scenari Given/When/Then (spec-template righe 26-37). Ci sono anche ID per requisiti (`FR-001`) e criteri di successo (`SC-001`) (righe 90-118). Nei task l'etichetta `[US1]` "maps to user stories from spec.md"; i task hanno ID `T001`.
- **Spec**: struttura per story, già con un criterio di test indipendente per ciascuna.
- **Ticket (task)**: formato `- [ ] [TaskID] [P?] [Story?] Description` (tasks-template righe 16-19; commands/tasks righe 149-164). L'etichetta è obbligatoria solo nelle fasi per story, assente in setup, foundational e polish. Le fasi sono raggruppate per story con "Goal" e "Independent Test".
- **Copertura**: sì, ma in un comando separato e non distruttivo, `/analyze`: inventario delle stories, mappa task → requisiti o stories "by keyword / explicit reference patterns like IDs", categoria "Coverage Gaps" (requisiti senza task, task senza requisito), tabella "Requirement Key | Has Task? | Task IDs", metrica "Coverage %" (analyze righe 111-113, 140-143, 174-187).
- **Peso**: un token per riga di task, quindi minimo per elemento; ma il framework complessivo (spec, plan, tasks, analyze, constitution) è molto più pesante del flusso di questo repo.
- **Seam**: nessun concetto di seam. Il più vicino è l'"Independent Test" per story e la cartella `contracts/` con contract test; i test sono opzionali.
- **ADR**: non ci sono ADR; il loro posto lo prende la **constitution**, con un gate "Constitution Check" nel plan ("Must pass before Phase 0 research", plan-template righe 39-43) e, in `/analyze`, conflitti con la constitution "automatically CRITICAL" (riga 60).
- **Nota sul confine verso le issue**: `/taskstoissues` crea un'issue per task e prima "strip the leading `- [ ]` (and any `[P]` / `[US#]` markers)", con titolo `T001: <description>` (riga 68); il comando non prescrive di riportare la story nel corpo. Anche in Spec Kit, cioè, l'etichetta di story si perde proprio quando il task diventa issue.

### Riferimento aggiuntivo: mattpocock/skills#818 (audit di batch senza campi)

Fonte: [issue #818](https://github.com/mattpocock/skills/issues/818), patch [FlapPearLabs `2670a59`](https://github.com/FlapPearLabs/skills/commit/2670a59a944e36d855a80994f23dd87fae9c531f). Aggiunge a `to-tickets` un passo "Validate the batch" prima del quiz, con un "Source-contract audit" in due direzioni ("every normative source requirement has an explicit disposition", "every ticket requirement traces back to the source") e "Fix, don't publish". Dichiara "no new ticket fields, no coverage spreadsheet": copertura senza ID e senza campi, quindi non verificabile a posteriori leggendo il ticket.

## Tabella comparativa

| Soluzione | Dove vivono gli ID | Aggiunge alla spec | Aggiunge al ticket | Controllo di copertura | Peso sui template | Seam | Vincoli ADR |
| --- | --- | --- | --- | --- | --- | --- | --- |
| sridhar `2922bf2` | Numero ADR, ref della spec | `Governing Decisions` (opz.) | `Parent spec` (solo locale) | No | Molto basso (+6/+2 righe) | No | Sì, link + 1 riga, ma solo nella spec |
| Seekers v1 `395ff0b` | Ledger `D-001` (issue o `.scratch/.../decisions.md`), ID tracker per ticket Wayfinder | `Source Decisions` (ogni decisione: covered by / N/A) | `Parent spec` + `Source decisions` (link) | Sì: regola + domanda nel quiz; poi `implement` e `code-review` | Alto (16 file, nuovo artefatto) | No | Esclusi di proposito |
| Seekers v2 `41e8e6e` | Solo ID tracker e `NN` locali | `Source map` | `Parent spec` (locale) | No | Basso | No | No |
| #959 source ledger | Non specificato (ledger estratto in `to-tickets`) | Nessuno | `Spec commitments` (copia) | Sì: quiz "Spec commitments carried" + audit in `implement` | Medio sui template, alto sul singolo ticket | Solo come "validation evidence" | Solo come "architecture choices" |
| #1078 / fabiandistler#158 | Nessuno | Nessuno | `## Seams` (incluso cosa non è seam) | No; gate bloccante sul seam | Basso | Sì, unica dedicata | No |
| SQLab / `prd-to-issues` originale | Numero della story nella spec | Nessuno (stories già numerate) | `User stories addressed` | No (upstream: stories mostrate nel quiz) | Molto basso | No | No |
| Spec Kit | `User Story N` nella spec, `[USn]` nei task, `FR-`, `SC-`, `T` | Story con priorità e Independent Test | Etichetta `[USn]` per riga | Sì: `/analyze`, report separato, non bloccante | Basso per task, alto per il framework | No (Independent Test per story) | Constitution come gate, non ADR |
| #818 audit | Nessuno | Nessuno | Nessuno | Sì, solo al momento della generazione | Nessuno sui template | No | Solo "hard constraints" generici |

## Implicazioni per la scelta tra (a) registro generale e (b) campi mirati

Osservazioni fattuali, non una raccomandazione; la decisione spetta a [#3](https://github.com/paoloangeliniagic/skills/issues/3).

**(a) Registro generale degli impegni nella spec** (modello Seekers v1, #959, proposta di #341)

- Copre tipi eterogenei (invarianti, non-goal, vincoli) e quindi anche spec senza user stories: rilevante per la nebbia "Spec senza user stories" della mappa e per la FAQ di `to-spec` che riconosce le stories come forma sbagliata per refactor e confini di modulo ([docs/engineering/to-spec.md](../docs/engineering/to-spec.md), riga 57).
- Nessuna delle due implementazioni nomina i seam come categoria, e una tiene fuori gli ADR di proposito. Coprire seam e ADR richiederebbe di aggiungerli come tipi di voce del registro.
- Costo documentato: Seekers v1 tocca 16 file e introduce un artefatto e due termini di glossario; il suo stesso autore ha poi ristretto la proposta (v2) alla sola navigazione tra artefatti.
- Le due varianti divergono su riferire o copiare: Seekers riferisce (il ticket non è autosufficiente), #959 copia (il ticket cresce). L'evidenza di #1078 indica che `implement` usa ciò che sta nel ticket, non ciò che sta dietro un link.

**(b) Campi mirati con ID stabili** (`Stories covered`, `Seams`, `Constraints (ADR)`; modello SQLab, Spec Kit, #1078, sridhar)

- Ogni campo ha già un precedente concreto: stories per numero (upstream originale, SQLab), seam come sezione (#1078), ADR come link + riga (sridhar, #613). Nessuna soluzione esistente li combina.
- Gli ID per le stories sono quasi gratuiti, perché `to-spec` già numera le stories. Per i seam non esiste un precedente di ID (`S1`): in tutte le soluzioni il seam è prosa o una sezione senza identificatore, e la domanda di [#1078](https://github.com/mattpocock/skills/issues/1078#issuecomment-5760083364) sulla deriva rispetto alla spec resta senza risposta.
- Upstream ha già avuto e rimosso il campo stories durante la generalizzazione a sorgenti senza stories; un campo mirato deve quindi dire cosa succede quando la spec non ha stories.
- Il controllo di copertura per ID è dimostrato da Spec Kit (`/analyze`, report non bloccante) e dalla domanda di quiz di Seekers v1; entrambi compatibili con la regola già concordata nella mappa ("segnala nel quiz e non blocca"). Il gate di #1078 e fabiandistler va invece nel senso opposto (blocca).
- Il caso `/taskstoissues` di Spec Kit mostra che un ID può sparire proprio al confine task → issue se il template di destinazione non ha un posto per lui.

**Punti trasversali**

- Nessuna soluzione affronta il problema di **ordine** segnalato nella mappa (seam abbozzati al passo 2 di `to-spec`, prima delle stories). Spec Kit è l'unico caso in cui le stories, ognuna con il proprio test indipendente, precedono il piano tecnico.
- Il formato ADR concordato nella mappa (riga applicabile + link) corrisponde a quello di sridhar, ma sridhar lo mette nella spec, non nel ticket.
- La **parità tra template locale e issue** è trattata in modo diseguale: sridhar e Seekers v2 aggiungono `Parent spec` solo al template locale, perché quello issue ha già `Parent`; Seekers v1, #959 e #1078 aggiungono il campo a entrambi.

## Fonti

- mattpocock/skills issues: [#341](https://github.com/mattpocock/skills/issues/341), [#328](https://github.com/mattpocock/skills/issues/328), [#613](https://github.com/mattpocock/skills/issues/613), [#818](https://github.com/mattpocock/skills/issues/818), [#924](https://github.com/mattpocock/skills/issues/924), [#959](https://github.com/mattpocock/skills/issues/959), [#976](https://github.com/mattpocock/skills/issues/976), [#1078](https://github.com/mattpocock/skills/issues/1078).
- Commit upstream: [`8e51ff7`](https://github.com/mattpocock/skills/commit/8e51ff765eb242b65324e9b770526cc53e9db767), [`aaf3050`](https://github.com/mattpocock/skills/commit/aaf3050857a8d00c710c382c87a29b81341370aa), [`386d4ff`](https://github.com/mattpocock/skills/commit/386d4ff719a7c420ad1454232d0436b01f1b8c17).
- Patch nei fork: [sridhar-3009 `2922bf2`](https://github.com/sridhar-3009/skills/commit/2922bf2e0ff9be831d5127aa04748d16208b1b68), [Seekers2001 `395ff0b`](https://github.com/Seekers2001/skills/commit/395ff0b461d5e73d8aa860b0b364a6773cc6fd83) (branch [`codex/decision-traceability`](https://github.com/Seekers2001/skills/tree/codex/decision-traceability)), [Seekers2001 `41e8e6e`](https://github.com/Seekers2001/skills/commit/41e8e6ec5b9d627da0865e0bc293758a9e86a1c4) (branch [`codex/decision-traceability-v2`](https://github.com/Seekers2001/skills/tree/codex/decision-traceability-v2)), [FlapPearLabs `2670a59`](https://github.com/FlapPearLabs/skills/commit/2670a59a944e36d855a80994f23dd87fae9c531f), [fabiandistler/agents#158](https://github.com/fabiandistler/agents/pull/158).
- [flh-raouf/SQLab `prd-to-issues`](https://github.com/flh-raouf/SQLab/blob/1089f19cd9be3a5f79d3699a4a899abf835140fe/.agents/skills/prd-to-issues/SKILL.md).
- [github/spec-kit @ `2dda047`](https://github.com/github/spec-kit/tree/2dda047809dd17fa56200408ce0228a2cfe08be7/templates).
- Questo repo: `skills/engineering/to-spec/SKILL.md`, `skills/engineering/to-tickets/SKILL.md`, `skills/engineering/implement/SKILL.md`, `skills/engineering/tdd/SKILL.md`, `docs/engineering/to-spec.md`.
