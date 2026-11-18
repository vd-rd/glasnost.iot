# AGENTS.md

Guidance for AI agents working in this repository or on Glasnost boards.

## This repository
- The source of truth for the review process (`docs/review/`), house rules, standards and templates. Changes here apply to every board — keep files board-agnostic.
- `docs/catalogue/` is the only place with per-board information, and only as an overview.

## Working on a board repo
1. Read `docs/review/house-rules.md` first — those decisions are settled.
2. Follow `docs/review/process.md` in order; use `checklist.md` per stage.
3. References first: the board's `resources/REFERENCES.md` (format: `docs/review/references.md`).
4. Tools per `docs/review/tooling.md` (containers only).

## Rules
- Don't install anything on the host; use containers.
- Treat repos as read-only during reviews; don't modify design files or commit unless asked.
- Every finding needs evidence (part, pin, net, value, datasheet table/section); say when a document wasn't opened.
- Findings go to the board repo's gitignored `review/<rev>/FINDINGS.md`. GitHub issues are opened only after the owner has gone through the findings and says so.
