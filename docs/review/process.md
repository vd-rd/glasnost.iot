# Review process

Order matters: each stage relies on the previous one.

## Full vs delta review
**Full review** (all stages) for: a new board; a change of SoC, PMIC/main regulators, memory type or boot media; a new layout/stackup or major re-route; no previous review, or the last one predates this process.

**Delta review** otherwise (rev N+1 against a reviewed rev N):
1. Find what changed: git diff of schematic and PCB since the reviewed baseline, plus a **netlist diff** (kicad-cli netlist at both revisions, compared per net).
2. Review each changed circuit through its **full chain** (every rail, enable or signal path it touches), not just the changed line.
3. Re-run all automated layout checks in full (DRC, refill diff, tangent scan, connectivity) — layout edits break things far away.
4. Re-check references (stage 1) for new or changed parts.
5. Re-verify open issues from the previous review against the new revision.

Every review records the commit/tag it covered (journal entry and/or a `review/<rev>` git tag) so the next delta has a baseline.

## Revisions and fab records
- When boards are ordered, the exact commit is tagged `fab/<rev>-<n>` (e.g. `fab/2.0-1`), annotated with fab/assembler, quantity, date and anything special (DNP changes, planned bodges).
- Right after a fab, `BOARD_REV` is bumped to the next revision with a `-dev` suffix (e.g. `2.1-dev`); the suffix is dropped only for the commit that is ordered next.
- Release tags (`v<rev>`) mark published releases with artefacts and may point at the same commit as the `fab/` tag.
- Each fab gets a journal entry linking its tag.
- Past fabs are tagged retroactively only on the owner's word.

## 0. Intake
- KiCad version of the files (`generator_version`) vs what the README states.
- `fab/*` tags (and releases): which revisions were built. Every finding states whether the affected circuit exists in a built revision; if no `fab/` tag exists, say so and ask the owner rather than guessing.
- Schematic ↔ PCB in sync (DRC `--schematic-parity`); any published schematic PDF matches the sources.

## 1. References (before any schematic review)
For the important circuits — CPU/SoC, PMIC/regulators, RAM, boot flash — **and every active part in the power path or in a reset/enable/power-hold chain** (level shifters, power switches, MOSFETs, boost/buck converters, LDOs), plus connectors whose behaviour matters (e.g. card-detect switches):
- Datasheet listed in the board's reference manifest (`resources/REFERENCES.md`) with document number, revision, date and URL. Key documents (SoC, PMIC, RAM, boot flash, errata, checklists, reference designs, app notes) are also committed in `resources/`; small-part datasheets are URL-only (see house-rules.md).
- Errata / silicon revision notes. Assume the newest silicon/part revision is bought; **warn if a part is not backwards compatible** with earlier revisions or with the reference design.
- Vendor schematic/hardware checklist, reference design (eval/EK board), relevant app notes.
- A missing or outdated reference is itself a finding.
- **No vendor checklist?** Fall back, in order, to: (1) the vendor reference design (EK/eval/reference schematic), (2) the datasheet's own design guidance (pin descriptions, power-supply and layout sections), (3) proven open designs using the same part — hints, not authority, (4) our checklist. Record what was used in the manifest. If there is no reference design either, mark the part as a **higher-risk area**: every finding there, Low included, goes through the verification gate.

