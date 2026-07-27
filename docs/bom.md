# SUNFLEX-HC Bill of Materials (BOM) — Revision A

> Complete component list for PCB assembly. Prices are approximate in USD (Q1 2025).
> See [design-calculations.md](design-calculations.md) for component selection justifications.

---

## BOM Summary

| Category | Line Items | Cost (1 unit) | Cost (100 units) | Cost (1000 units) |
|----------|:---------:|:-------------:|:----------------:|:-----------------:|
| Power Stage | 8 | $18.50 | $12.30 | $9.85 |
| Gate Drive | 6 | $4.20 | $2.80 | $2.15 |
| Sensing & Measurement | 8 | $3.80 | $2.40 | $1.90 |
| Control (MCU + FPGA) | 4 | $8.50 | $6.20 | $5.10 |
| Power Supplies | 6 | $5.00 | $3.50 | $2.80 |
| Connectors | 8 | $4.00 | $2.80 | $1.95 |
| PCB | 1 | $8.00 | $4.50 | $3.20 |
| Passives | 25 | $3.00 | $1.80 | $1.20 |
| **Total** | **66** | **~$55.00** | **~$36.30** | **~$28.15** |

---

## Power Stage

| Ref | Part | Value/Spec | Manufacturer | MPN | Qty | Unit Price | Supplier |
|-----|------|-----------|--------------|-----|:--:|:----------:|----------|
| Q1–Q4 | N-Ch MOSFET | 250V, 36A, 10.5 mΩ | Infineon | BSC105N25NS3 | 4 | $1.85 | Digi-Key |
| L1 | Power Inductor | 100 μH, 25A Isat | Bourns | SRP1770TA-101M | 1 | $5.20 | Mouser |
| C1–C3 | MLCC | 22 μF, 200V, X7R, 1210 | TDK | C5750X7R2E226M | 3 | $0.95 | LCSC |
| C4–C5 | Alu Electrolytic | 100 μF, 200V, low ESR | Rubycon | 200TXW100MEFC | 2 | $1.45 | LCSC |
| C6–C7 | MLCC | 47 μF, 100V, X7R, 1210 | Murata | GRM32ER72A476KE20 | 2 | $0.65 | LCSC |
| C8–C9 | Alu Polymer | 220 μF, 63V, 18 mΩ | Panasonic | 63SVPF220M | 2 | $1.80 | LCSC |
| F1 | Fuse | 25A, 250V, automotive | Littelfuse | 0997020.WXN | 1 | $0.40 | Mouser |
| D1 | TVS Diode | 150V, bidirectional (input protection) | STMicro | SM15T150CA | 1 | $0.85 | Digi-Key |

**Subtotal (1 unit): $18.50**

---

## Gate Drive

| Ref | Part | Value/Spec | Manufacturer | MPN | Qty | Unit Price | Supplier |
|-----|------|-----------|--------------|-----|:--:|:----------:|----------|
| U1–U2 | Half-Bridge Driver | 1A, 100V bootstrap | TI | LM5109BMAX | 2 | $1.25 | Digi-Key |
| C_BOOT | MLCC | 100 nF, 25V, X7R, 0603 | Samsung | CL10B104KA8NNNC | 4 | $0.02 | LCSC |
| C_BOOT_BULK | MLCC | 1 μF, 25V, X7R, 0805 | TDK | C2012X7R1E105K | 2 | $0.05 | LCSC |
| D_BOOT | Schottky | 200V, 1A | Diodes Inc | BAV21W-7-F | 2 | $0.08 | LCSC |
| R_GATE | Chip Resistor | 4.7 Ω, 0805, 0.25W | Yageo | RC0805FR-074R7L | 4 | $0.01 | LCSC |

**Subtotal (1 unit): $4.20**

---

## Sensing & Measurement

