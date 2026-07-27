# SUNFLEX-HC FPGA Logic (Renesas SLG47910V)

## Overview

This directory contains the FPGA logic for the SUNFLEX-HC controller, implemented on the **Renesas SLG47910V GreenPAK™** programmable mixed-signal device. The FPGA provides hard real-time control with sub-microsecond response — operations that would be impossible on a microcontroller alone.

The SLG47910V is configured by the ESP32-S3 via I²C at boot and during operation for dynamic parameter updates.

## Architecture

```
fpga/
├── src/                              # VHDL/Verilog source files
│   ├── top_level.vhd                 # Top-level entity (instantiates all modules)
│   ├── pwm_generator.vhd             # 8-channel phase-shifted PWM
│   ├── ripple_canceller.vhd          # Active ripple cancellation DSP
│   ├── dead_time_controller.vhd      # Adaptive dead-time insertion
│   ├── adc_interface.vhd             # High-speed ADC reader (1 MSPS)
│   ├── i2c_slave.vhd                 # I²C slave register interface
│   └── fault_handler.vhd             # Hardware-level fault protection
├── simulation/                       # Testbenches
│   ├── tb_pwm_generator.vhd
│   ├── tb_ripple_canceller.vhd
│   ├── tb_dead_time.vhd
│   ├── tb_i2c_slave.vhd
│   └── tb_top_level.vhd
├── constraints/                      # Physical constraints
│   └── sunflex_hc.pinout.sdc         # Pin assignments and timing constraints
├── Makefile                          # Simulation flow automation
└── README.md                         # This file
```

## Module Descriptions

### PWM Generator (`pwm_generator.vhd`)

Generates 8 independent PWM channels with:
- **Frequency:** 100–400 kHz (programmable via I²C register)
- **Resolution:** 0.5 ns (using delay-line interpolation → ~13-bit effective at 250 kHz)
- **Phase offset:** Per-channel programmable (0–360°)
- **Duty cycle:** 0–100% per channel
- **Update mechanism:** Double-buffered — glitch-free transitions

### Ripple Canceller (`ripple_canceller.vhd`)

Active output ripple cancellation:
- Monitors output voltage ripple via dedicated high-speed ADC input
- Computes anti-phase correction signal using LMS adaptive filter
- Drives an auxiliary MOSFET to inject canceling current into the output filter
- Achieves 10–20 dB ripple reduction (< 1% peak-to-peak residual)

### Dead-Time Controller (`dead_time_controller.vhd`)

Prevents shoot-through in half-bridge configurations:
- Adaptive dead time based on MOSFET temperature, gate drive strength, and load current
- Default: 50 ns, range: 20–200 ns
- Per-channel independent dead-time settings

### I²C Slave (`i2c_slave.vhd`)

Communication interface with ESP32-S3:
- 7-bit address: `0x2A` (default, configurable)
- Speed: 400 kHz (Fast Mode)
- 64 × 16-bit register file
- Register map defined in `fpga_comm.h` (firmware side)

### Fault Handler (`fault_handler.vhd`)

Hardware-level safety (< 1 μs response):
- Over-current: Cycle-by-cycle comparator trip
- Over-temperature: External NTC comparator
- Emergency shutdown: Latches all PWM outputs LOW, asserts FAULT_n to MCU
- Auto-retry after programmable delay

## Prerequisites

### Simulation
- [GHDL](https://github.com/ghdl/ghdl) (≥ 3.0) — open-source VHDL simulator
- [GTKWave](https://gtkwave.sourceforge.net/) — waveform viewer

### Synthesis / Bitstream Generation
- [Renesas GreenPAK Designer](https://www.renesas.com/software-tool/greenpak-designer) (free, Windows/Linux via Wine)

## Quick Start

```bash
# Compile all VHDL sources
cd fpga
make compile

# Run all testbenches
make sim

# Check assertions pass
make check

# View waveforms
make view

# Clean build artifacts
make clean
```

### Running Individual Testbenches

```bash
# PWM generator
make tb_pwm

# Ripple canceller
make tb_ripple

# I²C slave
make tb_i2c

# Full top-level
make tb_top
```

## Register Map

The FPGA is controlled via a 64-register I²C register file:

| Address | Name | Bits | Access | Description |
|:-------:|------|:----:|:------:|-------------|
| `0x00` | `FPGA_ID` | 16 | R | Device ID (`0x4791`) |
| `0x01` | `FPGA_VERSION` | 16 | R | Firmware version (BCD) |
| `0x02` | `FPGA_STATUS` | 16 | R | Fault flags, mode, temperature |
| `0x10` | `PWM_FREQ` | 12 | R/W | Switching frequency (Hz) |
| `0x11–0x14` | `PWM_DUTY[0:3]` | 12 | R/W | Duty cycle per channel (0–4095) |
| `0x15–0x18` | `PWM_PHASE[0:3]` | 12 | R/W | Phase offset per channel (0–4095) |
| `0x20–0x23` | `DEAD_TIME[0:3]` | 8 | R/W | Dead time per channel (ns) |
| `0x30` | `RIPPLE_GAIN` | 8 | R/W | Cancellation loop gain |
| `0x31` | `RIPPLE_AMPLITUDE` | 12 | R | Measured ripple amplitude (RMS) |
| `0x40` | `FAULT_MASK` | 8 | R/W | Fault interrupt enable mask |
| `0x41` | `FAULT_STATUS` | 8 | R | Latched fault status |
| `0x42` | `FAULT_RETRY_MS` | 16 | R/W | Auto-retry delay (ms) |
| `0xFF` | `SOFT_RESET` | 16 | W | Write `0xA5A5` to reset all registers |

## Coding Style

See [`community/CONTRIBUTING.md`](../community/CONTRIBUTING.md) for the full VHDL style guide.

Key points:
- 4-space indentation
- Prefix naming: `G_` generics, `C_` constants, `_i` in, `_o` out, `_s` internal
- One entity/architecture per file
- Synchronous resets (active low: `rst_n_i`)
- No latches — all combinational processes must be complete

## License

MIT License — see [LICENSE](../LICENSE) for details.
