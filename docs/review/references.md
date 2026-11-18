# Reference manifest

Each board keeps a manifest of its references so reviews work from known documents. Key documents are committed in `resources/` (column "Local file"); small-part datasheets are URL-only (see house-rules.md).

## What goes in
For every part covered by process.md stage 1:
- Datasheet
- Errata / silicon revision notes
- Vendor schematic or hardware checklist
- Reference design (eval/EK board schematic)
- Relevant app notes (migration notes, power-supply notes, …)

## Format
One table per board:

| Part (refs) | Document | Doc no. / rev / date | URL | Local file | Status |
|---|---|---|---|---|---|
| *e.g. SoC (U1)* | Datasheet | *DSxxxx rev C, 2024-01* | *https://…* | *datasheet-xxx.pdf* / — | verified YYYY-MM-DD / missing / outdated |

- **URLs must be clean and stable**: a direct vendor download link with no session IDs, tokens, tracking or query parameters. If the vendor only offers such links (or none), record the vendor's **product page** instead. Distributor/third-party mirrors are a last resort and are labelled "(mirror)" alongside the product page.
- **verified**: downloaded, and the content checked to match the part/variant actually used.
- **missing**: no reference found yet — a review finding.
- **outdated**: a newer revision exists — note what changed if it matters.

Location in a board repo: `resources/REFERENCES.md`. If no vendor checklist exists, the manifest says so and names the fallback used (see process.md, stage 1).
