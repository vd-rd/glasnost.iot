# Design review

How hardware design reviews are done. Board-agnostic: applies to every Glasnost board repo. Board repos follow this process; findings live in each board repo (gitignored `review/<rev>/`, then GitHub issues).

| File | Purpose |
|---|---|
| [process.md](process.md) | Review stages, their order, and rules for evidence |
| [checklist.md](checklist.md) | Concrete checks per stage and how to verify them |
| [house-rules.md](house-rules.md) | Owner decisions that apply across boards; don't re-litigate them |
| [references.md](references.md) | How to keep the per-board reference manifest (datasheets, errata, checklists, reference designs) |
| [tooling.md](tooling.md) | Containerised kicad-cli and helper scripts |

Findings are tracked as GitHub issues; fixes land as pull requests.
