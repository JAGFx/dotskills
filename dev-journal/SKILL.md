---
name: dev-journal
description: Run a dev through its dev journal, the single Markdown doc that carries a feature, a rework or a bug fix from the spec to the deployment (status, plan, open questions, spec, existing behavior, steps, decisions, tests, deployment, front contract). Use it whenever the user starts a new dev (pastes a spec, a ticket or an objective, « nouveau dev », « on démarre X »), opens or moves to a step (« on attaque la step X », « next » on a dev in progress), resumes a dev (« où on en est », « on reprend X »), prepares questions for the PO, the front or the team or brings back their answers, asks for the front contract, the deployment checklist or anything post-dev, and before editing any existing dev journal (a doc with a Status, a Plan and Open questions at its top). Not for an ADR, a functional reference sheet, a doc written for the PO, or an audit.
---

# Dev journal

A dev journal is the one doc that carries a dev from the spec to the deployment. The user and Claude keep it together, step by step. Claude rereads its head before every step, and a dev resumed after a break starts from it: it has to stay accurate, short at the top, and stable in its structure.

## Principles

- **Single source.** The state of a dev lives in its journal and in git, not in memory nor in the conversation. A memory about a dev only points to its journal and its branch, with « reread the head before each step »; no progress log in memory.
- **Step by step.** Answer the step at hand only, at the level of detail asked; propose the next one in a line, without unrolling it. A step opens with a brainstorm, never with code: « on attaque » is not « code ».
- **Validated, then written.** What is validated (a brainstorm, an answer, a decision) is written into the journal directly. Something that emerges in the conversation without being validated: propose the exact change and wait. The status and the tests after the dev are updated directly.
- **Factual first.** The framing (spec, existing behavior, questions) states facts; execution proposals come with the plan and the steps.
- **Own scope only.** Write about what the project owns. For an API: what it exposes or refuses (fields, HTTP codes, business rules). The UI (messages, buttons, screens, journeys) belongs to the front: out of the journal, the questions and the contracts. A front step of the spec leaves the plan; only the raw spec keeps it.
- **Spec and existing behavior prevail.** Mockups are indicative: read them for what the back must provide; a gap between a mockup and the spec or the existing behavior is not a question.

## File and language

- The journal: `docs/features/<domain>/<feature>.md`, or the project's own docs folder. The front contract next to it: `<feature>-front.md`.
- The journal is in English. The raw spec stays as is. Every question to the PO, the front or the team is in French, even in the table of an English journal. The front contract is in French.

## Skeleton

Start from `templates/journal.md`. The head (unnumbered) is what gets reread before every step; §1 to §10 are read by section when needed.

The numbering is fixed: a section with nothing to say keeps its heading with « — », never removed nor renumbered. Sections are cited (`§7 *Title*`) from the journal, the commits and the memory, so a renumbering breaks every reference.

The type of dev changes the content of some sections, never the structure:

| § | Feature | Rework | Bug fix |
|---|---|---|---|
| Plan | optional `Spec` column (steps or rules of the spec) | optional `Deploy` column when several deployments | `Size` column, an estimate line under the table |
| 1 Why | the goal of the spec | the goal and the problems observed | a concrete dated scenario, then the expected behavior |
| 3 Spec | raw, IDs added, coverage table | raw | *Origin*: the tickets that introduced the behavior |
| 4 Before | existing behavior | plus the reference measures | existing behavior |
| 5 Target | flows, architecture (Mermaid) | the target | usually « — » |
| 7 Decisions | — | the first one: existing behavior and API contract unchanged | — |
| 8 Tests | — | plus the verification protocol | — |
| 9 Deployment | what to hand over to the front | several deployments, rollback, breaking changes | data recovery |
| 10 Out of scope | — | plus the debt left as is | — |

### Head

- **Title and ticket**, the scope in one sentence, the branch (and its base), the links: front contract, ADR, previous phase. A `Branches:` line under the status when branches are stacked.
- **Status**: a dated blockquote, three lines at most: the last thing done, the next step, what blocks. No history: the plan and git carry it.
- **Plan**: columns `# | Step | Depends on | Status | Waiting for`. *Depends on* lists steps; *Waiting for* lists questions or people. Optional columns: `Spec`, `Size`, `Deploy`, `Branch` (several PRs).
  - A parent subject in bold, without status; its steps `N.M`; a step split again, `N.Ma`.
  - Statuses: `To do`, `Validated` (brainstorm validated, not coded), `Done`, `Removed (YYYY-MM-DD): <reason>`. A reworked step stays on its row: `Done (reworked YYYY-MM-DD)`.
  - Under the table, a *Logical order* paragraph: why this order, what can run in parallel, the deployments.
  - The plan reflects how the dev is organized, not the history of the back-and-forth: a step taken up again is merged into its original row (its history in one sentence at most, « first X, then moved… »), never a new sub-step.
