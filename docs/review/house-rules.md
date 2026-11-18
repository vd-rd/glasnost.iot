# House rules (owner decisions)

Decisions that apply across Glasnost boards. Reviewers: don't re-propose these; when a finding matches one, say so in the issue.

## Design
- A board's boot media layout is fixed by design; report its limits (e.g. SDSC-only SD boot) as docs, don't propose rerouting boot devices.
- No JTAG required; debug via the SoC's debug UART and ROM recovery mode.
- Status LEDs: one common series-resistor value for all channels (no per-colour tuning), chosen as large as still gives visible output on the dimmest channel (typically 270–330 Ω for a common-anode RGB on 3.3 V). Pin current above the rated drive is reported as Low with the numbers, not as a blocker.
- microSD card-detect switch to shell/GND is correct wiring, not a defect. The switch type (normally open vs normally closed) depends on the connector variant — take it from the connector drawing and state the resulting GPIO polarity for software (e.g. GCT MEM2061-01 is normally closed: grounded without a card, open with a card).
- The 1.27 mm USB-host add-on header carries no VBUS: the add-on generates its own +5 V.
- Shared Glasnost header pinout: ACIN is wired when the board has a power path — a power-path PMIC or a discrete one (e.g. diode/MOSFET OR-ing of USB and ACIN); otherwise it's a placeholder. Header signals that exist only on some SoC variants of a family board are wired, not placeholders, and are named after the actual peripheral instance (e.g. `CAN1_TX`, not `CAN0`), with the per-variant function documented. Unused standard labels (CAN, GPIOA, …) stay as placeholders with a "not connected on this board" note. No written spec yet (to be built later); until then the existing boards' header pinouts are considered correct — check new boards for consistency with them, don't flag the pinout itself.
- Copper joints are real tracks/vias into pad centres, never tangent or marginal overlaps.
- No fiducials required; don't flag missing fiducials.
- I²C pull-ups are fitted on the board (not left to add-ons): **1.8 kΩ** to the I/O rail, or stronger if the bus load needs it (TI I²C pull-up app note: ≈1.6 mA at 3.3 V, fast-mode rise time up to ~200 pF).
- New optional parts (pull-ups, termination, filters) may be added as DNP / 0 Ω footprints and tuned after bring-up.

## Naming convention
- Annotation: sheet number × 100 (root sheet 1xx, then each sheet in hierarchy order).
- Refdes letters per ASME Y14.44 / IEEE 315.
- Rails:
  - **Fixed rails** are named by voltage: `+<voltage>[_<purpose>]` — e.g. `+3V3`, `+1V8`, `+5V_USB`; add a purpose suffix only when two rails share a voltage (`+3V3_WIFI`).
  - **Variable rails** (DVFS, or set by software/straps at runtime) are named by function, not voltage: e.g. `VDD_CPU`, `VDD_CORE`. The schematic notes the range and default (e.g. "1.0–1.3 V, default 1.2 V").
- Signals: **UPPER_SNAKE for global (cross-sheet) nets, lower_snake for sheet-local nets**; active-low `~{…}`; bus members without leading zeros; one name per net across the design. Differential pairs use `_P`/`_N` (e.g. `USB0_D_P`, `USB0_D_N`).

## Parts & BOM
- Boards are open hardware built by others: **don't lock passives to one part number**. Give guidance per line (value, tolerance, voltage/power rating, dielectric, saturation current, load capacitance, package, …) plus a recommended part number; publish as an HTML/PDF BOM.
- Active parts in power/reset/enable chains need an identified part or exact variant when polarity/variant matters.
- Assume the newest silicon/part revision is bought; flag anything not backwards compatible.

## Fab
- Primary target: **JLCPCB**; secondary: **PCBWay**. Designs stay within the common subset of both fabs' standard (non-premium) capabilities, so builders can use either — or another fab that meets the board's stated minimums.
- Each board documents its fab requirements for builders: layer count, thickness/stackup, min track/space, min drill and annular ring, copper-to-edge, via-in-pad, impedance control if needed. Anything outside the common standard subset is called out explicitly.
- Capability numbers come from the fabs' published capability pages (URLs recorded with the numbers; re-check when reviewing, they change).

## Repo & docs
- **Key documents are committed** in `resources/` and listed in `resources/REFERENCES.md`: SoC/CPU, PMIC, RAM, boot flash datasheets, errata, vendor checklists, reference designs, relevant app notes. **Small-part datasheets** (regulators, LDOs, logic gates, MOSFETs, switches, connector drawings, passives) are listed in the manifest by URL only.
- File naming in `resources/`: `datasheet-<part>[-<docno/rev>].pdf`, `appnote-<docno>_<topic>.pdf`, `refdes-<board>.pdf`, `errata-<part>.pdf`.
- Docs wording: "default + option" (e.g. "64 MB default, up to 128 MB").
- DRC rule severities in the project are the owner's choice; leave them.
