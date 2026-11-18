# Tooling

Run tools in containers; don't install anything on the host. Mount the board repo read-only and write outputs to a separate directory.

## kicad-cli — first automated stage
Use the official KiCad image whose major version matches the files (`generator_version` in the `.kicad_*` files); an older KiCad cannot open newer files.

Run inside the container, from the directory holding the project (`<p>` = project name):

```sh
kicad-cli sch erc --format json --severity-all -o erc.json <p>.kicad_sch
kicad-cli pcb drc --format json --severity-all --schematic-parity -o drc.json <p>.kicad_pcb
kicad-cli pcb drc --format json --severity-all --schematic-parity --refill-zones -o drc_refill.json <p>.kicad_pcb
kicad-cli sch export netlist -o <p>.net <p>.kicad_sch
kicad-cli sch export svg -o sch_svg <p>.kicad_sch
kicad-cli pcb export svg --mode-multi --page-size-mode 2 --exclude-drawing-sheet -l <copper layers> --cl Edge.Cuts -o pcb_svg <p>.kicad_pcb
kicad-cli pcb render --quality high --side top -o render/top.png <p>.kicad_pcb
```

- Diff `drc.json` vs `drc_refill.json`: differences mean stale zone fills saved in the board file.
- DRC treats near-zero copper overlaps (µm-level via/pad or track/pad contact) as connected → also run the tangent-overlap scan.
- Expected ERC noise when project libraries aren't available in the container: `lib_symbol_mismatch`, `footprint_link_issues`.
- Rootless containers: map the user namespace (e.g. podman `--userns=keep-id`) or kicad-cli can't write its outputs.
- Position (pick-and-place) export: use `kicad-cli pcb export pos --use-drill-file-origin` when the board sets an aux/drill origin.
- `pcb render` shows component bodies only if their 3D models are available in the container; user-library models must be in the repo for 3D checks.

## Helper scripts
Live in `scripts/` at the repo root (not under `docs/`, which MkDocs would publish); they move to the central Glasnost repo once it exists. Planned set: s-expression parser, PCB netlist dump, copper connectivity, shallow-overlap scan (every pad↔via/track overlap below 0.05 mm, reported per pad even when the pad also has full joints — combine with connectivity split groups), track lengths/widths, plane-split crossings (all layers, all plane islands), netclass/custom-rule name check, region render. Known limitation to fix: zone-to-track joining in the connectivity check is heuristic (false positives possible) — verify splits by render.

## tscircuit
KiCad → Circuit JSON import was evaluated and is not yet usable as the primary model (failed on KiCad 10 boards and on schematic sheets with buses; one sheet per run; pin functions and refdes dropped). Useful as an optional cross-check (`@tscircuit/checks`: pour shorts, trace contiguity). Re-evaluate as it matures.