- **Open questions**: pending only, `# | Prio | Question | To | Step`.
  - Priority: P1 changes the design or the scope, to ask first; P2 needed before its step; P3 a detail, may come during the dev; Later: a deferred subject, not to ask now.
  - Recipients: PO, Lead, Front, Team, Support.
  - Under a question, in italics: its impact and what is done meanwhile.
  - IDs are never reused nor renumbered.
  - Once answered, a question leaves the table and becomes a dated decision (§7). A question the existing behavior answers is never asked: it goes straight to §7, « (deduced from the existing) ».

### Sections

1. **Why**: see the table of types; cite its sources (§3).
2. **Glossary**: `Term | Meaning`, the term of the spec against the term of the code.
3. **Spec**: the raw spec, as is, with IDs added (R1… for the rules, AC1… for the acceptance criteria). For a feature, a coverage table `Spec | Step | Coverage` once the plan exists.
4. **Before**: the existing behavior, dated: what the code does today, its entry points, its readers.
5. **Target**: the overall picture when the subjects are intertwined (flows, architecture, Mermaid); « — » when the steps suffice.
6. **Steps**: per step, `### N.M <Title>`, then:
   - one line: what it covers, business-wise;
   - **Spec**: every passage of the spec touching it, quoted;
   - **Decided**: written once the brainstorm is validated;
   - **To settle**: « — » when nothing is left.
   No tests here (§8), no long why (§7).
7. **Decisions**: one `### <Title> (YYYY-MM-DD)` per decision, appended in chronological order, cited by its title. The body: what, why, `Not kept:` the alternative and why, `Cost:` when there is one. The source when it is not us: (PO), (Lead), (deduced from the existing). A revised decision keeps its entry: « first X, then Y (date) ».
   §6 *Decided* holds the detail of a step (names, columns, rules); §7 holds only the structuring or cross-step choices, with their alternatives. Do not write the same thing in both.
8. **Tests**: the non-regression written before the dev first; then a bullet per step (`- N.M: …`), Unit or Feature stated. A real check (HTTP calls, data dump) is described by its logic (scope, checks, pitfalls, reference results), the scripts stay local and unversioned.
9. **Deployment**: a `- [ ]` checklist under *Before*, *Deploy*, *After*, *Rollback*, *Later*. Filled as the steps close (W4), completed post-dev (W8).
10. **Out of scope**: the follow-ups, the debt left as is, the findings outside the feature (not fixed), what to report to the team.

### IDs, dates, references

- Stable IDs: R*, AC* (spec), Q* (questions); subjects `N`, steps `N.M`, sub-steps `N.Ma`. A new question takes the next free number.
- Brainstorm points, in the conversation only, prefixed by their step: `3.1-1`, `3.1-2`.
- Every status, answer, decision and measure is dated (`YYYY-MM-DD`).
- Refer with `§N *Title*` rather than repeating.

## Workflow

### W1. Framing a new dev

The user gives the spec, the goal and what else matters. Five beats, each validated before the next:

1. **The journal**: copy the template; head and status; §3 the raw spec, IDs added; §1 Why.
2. **The existing**: read the code the spec touches; §4 Before (dated), §2 Glossary.
3. **Spec against existing**: the open questions, P1 first; what the existing behavior answers goes to §7.
4. **The macro plan**: the subjects, their dependencies, the logical order, the deployments. Propose a split; the user adjusts it.
5. **The rough technical outline**: §5 Target when the subjects are intertwined, a cross-subject outline before detailing any of them; « — » otherwise.

Beats 1 to 3 stay factual: no execution proposal (data model, API, architecture) before the plan.

### W2. Opening a step (« on attaque X »)

1. **Reread** the head, the section of the step and the decisions it cites.
2. **Business recall**, in the conversation and as the first line of the step in §6: what is treated, business-wise (who, which journey, which rule, in the words of the spec), and where it sits in the plan (what is done before, what it unblocks).
3. **Spec recall**: every passage touching the step, not only the section bearing its name: the intro, the goal, details slipped into other sections, and the connectors (« Donc… ») that tie one requirement to another. Discard any justification that does not come from the spec.
4. **Brainstorm**: numbered points (`3.1-1`…), each with the choice, the alternatives and a recommendation; the tests considered; the API contract as JSON examples when the step touches it; the open points, which become questions.
5. **Fine split**, at every step:
   - sub-steps, each a commit green on its own, in a logical order, with a rough count of the files touched (tests included);
   - PRs of one or more sub-steps, around 50 files: an order of magnitude to keep in mind, hard to estimate, not a rule;
   - beyond it, split further, or stack the branches (one per PR, each from the previous one);
   - the sub-steps added to the plan (`Branch` column when several PRs).
