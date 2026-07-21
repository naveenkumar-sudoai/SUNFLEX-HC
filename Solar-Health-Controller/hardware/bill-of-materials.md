# Bill of Materials (BOM)

## Power Stage

| Ref | Part | Description | Qty | Unit Cost | Total |
|-----|------|-------------|-----|-----------|-------|
| Q1, Q2 | CSD19536KCS | N-Ch MOSFET, 100V, 150A, TO-220 | 2 | $3.50 | $7.00 |
| Q3, Q4 | IRFZ44N | N-Ch MOSFET, 55V, 49A, TO-220 | 2 | $0.80 | $1.60 |
| L1 | 100µH/10A | Toroidal power inductor | 1 | $4.00 | $4.00 |
| C1, C2 | 470µF/63V | Electrolytic, low ESR | 2 | $1.20 | $2.40 |
| C3, C4 | 10µF/50V | MLCC, X7R, 1206 | 2 | $0.15 | $0.30 |
| D1 | MBR2045CT | Schottky, 45V, 20A, TO-220 | 1 | $1.50 | $1.50 |

## Gate Drive

| Ref | Part | Description | Qty | Unit Cost | Total |
|-----|------|-------------|-----|-----------|-------|
| U1 | IR2110 | High/low-side gate driver, DIP-14 | 1 | $2.50 | $2.50 |
| C5, C6 | 10µF/25V | Bootstrap capacitor | 2 | $0.15 | $0.30 |
| D2, D3 | 1N4148 | Bootstrap diode | 2 | $0.05 | $0.10 |

## Sensing

| Ref | Part | Description | Qty | Unit Cost | Total |
|-----|------|-------------|-----|-----------|-------|
| U2, U3 | INA226 | I²C power monitor | 2 | $3.00 | $6.00 |
| R1, R2 | 0.01Ω/1% | Current sense shunt, 2512 | 2 | $0.50 | $1.00 |
| R3, R4 | 10kΩ/1% | Voltage divider (top) | 2 | $0.05 | $0.10 |
| R5, R6 | 1kΩ/1% | Voltage divider (bottom) | 2 | $0.05 | $0.10 |

## Control (Phase 1 – STM32)

| Ref | Part | Description | Qty | Unit Cost | Total |
|-----|------|-------------|-----|-----------|-------|
| U4 | STM32F407VGT6 | ARM Cortex-M4, LQFP-100 | 1 | $12.00 | $12.00 |
| Y1 | 8 MHz | Crystal, HC-49S | 1 | $0.30 | $0.30 |
| C7, C8 | 22pF | Load capacitor, 0805 | 2 | $0.05 | $0.10 |

## Control (Phase 2 – FPGA)

| Ref | Part | Description | Qty | Unit Cost | Total |
|-----|------|-------------|-----|-----------|-------|
| U5 | iCE40HX4K-TQ144 | Lattice FPGA | 1 | $8.00 | $8.00 |
| U6 | W25Q32JV | 32Mb SPI Flash | 1 | $0.50 | $0.50 |

## Miscellaneous

| Ref | Part | Description | Qty | Unit Cost | Total |
|-----|------|-------------|-----|-----------|-------|
| - | PCB | 2-layer, 100x80mm, FR4 | 1 | $5.00 | $5.00 |
| - | Terminal blocks | 2-pin, 5.08mm pitch | 4 | $0.30 | $1.20 |
| - | Heatsink | TO-220, 20°C/W | 3 | $0.80 | $2.40 |
| - | Fan | 40x40x10mm, 12V | 1 | $2.00 | $2.00 |

**Estimated Total (Phase 1):** ~$56.00
