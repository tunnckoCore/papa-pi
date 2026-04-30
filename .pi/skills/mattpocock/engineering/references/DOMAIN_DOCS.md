# Domain Docs

Shared rules for consuming project domain documentation.

## Before exploring, read these

- **`CONTEXT-MAP.md`** at the repo root, if it exists. It points to one `CONTEXT.md` per context; read the contexts relevant to the task.
- **`CONTEXT.md`** at the repo root, if there is no context map.
- **`docs/adr/`** for architectural decisions that touch the area you're about to work in.
- In multi-context repos, also check context-local ADRs such as `src/<context>/docs/adr/`.

If any of these files don't exist, proceed silently. Don't flag their absence or suggest creating them upfront unless the active skill is explicitly a producer flow.

## Producer vs consumer flows

Consumer flows read existing domain docs and adapt their work to them. Examples: diagnosis, TDD, triage, issue creation, PRD creation, architecture review, and zooming out.

Producer flows may create or update domain docs when language or decisions crystallize. Examples: `grill-with-docs` and the grilling loop in `improve-codebase-architecture`.

Create docs lazily:

- create `CONTEXT.md` only when the first project-specific term is resolved;
- create `docs/adr/` only when the first ADR is needed;
- create context-local docs only when a multi-context repo actually needs them.

## Layouts

Single-context repo:

```text
/
├── CONTEXT.md
├── docs/adr/
└── src/
```

Multi-context repo:

```text
/
├── CONTEXT-MAP.md
├── docs/adr/                         # system-wide decisions
└── src/
    ├── ordering/
    │   ├── CONTEXT.md
    │   └── docs/adr/                 # context-specific decisions
    └── billing/
        ├── CONTEXT.md
        └── docs/adr/
```

## Use the glossary vocabulary

When your output names a domain concept in an issue title, refactor proposal, hypothesis, test name, PRD, or comment, use the term as defined in `CONTEXT.md`. Don't drift to synonyms the glossary explicitly avoids.

If the concept you need isn't in the glossary yet, don't invent a new canonical term casually. Either use neutral code-facing language, ask the user, or note that the term should be resolved by a producer flow.

## Flag ADR conflicts

If your output contradicts an existing ADR, surface it explicitly rather than silently overriding it:

> _Contradicts ADR-0007 (event-sourced orders) — but worth reopening because ..._