## 2. Schematic — power first
1. **Root-sheet power tree**: the PMIC/regulators and the main blocks connected to the CPU must be drawn on the root sheet. Compare the drawn tree with the netlist.
2. **Power plane**: each rail's voltage vs its consumers' requirements (feedback dividers checked against the regulator's own table/equation), capacitance (min/max, DC-bias derating, vendor-recommended values), inductors, control signals (EN, PG, power-hold, sequencing, auto-start), input protection (ESD/TVS/reverse), power path.
3. **Current budget per rail** vs regulator / switch / LDO / MOSFET rating (typical and peak).
4. **Consumers**: CPU (every supply pin incl. PLL/OSC/backup/analog/USB and their filtering), memory, SD, wireless, others — each from the right rail, decoupled per vendor guidance.
5. **Cross-voltage-domain signals**: every signal between rails of different voltage — thresholds checked against both parts' datasheets.

## 3. Schematic — CPU
- Boot flow: ROM boot order, pins/chip-selects/slots it uses, media limits, boot straps, recovery path.
- **Reset state of every used pin** and pins driven by the boot ROM; voltage domain and tolerance of each pin; pin current limits.
- Clocks: crystal load capacitance vs CL and internal capacitance, case grounding, PLL filters.
- Reset, debug, wake/shutdown pins.
- Memory interface: address/bank/byte-mask mapping, data bus, clock, termination, VREF/ZQ for DDR.
- Pin mux of every used signal; pull-ups/pull-downs (I²C, SD); USB (series resistors, pull-downs, ID, VBUS detect and path).

## 4. Schematic — other blocks
Networking, storage, LCD, audio, headers (against the shared Glasnost header pinout), connectors.

## 5. Layout

| Check | Tool |
|---|---|
| DRC with schematic parity; unrouted nets | `kicad-cli pcb drc` |
| Stale zone fills: plain DRC vs `--refill-zones` diff | `kicad-cli` (two runs) |
| Near-tangent joints (µm overlaps DRC treats as connected) | tangent-overlap scan |
| Copper connectivity per net (cross-check) | connectivity script |
| Fast signals crossing inner-plane splits | plane-split script |
| Lengths/widths/matching: memory clock vs address/data, diff pairs, crystal traces | length script |
| RF feed impedance | microstrip/CPW calculation from the stackup |
| Netclass patterns and custom rules match current net/ref names | name-check script |
| Visual inspection of specific areas | `kicad-cli` SVG export, region render |

Not yet covered: DDR3-specific rules (topology, per-lane matching, VREF routing), thermal, fab/mechanical checks.

## 5b. Mechanical, fab & assembly

**Mechanical**
- Outline and mounting holes match the docs and any shared form factor/enclosure.
- Connector positions/orientation (edge overhang/alignment), header pin-1 position and mounting side consistent across boards.
- Tall and bottom-side parts vs clearances (stacked add-ons, enclosure); 3D render with models shows no collisions.

**Fab**
- Stackup, min track/clearance, drill/annular ring, thickness vs the target fab's published capabilities (compare once per fab).
- Copper-to-edge clearance near overhanging connectors.
- Impedance-controlled stackup ordered when RF/DDR needs it; panel/rails if required.

**Assembly**
- Pick-and-place: rotation/origin per the assembler's convention; bottom-side parts handled.
- Paste for BGA/QFN thermal pads; via-in-pad flagged.
- Polarity and pin-1 marks in silkscreen; DNP parts marked in BOM and assembly outputs.
- Parts needing hand soldering listed; hand-assembly difficulty noted for the "build your own" docs.

**BOM consolidation (passives)**
- List unique passive values per type/package. For values used only once or twice, check whether a value already on the board works instead (e.g. a lone 68 kΩ pull-up → 47 kΩ or 100 kΩ already used elsewhere).
- Never consolidate values that set something precisely without recomputing it: feedback dividers, crystal load caps, filter/RC timing, current-sense and USB/impedance-matching resistors. Recompute and record the result when changed.
- Also consolidate voltage rating/dielectric/package where it doesn't hurt (fewer line items, fewer reels).

## 6a. Conventions & best practices
Check the board against the house naming convention (house-rules.md) and, where it is silent, against established practice:
- **Reference designators**: letters per ASME Y14.44 / IEEE 315 (R, C, L, FB, U, Q, D, Y, J, SW, TP, F, RN, AE); numbering consistent with the sheet structure (e.g. sheet-based ranges), no stray low/odd numbers.
- **Net names**: one case style and separator; consistent differential-pair and active-low notation; rails named by voltage; the same signal has the same name on every sheet; no duplicate names for one net.
- **Symbols/footprints**: IPC-7351 land patterns, KiCad library conventions (KLC); project libraries named and shipped with the project; footprint/symbol names match the actual part.
- **General practice**: decoupling per pin, no unexplained single-pin nets, no-connect flags on intentionally unused pins, power flags where needed, sheet titles/rev/date filled.
Findings here are usually Low; fix in batches (re-annotation touches every refdes — do it once per revision, before fab outputs).

## 6. Hygiene & docs
- BOM: guidance per line plus a recommended part (see house-rules.md).
- Orphan files, stale netclass patterns/custom rules, dangling labels.
- Docs vs hardware: features, dimensions, boot, pinouts, KiCad version, links.
- Software bring-up hints (DT pull-ups, PMIC register assumptions, …) — format to be defined.

## Ready for fab (exit criteria)
A revision may be ordered when all of these hold on the exact commit being ordered:

**Review**
- Review (full or delta) done; baseline commit/tag recorded.
- No open `severity:high` issues; every open `severity:medium` issue fixed or justified in the issue.
- Reference manifest has no "missing" entries for stage-1 parts, or the gaps are accepted in an issue.

**Automated checks**
- ERC triaged, no unexplained errors.
- DRC with schematic parity: 0 unconnected, no unexplained violations.
- DRC vs `--refill-zones` diff empty (saved fills are current).
- Tangent-overlap scan and connectivity check clean.

**Artefacts** — generated from that same commit, only after the refill check passed:
- Gerbers/drill, pick-and-place, BOM (guidance + recommended parts), schematic PDF, renders.

**Docs & release**
- Docs updated for the hardware changes (features, pinouts, boot, dimensions).
- `BOARD_REV` set to the final revision (no `-dev`); commit tagged `fab/<rev>-<n>`; release tagged with the artefacts attached.
- Journal entry for the fab: what changed, what's still open.

Rule: all artefacts come from one tagged commit — nothing hand-exported from a working copy.

## Severity

| Severity | Meaning | Before fab |
|---|---|---|
| **High** | Board or a function won't work, a part is driven beyond its absolute maximum, a short/open, or a fab defect | must fix |
| **Medium** | Works on the bench but fails under some conditions or erodes margin: corner cases, part-to-part variation, reliability, ESD | fix, or justify in the issue |
| **Low** | Spec deviation with negligible effect, optimisation, housekeeping | optional |
| **Docs** | Documentation doesn't match the hardware, or is missing | before release |

GitHub labels: `severity:high`, `severity:medium`, `severity:low`, `severity:docs`; add `built-rev-affected` when a fabbed revision has the problem.

## Rules for reviewers (human or agent)
- **Evidence or it didn't happen**: cite part, pin/ball, net, value, and the datasheet table/section. Say explicitly when a datasheet was not opened.
- Values taken from a standard or document that wasn't opened (USB, SD specs, app notes) are marked **"not verified"** — cite the document, or don't use the number as evidence.
- **Errata override the datasheet body**: a pin the datasheet says may be left unconnected can still need a connection per errata.
- For "board won't work" claims, check every link of the chain against each part's own datasheet.
- Give typical **and** worst-case numbers.
- **Verification gate**: every High/Medium finding is re-checked against the primary source before it is reported to the owner.
- **Working report first, issues last.** During a review, findings go to a gitignored working folder at the repo root: `review/<rev>/FINDINGS.md` (plus the reference manifest draft and any notes/renders). The owner walks through them and decides per finding. Only when the owner is satisfied with the remaining list are GitHub issues opened — one per finding: severity (High/Medium/Low/Docs), finding with evidence, proposal, built-revision impact, owner decision. Decisions that generalise go into house-rules.md.
- After fixes: re-run ERC/DRC (incl. refill diff), connectivity and tangent scans.
