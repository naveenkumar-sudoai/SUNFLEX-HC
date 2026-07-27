# SUNFLEX-HC Hardware Design

## Overview

SUNFLEX-HC hardware is designed around the **ShrikeFi** platform — a 4-layer PCB featuring a reconfigurable buck-boost power stage, ESP32-S3 MCU, and Renesas SLG47910V FPGA.

## Design Files

| Directory | Contents |
|-----------|----------|
| `schematics/` | KiCad schematic files (`.kicad_sch`) |
| `pcb/` | KiCad PCB layout files (`.kicad_pcb`) |
| `gerbers/` | Fabrication outputs (Gerber, drill, BOM, CPL) |
| [`bill-of-materials.md`](bill-of-materials.md) | Hardware BOM quick reference |

## Specifications

| Parameter | Value |
|-----------|-------|
| **PCB** | 4-layer, 2 oz Cu inner, ENIG finish |
| **Dimensions** | 100mm × 80mm |
| **Input** | 20–150V DC (terminal block, 5.08mm) |
| **Output** | 10.5–58.8V DC (terminal block, 7.62mm) |
| **Max Power** | 500W (Rev A) |
| **Switching Frequency** | 100–400 kHz (FPGA-controlled) |
| **MCU** | ESP32-S3-WROOM-1 (16 MB Flash, 8 MB PSRAM) |
| **FPGA** | Renesas SLG47910V GreenPAK™ |

## KiCad Version

Designed with **KiCad 8.0+**. To open:

```bash
# Install KiCad (if not already installed)
sudo apt install kicad    # Ubuntu/Debian
# or download from https://www.kicad.org/download/

# Open project
kicad hardware/sunflex_hc.kicad_pro
```

## Fabrication

Upload `gerbers/sunflex_hc_rev_a.zip` to your preferred PCB fab:

- **JLCPCB** — Recommended: 4-layer, 2 oz inner copper, ENIG, green solder mask
- **PCBWay** — 4-layer, 2 oz, ENIG
- **OSH Park** — 4-layer, standard spec (After Dark aesthetic)

## Assembly

See `manufacturing/assembly-notes/` for detailed assembly instructions. Key points:
- Power MOSFETs (Q1–Q4): Solder paste + hot air or reflow
- Inductor (L1): Through-hole, hand solder after SMT reflow
- Terminal blocks: Through-hole, hand solder
- Programming: Tag-Connect TC2030 (no soldered programming header needed)

