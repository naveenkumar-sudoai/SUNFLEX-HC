# SUNFLEX-HC Bill of Materials (BOM) — Hardware Reference

> This is a quick-reference BOM for the hardware design. For the complete BOM with supplier links, part numbers, and cost breakdowns, see [`docs/bom.md`](../docs/bom.md).

## Power Stage

| Ref | Part | Description | Qty | Unit Cost | Total |
|-----|------|-------------|:---:|-----------|-------|
| Q1–Q4 | BSC105N25NS3 | N-Ch MOSFET, 250V, 36A, 10.5 mΩ, TDSON-8 | 4 | $1.85 | $7.40 |
| L1 | SRP1770TA-101M | 100 μH, 25A Isat, shielded | 1 | $5.20 | $5.20 |
| C1–C3 | C5750X7R2E226M | 22 μF, 200V, X7R, 1210 | 3 | $0.95 | $2.85 |
| C4–C5 | 200TXW100MEFC | 100 μF, 200V, Alu Electrolytic, low ESR | 2 | $1.45 | $2.90 |
| C6–C7 | GRM32ER72A476KE20 | 47 μF, 100V, X7R, 1210 | 2 | $0.65 | $1.30 |
| C8–C9 | 63SVPF220M | 220 μF, 63V, Alu Polymer, 18 mΩ | 2 | $1.80 | $3.60 |
| F1 | 0997020.WXN | 25A, 250V, automotive blade fuse | 1 | $0.40 | $0.40 |
| D1 | SM15T150CA | 150V bidirectional TVS | 1 | $0.85 | $0.85 |

**Power Stage Subtotal: $24.50**

## Gate Drive

| Ref | Part | Description | Qty | Unit Cost | Total |
|-----|------|-------------|:---:|-----------|-------|
| U1–U2 | LM5109BMAX | Half-bridge driver, 1A, 100V bootstrap | 2 | $1.25 | $2.50 |
| C_BOOT (×4) | CL10B104KA8NNNC | 100 nF, 25V, X7R, 0603 | 4 | $0.02 | $0.08 |
| C_BOOT_BULK (×2) | C2012X7R1E105K | 1 μF, 25V, X7R, 0805 | 2 | $0.05 | $0.10 |
| D_BOOT (×2) | BAV21W-7-F | 200V, 1A Schottky | 2 | $0.08 | $0.16 |
| R_GATE (×4) | RC0805FR-074R7L | 4.7 Ω, 0805 | 4 | $0.01 | $0.04 |

**Gate Drive Subtotal: $2.88**

## Sensing & Measurement

| Ref | Part | Description | Qty | Unit Cost | Total |
|-----|------|-------------|:---:|-----------|-------|
| U3–U4 | INA229AIDGSR | 20-bit current sense, SPI | 2 | $2.35 | $4.70 |
| R_SHUNT (×2) | CSS2H-3920R-5L00F | 5 mΩ, 10W, 1%, 3920 | 2 | $0.65 | $1.30 |
| TH1–TH3 | NCP15XH103F03RC | 10 kΩ NTC, B=3435 | 3 | $0.12 | $0.36 |
| Various | — | Voltage divider resistors | 6 | $0.03 | $0.18 |

**Sensing Subtotal: $6.54**

## Control (ESP32-S3 + FPGA)

| Ref | Part | Description | Qty | Unit Cost | Total |
|-----|------|-------------|:---:|-----------|-------|
| U5 | ESP32-S3-WROOM-1-N16R8 | MCU module, 16 MB Flash, 8 MB PSRAM | 1 | $3.50 | $3.50 |
| U6 | SLG47910V | Renesas GreenPAK FPGA, 960 LUTs | 1 | $4.20 | $4.20 |
| Y1 | ABM8G-40.000MHZ | 40 MHz crystal, 10 ppm | 1 | $0.35 | $0.35 |
| ANT1 | 2450AT18A100E | 2.4 GHz ceramic antenna | 1 | $0.45 | $0.45 |

**Control Subtotal: $8.50**

## Power Supplies

| Ref | Part | Description | Qty | Unit Cost | Total |
|-----|------|-------------|:---:|-----------|-------|
| U7 | LR8N8-G | HV LDO, 450V → 12V, 30 mA | 1 | $0.85 | $0.85 |
| U8 | AP63205WU-7 | Buck reg, 12V → 5V, 2A | 1 | $0.55 | $0.55 |
| U9 | AMS1117-3.3 | LDO, 5V → 3.3V, 1A | 1 | $0.12 | $0.12 |
| U10 | AP2112K-1.8TRG1 | LDO, 5V → 1.8V, 600 mA | 1 | $0.18 | $0.18 |
| Various | — | MLCC caps (10×) | 10 | $0.05 | $0.50 |

**Power Supplies Subtotal: $2.20**

## Connectors & Mechanical

| Ref | Part | Description | Qty | Unit Cost | Total |
|-----|------|-------------|:---:|-----------|-------|
| J1 | 1754465 | Solar input, 2-pin, 5.08 mm, 30A | 1 | $0.95 | $0.95 |
| J2 | 1714981 | Battery output, 2-pin, 7.62 mm, 30A | 1 | $1.10 | $1.10 |
| J3 | USB4135-GF-A | USB-C, 16-pin, SMT | 1 | $0.45 | $0.45 |
| J4 | B4B-XH-A | I²C expansion, 4-pin | 1 | $0.12 | $0.12 |
| J5 | B2B-XH-A | Fan header, 2-pin | 1 | $0.08 | $0.08 |
| J6 | TC2030-IDC-NL | Programming header, Tag-Connect | 1 | $0.85 | $0.85 |
| J7 | SM04B-SRSS-TB | Debug UART, 4-pin | 1 | $0.25 | $0.25 |
| SW1 | PTS645SM43SMTR92 | Reset button, tactile | 1 | $0.20 | $0.20 |

**Connectors Subtotal: $4.00**

## PCB & Assembly

| Item | Spec | Qty | Unit Cost | Total |
|------|------|:---:|-----------|-------|
| PCB | 4-layer, 2 oz Cu inner, ENIG, 1.6 mm | 1 | $8.00 | $8.00 |
| Passives | Resistors, caps, LEDs, ferrite beads, diodes | — | — | $3.00 |
| Heatsink | 40×40×20mm, 8°C/W for power FETs | 1 | $1.50 | $1.50 |
| Fan | 40×40×10mm, 12V (optional) | 1 | $2.00 | $2.00 |

**PCB & Assembly Subtotal: $14.50**

---

## Grand Total

| Category | Cost |
|----------|------|
| Power Stage | $24.50 |
| Gate Drive | $2.88 |
| Sensing & Measurement | $6.54 |
| Control (MCU + FPGA) | $8.50 |
| Power Supplies | $2.20 |
| Connectors & Mechanical | $4.00 |
| PCB & Assembly | $14.50 |
| **Total (1 unit)** | **~$63.12** |
| **Total (100 units)** | **~$42.00** |
| **Total (1000 units)** | **~$32.00** |

> **Note:** This BOM is for the SUNFLEX-HC Revision A hardware (ShrikeFi platform). See [`docs/bom.md`](../docs/bom.md) for the full BOM with manufacturer part numbers, alternative parts, and ordering links.