| Ref | Part | Value/Spec | Manufacturer | MPN | Qty | Unit Price | Supplier |
|-----|------|-----------|--------------|-----|:--:|:----------:|----------|
| U3–U4 | Current Sense Amp | INA229, 20-bit, SPI | TI | INA229AIDGSR | 2 | $2.35 | Digi-Key |
| R_SHUNT | Shunt Resistor | 5 mΩ, 10W, 1%, 3920 | Bourns | CSS2H-3920R-5L00F | 2 | $0.65 | Mouser |
| R_DIV_HV | Chip Resistor | 100 kΩ, 1206, 0.25W, 1% | Yageo | RC1206FR-07100KL | 2 | $0.03 | LCSC |
| R_DIV_LV1 | Chip Resistor | 2.2 kΩ, 0603, 0.1W, 1% | Yageo | RC0603FR-072K2L | 1 | $0.01 | LCSC |
| R_DIV_LV2 | Chip Resistor | 2.7 kΩ, 0603, 0.1W, 1% | Yageo | RC0603FR-072K7L | 1 | $0.01 | LCSC |
| TH1–TH3 | NTC Thermistor | 10 kΩ, B=3435, 0603 | Murata | NCP15XH103F03RC | 3 | $0.12 | LCSC |
| R_NTC_BIAS | Chip Resistor | 10 kΩ, 0603, 0.1W, 1% | Yageo | RC0603FR-0710KL | 3 | $0.01 | LCSC |

**Subtotal (1 unit): $3.80**

---

## Control (MCU + FPGA)

| Ref | Part | Value/Spec | Manufacturer | MPN | Qty | Unit Price | Supplier |
|-----|------|-----------|--------------|-----|:--:|:----------:|----------|
| U5 | MCU Module | ESP32-S3-WROOM-1, 16 MB Flash, 8 MB PSRAM | Espressif | ESP32-S3-WROOM-1-N16R8 | 1 | $3.50 | LCSC |
| U6 | FPGA | SLG47910V, GreenPAK, 960 LUTs | Renesas | SLG47910V | 1 | $4.20 | Digi-Key |
| Y1 | Crystal | 40 MHz, 10 ppm, 3225 | Abracon | ABM8G-40.000MHZ | 1 | $0.35 | LCSC |
| ANT1 | Ceramic Antenna | 2.4 GHz, 3 dBi | Johanson | 2450AT18A100E | 1 | $0.45 | LCSC |

**Subtotal (1 unit): $8.50**

---

## Power Supplies

| Ref | Part | Value/Spec | Manufacturer | MPN | Qty | Unit Price | Supplier |
|-----|------|-----------|--------------|-----|:--:|:----------:|----------|
| U7 | High-Voltage LDO | LR8, 13.2–450V → 12V, 30 mA | Microchip | LR8N8-G | 1 | $0.85 | Digi-Key |
| U8 | Buck Regulator | AP63205, 12V → 5V, 2A | Diodes Inc | AP63205WU-7 | 1 | $0.55 | LCSC |
| U9 | LDO | AMS1117-3.3, 5V → 3.3V, 1A | AMS | AMS1117-3.3 | 1 | $0.12 | LCSC |
| U10 | LDO | AP2112K-1.8, 5V → 1.8V, 600 mA | Diodes Inc | AP2112K-1.8TRG1 | 1 | $0.18 | LCSC |
| C_PS_IN | MLCC | 10 μF, 25V, X7R, 0805 | Samsung | CL21B106KOQNNNE | 4 | $0.04 | LCSC |
| C_PS_OUT | MLCC | 22 μF, 10V, X7R, 0805 | Samsung | CL21B226KPQNNNE | 6 | $0.05 | LCSC |

**Subtotal (1 unit): $5.00**

---

## Connectors

