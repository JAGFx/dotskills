---
name: adr
description: Write or update an Architecture Decision Record (ADR), in French. Use whenever the user mentions an ADR or asks to document, record or trace an architecture or technical decision (e.g. « écris-moi un ADR pour XXX », « fais un ADR », « documente cette décision », « trace ce choix d'archi »), to create a new ADR, change an ADR's status or supersede an existing one. Also use before editing any existing ADR file.
---

# ADR (Architecture Decision Records)

An **Architecture Decision Record (ADR)** is a short, immutable document that captures an important architecture decision: its context, the options considered, and the rationale behind the chosen option.

## Why ADRs?

- Trace the history of technical decisions
- Ease onboarding of new team members
- Avoid rehashing the same debates
- Document rejected alternatives (and why)

## Language

ADRs are written in **French**: headings, content and status labels. Code identifiers stay as they are.

## Naming

File name: `NNN_short_title.md` — sequential numbering, snake_case title.

## ADR sections

Every ADR must include these sections in order — use the exact French headings:

1. **Title** — `# NNN. Titre de la décision`
2. **Metadata** — `Date`, `Statut`, `Décideurs`
3. **`## Contexte et problème`** — 2-3 sentences describing the situation
4. **`## Critères de décision`** — bulleted list of evaluation criteria
5. **`## Options envisagées`** — list all options, even obvious ones
6. **`## Décision retenue`** — which option and why, with `### Avantages` / `### Inconvénients`
7. **`## Analyse des options`** — pros/cons breakdown for each option

## Status lifecycle

The `Statut` value uses the French label:

| Statut       | Meaning                                         |
| ------------ | ----------------------------------------------- |
| `Proposée`   | Under discussion, not yet validated             |
| `Acceptée`   | Decision made and validated                     |
| `Rejetée`    | No satisfactory option retained                 |
| `Dépréciée`  | Obsolete without direct replacement             |
| `Remplacée`  | Replaced by a new ADR (add link to the new one) |

## Golden rules

1. **An ADR is immutable** — once accepted, only its status may change.
2. If a decision changes, create a **new ADR** that supersedes the previous one.
3. ADRs live **in the Git repository** to stay in sync with the code; location follows the project's existing ADRs.
4. Use **sequential numbering** and problem-oriented titles.
5. List **all options considered**, including the simplest ones.
