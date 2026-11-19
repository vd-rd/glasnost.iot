# Documentation style guide

How Glasnost docs are written, so every board reads the same way. Applies to board READMEs, board doc sites and this site.

## Who we write for

Two readers, on every page:

- **The newcomer** — has soldered before, maybe flashed a Raspberry Pi, but has never brought up a bare Linux board. Needs the *why*, the steps in order, and every term explained once.
- **The experienced builder** — knows embedded Linux and wants facts fast: specs, pinouts, boot order, part guidance, known issues.

Serve both without making either wade through the other's material: lead with a short plain-language summary, then the details in tables. Explain a term the first time it appears on a page (or link the [glossary](glossary.md)); never assume it.

## Voice

- Plain, direct, friendly. Second person ("you") for instructions; "we" for the project.
- Short sentences. One idea per sentence. Active voice ("Insert the card", not "The card should be inserted").
- Say what something *does* before what it *is* ("The debug console shows boot messages — it's the UART on header pins 11 and 12").
- Be honest about status: if something is untested, missing or broken, say so and link the issue. Don't call a board "stable" until a newcomer can follow the docs to a working board.
- No filler ("simply", "just", "easily", "obviously"). If it were simple, they wouldn't be reading.

## Terms

Use one name per thing, everywhere:

| Use | Not |
|---|---|
| board | SBC (except in titles), module, device |
| cape | add-on, shield, HAT, expansion board |
| microSD card | SD, sd-card, card (alone, at first mention) |
| main header (J4) | expansion header, GPIO header |
| debug console | serial console, DBGU, UART0 (give the hardware name once in brackets) |
| USB-C port | microUSB, USB port |
| 3.3 V, 5 V (space, capital V) | 3v3, 3.3v, 5V |
| 64 MB, 2 Gbit | 64MB, 2Gb |

Spell out numbers with units consistently: `66 × 42 mm`, `115200 baud`, `1 A`.

## Page structure

Every page:

1. **Title** — what the page is about, in words a newcomer would search for.
2. **One-paragraph summary** — what you'll learn or do here, and who it's for.
3. **Body** — for how-tos: numbered steps, each with the expected result. For reference: tables.
4. **What next / see also** — links to the logical next page.

Use admonitions sparingly:
- `!!! warning` — can damage hardware or lose data.
- `!!! note` — limits and gotchas (e.g. "only cards up to 2 GB boot").
- `!!! tip` — optional shortcuts for experienced readers.

## Board doc site layout

Every board site has the same sections, in this order:

| Section | Contents |
|---|---|
| Home | One sentence on what the board is; photo; links to Getting started and Build your own |
| Getting started | What you need, preparing the microSD card, first power-up, connecting the debug console, what you should see |
| Hardware → Overview | Specifications table; block diagram; what's on the board |
| Hardware → Connectors & pinout | Every connector with a pinout table: pin, signal, voltage, notes |
| Hardware → Power | Supply input, rails, current needs |
| Hardware → Boot & recovery | Boot order, media limits, recovery mode |
| Hardware → Schematics / PCB / BOM | Links to sources and outputs; stackup; BOM guidance |
| Hardware → Build your own | Fab requirements, assembly notes, difficulty |
| Hardware → Known issues | Open issues that affect users, with workarounds |
| Software | Boot chain overview; building from source |
| Journal | Board journal (dated entries) |
| Glossary | Link to the [central glossary](glossary.md) plus board-specific terms |

The README is a short version of Home + the specifications table + links into the site. It must not contradict the site.

## Facts and sources

- Every number in the docs must match the hardware sources (KiCad files) or a cited datasheet. When the hardware changes, the docs change in the same pull request.
- Link datasheets from `resources/` or by clean vendor URL (see [references](review/references.md)).
- Mark unverified information as such ("not yet tested").