6. **Explicit validation**: §6 *Decided*, §7 for the structuring choices, status `Validated`. The dev starts on the user's « go », often in the same message.

### W3. During the dev

- A decision taken on the way goes to §6 or §7, dated.
- A gap with the spec becomes a question.
- A finding outside the scope goes to §10, not fixed.
- A step taken up again is merged into its row.
- The journal moves with the code and goes in the same commit.

### W4. Closing a sub-step

1. The tests of the step green, the formatter run; §8 updated.
2. API contract touched: generate and check the API types at this sub-step, not at the end (the project's type generation); every endpoint touched titled and described in the API doc; the front contract updated; a change after the contract was handed over to the front signaled in *Waiting for*.
3. Its deployment items added to §9: migration, one-time operation, env variable, cron, command to run.
4. The user's FIXME review, when asked.
5. The plan at `Done`, the status rewritten.
6. Commit only when the user asks, the journal included.
7. The next step proposed in a line.

### W5. Resuming (« où on en est »)

Read the head and the git state (branch, what is not committed), nothing more unless the next step needs it. Answer in three points: last done, next step, blocking questions.

### W6. Questions and answers

A question for the PO, the front or the team:

- understandable without technical context, short, in French, in the words of the spec: no column, constant or class names;
- one line of context if needed, the question, the options (a / b / c) with the proposal marked, a concrete consequence when it helps choosing;
- nothing about the UI; nothing already settled or obvious; nothing the existing behavior answers (align on it, its gaps go to §10 as a short note). What follows from the problem the feature fixes is obvious, but if it also needs front work, ask whether to include it.

« Prépare les questions pour X »: extract from the table the open questions addressed to X.

An answer received: a dated decision (§7), the question out of the table, the steps it unblocks updated (*Waiting for*), the status rewritten.

### W7. End of the dev

- Real checks: HTTP calls, data dump.
- The full test suite.
- A simulated merge on the base branch, for the conflicts.
- A global review, limited to the feature: a preexisting gap on something the feature does not touch is not reported, not even as out of scope.

### W8. Post-dev

1. **Front contract** (`templates/front-contract.md`, in French). Started as soon as the contract is validated (W2), finalized here. The API doc stays the reference of the details; the front contract holds what the front needs to start its dev and the API doc does not say: what changed compared to before, the sequence of calls, the business rules that reach the front (when a field is `null`, when a 409 or a 422 comes back), the breaking changes and deprecations, what is expected from the front. Never the types nor the required flags (the type generation gives them), the full lists of fields (the API doc) nor any UI. Organized as *Ajouté* / *Modifié* / *Supprimé* / *Déprécié*, then *Attendu du front*; « — » for an empty section, so the front sees it was considered. Several consuming fronts (public, admin, pro): one file, a `##` per front.
2. **API doc**: every endpoint of the feature titled and described.
3. **Docs for other readers**, on request only: for the PO, only the content asked, exhaustive and readable (no generic sections); for a provider or a partner.
4. **§9 Deployment** reread and completed:
   - *Before*: what to hand over to the front and the team, the questions marked « before the deployment » answered, the merge order with other branches, the env variables and the config;
   - *Deploy*: the order of the migrations and one-time operations, the heavy migrations (locks, maintenance mode);
   - *After*: the data recovery commands, the checks and measures in production, the logs and failed jobs to watch;
   - *Rollback*: the way back;
   - *Later*: the cleanup (one-time operations removed, deprecated code) and a second deployment if any.
5. **Status**: `Done (YYYY-MM-DD)`, then `Deployed (YYYY-MM-DD)`. The journal then stays as is, as history; a reference doc only on request.

On request only:

- messages ready to send to the team, the PO or the front;
- the list of follow-ups to turn into tickets, from §10 and the open points: a title, a line of context, the `§` where it is noted.

## Boundaries

- No architecture nor code rule in the journal process: they live in the project's instructions.
- Commits follow the project's rules; the journal goes in the commit of its step.
- A structuring decision can become an ADR on request (the `adr` skill), linked from the head.
- Past journals are not realigned to this skeleton: case by case. When a rename touches classes cited by a previous phase's doc or by ADRs, leave them as they are; only the current journal follows.
