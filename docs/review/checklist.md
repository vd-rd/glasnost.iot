# Checklist

Stages follow process.md. Each check: what to verify → how / evidence.

## 0. Intake
- [ ] KiCad version in README matches `generator_version` of the files.
- [ ] Built revisions identified (tags/releases, fab commits).
- [ ] Schematic ↔ PCB parity clean; published schematic PDF matches sources.

## 1. References
- [ ] Manifest lists datasheet, errata, hardware checklist, reference design, app notes for every stage-1 part → each verified against the exact part/variant.
- [ ] Datasheets in the repo (if any) are for the right part and current.
- [ ] Silicon/part revision differences that break backwards compatibility flagged.
- [ ] **Every hardware-relevant erratum read and checked against the schematic** (power-up sequencing, reset/test/debug pins, clocks, memory interfaces, peripherals in use) — including pins the datasheet body says "may be left unconnected".

## 2. Power
- [ ] Root sheet shows the power tree; it matches the netlist.
- [ ] Every regulator output: feedback divider → output voltage computed with the datasheet reference voltage; compared with the vendor's recommended-values table. Top/bottom resistor orientation checked.
- [ ] Each rail voltage within every consumer's operating range (typ and worst case).
- [ ] Input/output capacitance vs vendor min/max, after DC-bias derating; inductor value and saturation current in the recommended range.
- [ ] Control chain: EN / PG / power-hold / sequencing / auto-start — every link's logic thresholds and polarity checked against both datasheets; default state at power-up (floating pins, pull resistors).
- [ ] Power path: MOSFET/switch/diode current and RDS(on)/Vf vs load; reverse-current behaviour; variant/polarity of switches.
- [ ] Current budget per rail (typ/peak) vs regulator/LDO/switch rating; dissipation.
- [ ] Input protection: ESD/TVS on external connectors, overvoltage/reverse.
- [ ] Every CPU supply pin connected to the right rail, incl. PLL/OSC/backup/analog/USB; filtering per vendor checklist.
- [ ] Memory, storage, wireless and other consumers on correct rails, decoupled per vendor guidance.
- [ ] Every signal crossing voltage domains: driver VOH/VOL vs receiver VIH/VIL; pull-ups only to allowed rails.

## 3. CPU
- [ ] Boot: ROM boot order, media/slot/chip-select used, media limits (capacity, file size), straps, recovery path — documented and consistent with the hardware.
- [ ] Reset state of every used pin (input/pull-up vs actively driven bus/peripheral function); pins driven by the boot ROM; no contention with what's attached.
- [ ] Pin voltage domain and tolerance for every external signal.
- [ ] Pin output current vs load (LEDs, etc.).
- [ ] Crystals: load caps from CL and internal capacitance; case pins grounded; placement/trace length (esp. 32 kHz).
- [ ] PLL loop filters per vendor tool/reference.
- [ ] Reset network (cap size vs the reset controller's pulse), debug/test pins, wake/shutdown pins per datasheet.
- [ ] Memory: address/bank/byte-mask mapping for the bus width; clock termination; DDR VREF/ZQ/ODT.
- [ ] Pin mux of every used signal; no double use.
- [ ] Pull-ups/downs: I²C, SD CMD/DAT, open-drain lines; internal pull-up range vs spec.
- [ ] USB: series resistors per datasheet, host pull-downs (internal vs external), device pull-up, ID, VBUS detect divider (pin tolerance), VBUS enable/switch logic.

## 4. Other blocks
- [ ] Networking (PHY straps, clocks, reset/interrupt wiring), storage, LCD, audio (incl. supply pins), wireless module supply/enable.
- [ ] Headers against the shared Glasnost pinout; placeholders noted.

## 5. Layout (pending tooling)
- [ ] ERC/DRC clean or triaged; DRC vs `--refill-zones` diff empty.
- [ ] Connectivity and tangent-overlap scan clean.
- [ ] Fast signals over continuous reference planes; split crossings stitched.
- [ ] Memory/clock lengths and termination; RF feed impedance.
- [ ] Netclass patterns and custom rules match current net/ref names.

## 5b. Mechanical, fab & assembly
- [ ] Outline/holes/connectors match docs and the shared form factor; no 3D collisions.
- [ ] Board minimums (track/space, drill, annular ring, copper-to-edge, via-in-pad, stackup) within the common standard capabilities of JLCPCB and PCBWay (published pages, re-checked); exceptions called out; fab requirements documented for builders.
- [ ] Pick-and-place conventions, paste on thermal pads, via-in-pad, polarity marks, DNP marked.
- [ ] Passive consolidation: one-off values replaced by values already on the board where the function allows; precision-setting values (dividers, load caps, RC/filters, sense, matching) only with recomputation.

## 6a. Conventions & best practices
- [ ] Refdes letters per ASME Y14.44 / IEEE 315; numbering follows the house convention (sheet-based ranges).
- [ ] Net names: consistent case/separator, diff-pair and active-low notation, rails named by voltage, one name per net across sheets.
- [ ] Footprints IPC-7351 / KLC-conformant; project libraries named and included; names match the actual part.
- [ ] No-connect flags on unused pins, power flags, no unexplained single-pin nets; title blocks (title, rev, date) filled.

## 6. Hygiene & docs
- [ ] BOM lines carry guidance (value, tolerance, rating, dielectric, saturation, CL, package) + recommended part; descriptions not copy-pasted from other lines.
- [ ] No orphan sheets/files; no dangling labels without a note.
- [ ] Docs match hardware: features, memory, USB, boot, dimensions, pinouts, KiCad version, links.
