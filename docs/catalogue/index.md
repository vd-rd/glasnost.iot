# Catalogue

All Glasnost boards. Status from a DRC survey on 2026-11-18 (unconnected items = routing not finished).

## Routed

| Board | SoC | Repo | Status |
|---|---|---|---|
| M9G10 | Microchip AT91SAM9G10 | [sbc_at91sam9g10](https://github.com/vd-rd/sbc_at91sam9g10) | Rev 2.0 built; review done, fixes tracked in issues |
| M9G20 | Microchip AT91SAM9G20 | [sbc_at91sam9g20](https://github.com/vd-rd/sbc_at91sam9g20) | Routed; reviewed |
| A13 | Allwinner A13 | [sbc_allwinner_a13](https://github.com/vd-rd/sbc_allwinner_a13) | Routed; review in progress |
| M9G25 | Microchip AT91SAM9G25 | [sbc_at91sam9g25](https://github.com/vd-rd/sbc_at91sam9g25) | Routed (variants hw-compute, hw-mobile); review in progress |

## In progress

| Board | SoC | Repo | Unconnected |
|---|---|---|---|
| A33 | Allwinner A33 | [sbc_allwinner_a33](https://github.com/vd-rd/sbc_allwinner_a33) | 127 |
| i.MX | NXP i.MX | sbc_nxp_imx | 171 |
| SAM9X60 | Microchip SAM9X60 SiP | [sbc_microchip_SAM9X60](https://github.com/vd-rd/sbc_microchip_SAM9X60) | 189 |
| A20 | Allwinner A10/A20/T2 | sbc_allwinner_a20 | 292 |
| SAMA7G54 | Microchip SAMA7G54 | sbc_microchip_sama7g54 | 388 |
| D1 | Allwinner D1 (RISC-V) | [sbc_allwinner_D1](https://github.com/vd-rd/sbc_allwinner_D1) | 457 |

## Early

A64 (sbc_allwinner_a64), SAMA5D3 (sbc_microchip_sama5d3), SoChip S3 (sbc_sochip-s3), SAMA5D2 ([sbc_microchip_sama5d2](https://github.com/vd-rd/sbc_microchip_sama5d2)), V3s ([sbc_allwinner_v3s](https://github.com/vd-rd/sbc_allwinner_v3s)), D1s/F133 (sbc_allwinner_F133), F1C100s/F1C200s ([sbc_allwinner_F1C200s](https://github.com/vd-rd/sbc_allwinner_F1C200s)), i.MX233 (sbc_nxp_imx233), LS1012A ([sbc_nxp_ls1012a](https://github.com/vd-rd/sbc_nxp_ls1012a)), ADSP-SC589 (sbc_adi_sc58x), NUC980 ([sbc_nuvoton_nuc980](https://github.com/vd-rd/sbc_nuvoton_nuc980), no design files yet).

## Legacy (Eagle) / archived

| Board | SoC | Repo | Notes |
|---|---|---|---|
| SAM9260 | AT91SAM9260 | [sbc_at91sam9260](https://github.com/vd-rd/sbc_at91sam9260) | Eagle; v1.1.0 assembled and tested |
| RM9200 | AT91RM9200 | [sbc_at91rm9200](https://github.com/vd-rd/sbc_at91rm9200) | Eagle; archived |
| SAM9N12 | AT91SAM9N12 | sbc_at91sam9n12 | Eagle; archived |
| Quark | Intel Quark X1000 | [sbc_quark_x1000](https://github.com/vd-rd/sbc_quark_x1000) | Eagle; archived |
| R329 | Allwinner R329 | sbc_allwinner_r329 | KiCad 6, placement only; archived |

## Capes and modules

Add-on boards for the SBCs. Status not yet surveyed.

| Cape / module | Function | Repo | Last activity |
|---|---|---|---|
| PoE cape | 802.3 PD power | [hw_cape_poe](https://github.com/vd-rd/hw_cape_poe) | 2026-04 |
| LCD cape | LCD module | [hw_cape_lcd](https://github.com/vd-rd/hw_cape_lcd) | 2022-04 |
| GPS cape | GPS telemetry | [hw_cape_gps](https://github.com/vd-rd/hw_cape_gps) | 2019-06 |
| GSM cape | 2G GSM/GPRS | [hw_cape_gsm](https://github.com/vd-rd/hw_cape_gsm) | 2019-06 |
| Mains cape | Isolated step-down mains power | [hw_cape_mains](https://github.com/vd-rd/hw_cape_mains) | 2019-09 |
| Heimdall cape | Motion detection (PIR, ToF, FIR) | [hw_cape_heimdall](https://github.com/vd-rd/hw_cape_heimdall) | 2020-01 |
| mPCIe cape | Mini PCI Express breakout | [hw_cape_mpci](https://github.com/vd-rd/hw_cape_mpci) | 2019-05 |
| mPCIe GPS module | GPS on mini PCIe | [hw_mpcie_gps](https://github.com/vd-rd/hw_mpcie_gps) | 2022-01 |
| mPCIe GSM module | GSM on mini PCIe | [hw_mpcie_gsm](https://github.com/vd-rd/hw_mpcie_gsm) | 2019-05 |
| Mains power (old) | — | [hw_power_mains](https://github.com/vd-rd/hw_power_mains) | archived |

## Tools & support

| Item | Purpose | Repo |
|---|---|---|
| Debug jig | Debug jig for Glasnost boards | [glasnost_debug_jig](https://github.com/vd-rd/glasnost_debug_jig) |
| KiCad libraries | Shared symbols, footprints, 3D models | [kicad-libraries](https://github.com/vd-rd/kicad-libraries) |

Software (bootloaders, U-Boot, Linux, distro) is maintained under [printarescu-labs](https://github.com/printarescu-labs).

Repos without a link are private.
