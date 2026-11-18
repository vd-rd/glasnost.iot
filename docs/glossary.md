# Glossary

Terms used across Glasnost docs.

| Term | Meaning |
|---|---|
| **Boot ROM** | Code built into the processor that runs first at power-up and looks for a bootloader on the boot media (microSD card, flash, …). |
| **Bootloader** | Small program that prepares the board (memory, clocks) and starts the next stage. Glasnost boards use AT91Bootstrap or SPL, then U-Boot, then Linux. |
| **Cape** | An add-on board that plugs onto a Glasnost board's header to add a function (PoE, LCD, GPS, …). |
| **Debug console** | The serial port that shows boot messages and gives you a login prompt. You connect to it with a 3.3 V USB-to-serial adapter. |
| **Device tree (DT)** | A file that tells Linux what hardware is on the board and how it's wired. |
| **DNP** | "Do not populate": a part that has a place on the board but isn't fitted by default. |
| **ESD** | Electrostatic discharge — a static zap; protection parts absorb it before it reaches the chips. |
| **FEL / SAM-BA** | Recovery modes built into the processor (FEL on Allwinner, SAM-BA on Microchip): if no bootable media is found, the board waits for a PC to load software over USB or serial. |
| **Gerbers** | The manufacturing files a PCB fab uses to make the bare board. |
| **PMIC** | Power-management IC: one chip that makes several of the board's supply voltages and controls power-up order. |
| **Rail** | One supply voltage on the board, e.g. the 3.3 V rail. |
| **SDSC / SDHC** | microSD card types: SDSC (standard capacity, up to 2 GB) and SDHC/SDXC (larger). Some processors' boot ROMs only read SDSC. |
| **U.FL** | A tiny coaxial connector for an external antenna. |