| Ref | Part | Value/Spec | Manufacturer | MPN | Qty | Unit Price | Supplier |
|-----|------|-----------|--------------|-----|:--:|:----------:|----------|
| J1 | Solar Input | 2-pin, 5.08 mm, 30A | Phoenix Contact | 1754465 | 1 | $0.95 | Digi-Key |
| J2 | Battery Output | 2-pin, 7.62 mm, 30A | Phoenix Contact | 1714981 | 1 | $1.10 | Digi-Key |
| J3 | USB-C | 16-pin, SMT | GCT | USB4135-GF-A | 1 | $0.45 | LCSC |
| J4 | I²C Expansion | 4-pin, 2.54 mm | JST | B4B-XH-A(LF)(SN) | 1 | $0.12 | LCSC |
| J5 | Fan Header | 2-pin, 2.54 mm | JST | B2B-XH-A(LF)(SN) | 1 | $0.08 | LCSC |
| J6 | Programming | 6-pin, 1.27 mm, Tag-Connect | Tag-Connect | TC2030-IDC-NL | 1 | $0.85 | Tag-Connect |
| J7 | Debug UART | 4-pin, 1.27 mm | JST | SM04B-SRSS-TB | 1 | $0.25 | LCSC |
| SW1 | Reset Button | Tactile, 3.5×6 mm, SMT | C&K | PTS645SM43SMTR92 | 1 | $0.20 | LCSC |

**Subtotal (1 unit): $4.00**

---

## PCB

| Ref | Part | Value/Spec | Qty | Unit Price | Supplier |
|-----|------|-----------|:--:|:----------:|----------|
| — | PCB | 4-layer, 2 oz Cu inner, ENIG, 1.6 mm, green | 1 | $8.00 | JLCPCB |

**Subtotal (1 unit): $8.00 (qty 1); $4.50 (qty 100)**

---

## Passives

| Qty | Value | Package | Description |
|:---:|-------|---------|-------------|
| 4 | 100 nF | 0603 | Decoupling (MCU, FPGA) |
| 4 | 10 nF | 0603 | Decoupling (analog) |
| 2 | 1 nF | 0603 | Compensation |
| 4 | 10 kΩ | 0603 | Pull-up / bias |
| 2 | 100 kΩ | 0603 | Pull-down |
| 4 | 0 Ω | 1206 | Jumpers / config |
| 2 | Ferrite Bead | 0805 | 600Ω @ 100 MHz |
| 2 | LED (Green, Red) | 0603 | Status indicators |
| 1 | 3.3V Zener | SOD-323 | ESD protection |

**Subtotal (1 unit): $3.00**

*Note: Passive components use commodity parts from Yageo, Samsung, TDK, or equivalent. Exact MPNs not listed as they are interchangeable.*

---

## Assembly Notes

### Critical Components (No Substitutions)
- Q1–Q4 (Power MOSFETs): Must use BSC105N25NS3 or direct equivalent with R_DS(on) ≤ 12 mΩ @ 25°C
- L1 (Inductor): Saturation current must exceed 25A at 100°C
- U6 (FPGA): Only SLG47910V; no pin-compatible alternative

### Acceptable Substitutions
- Current sense: INA226 (16-bit) if INA229 is unavailable; update firmware ADC gain
- Gate driver: IR2110S if LM5109B is unavailable; increase bootstrap cap to 220 nF
- Connectors: Any 5.08 mm terminal block rated ≥ 30A
- Passives: Any X7R dielectric, ≥ rated voltage, same package

### Not Recommended
- Lower-voltage MOSFETs (< 200V V_DS): Risk of avalanche breakdown with 150V panels in cold weather (Voc increases ~0.3%/°C below 25°C)
- Smaller inductor (< 68 μH): Increased ripple, potential saturation at high load

---

## Ordering Links

### Single-Unit Build (Digi-Key / Mouser / LCSC)
Estimated total: **$55–65** (depending on shipping and current stock)

### 100-Unit Build (JLCPCB Assembly + LCSC)
Estimated total: **$36/unit** (PCB + assembly + components)

### Kit Sourcing
We plan to offer partial kits (PCB + programmed MCU + FPGA + critical components) at ~$25 via the project store.

---

*Last updated: July 2025*
