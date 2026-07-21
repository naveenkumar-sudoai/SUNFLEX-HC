# System Architecture

## Overview

The Solar Health Controller is a hybrid charge controller that intelligently selects between MPPT, FPPT, and PWM operating modes. This document describes the system-level architecture, data flow, and control hierarchy.

## Block Diagram

<!-- Insert detailed block diagram here -->

## Control Hierarchy

```
Level 1: Fuzzy Supervisor
  ├── Inputs:  SoH, SoC, P_load, P_solar_available
  ├── Outputs: Operating Mode (MPPT | FPPT | PWM)
  └── Period:  Every 1 second

Level 2: Mode-Specific Controller
  ├── MPPT: Perturb & Observe / Incremental Conductance
  ├── FPPT: Constant-Power PI Control Loop
  └── PWM:  Fixed Duty-Cycle Lookup Table

Level 3: Hardware Abstraction
  ├── ADC Driver (V/I sensing)
  ├── HRTIM Driver (PWM generation)
  ├── I2C Driver (INA226 communication)
  └── UART Driver (Debug / manual override)
```

## Data Flow

1. INA226 sensors sample panel V/I and battery V/I at 1 kHz
2. Samples are low-pass filtered (moving average, N=16)
3. Filtered values feed the mode-specific control loop
4. Every 1 s, the fuzzy supervisor re-evaluates the operating mode
5. PWM duty cycle is updated at the control loop rate (100 Hz for MPPT, 10 Hz for FPPT)

## Key Components

| Component | Part Number | Role |
|-----------|------------|------|
| MCU | STM32F407VGT6 | Control core (Phase 1) |
| FPGA | Lattice iCE40HX4K | Control core (Phase 2) |
| Gate Driver | IR2110 | High/low-side MOSFET drive |
| Current Sensor | INA226 | Bidirectional I²C power monitor |
| Buck MOSFETs | IRFZ44N / CSD19536 | Synchronous buck switches |
| Inductor | 100 µH, 10 A | Buck converter energy storage |

## Fuzzy Logic Details

- **Type**: Mamdani fuzzy inference
- **Inputs**: 4 (SoH, SoC, P_load, P_solar)
- **Outputs**: 1 (Mode selection, defuzzified via centroid)
- **Membership Functions**: Triangular and trapezoidal
- **Rule Base**: ~27 rules (see firmware for full rule table)

## References

- [Perturb & Observe MPPT – IEEE Paper](references.md)
- [Battery SoH Estimation via Internal Resistance](references.md)
- [Mamdani Fuzzy Inference Systems](references.md)
