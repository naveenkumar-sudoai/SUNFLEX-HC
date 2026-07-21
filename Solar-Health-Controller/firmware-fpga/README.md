# Firmware – FPGA (Phase 2)

## Overview
VHDL-based control core targeting Lattice iCE40HX4K (prototyping) and Xilinx Spartan-7 (production). Migrates the STM32 control loops from software to dedicated hardware for deterministic sub-microsecond response.

## Directory Structure
```
firmware-fpga/
├── vhdl/
│   ├── top.vhd                # Top-level entity
│   ├── mppt_engine.vhd        # P&O MPPT in hardware
│   ├── fppt_engine.vhd        # Constant-power PI controller
│   ├── pwm_generator.vhd      # High-resolution PWM (250 ps res.)
│   ├── fuzzy_supervisor.vhd   # Fuzzy inference pipeline
│   ├── battery_health.vhd     # IR estimation FSM
│   ├── adc_interface.vhd      # SPI ADC controller
│   ├── i2c_master.vhd         # INA226 communication
│   ├── uart_debug.vhd         # Debug CLI over UART
│   └── tb_top.vhd             # Top-level testbench
├── constraints/
│   ├── ice40hx4k.pcf           # Lattice iCE40 pin constraints
│   └── spartan7.xdc            # Xilinx Spartan-7 constraints
└── README.md
```

## Simulation
```bash
# Analyze all sources
ghdl -a --work=work vhdl/*.vhd

# Elaborate testbench
ghdl -e --work=work tb_top

# Run simulation
ghdl -r --work=work tb_top --vcd=wave.vcd --stop-time=10ms

# View waveforms
gtkwave wave.vcd
```

## Synthesis (iCE40)
```bash
# Yosys + nextpnr flow
yosys -p "synth_ice40 -top top -json build/top.json" vhdl/*.vhd
nextpnr-ice40 --hx4k --package tq144 --json build/top.json --pcf constraints/ice40hx4k.pcf --asc build/top.asc
icepack build/top.asc build/top.bin
iceprog build/top.bin
```

## Key Performance Targets
| Metric | STM32 (Phase 1) | FPGA (Phase 2 Target) |
|--------|-----------------|----------------------|
| PWM Resolution | 16-bit (HRTIM) | 20-bit (250 ps) |
| MPPT Loop Latency | ~100 µs | <1 µs |
| Fuzzy Eval Latency | ~500 µs | <5 µs |
| ADC Sampling | 1 kHz | Up to 1 MHz |

## Block Diagram (FPGA Internal)
```
  ADC Data ──→ Filter ──→ MPPT Engine ──→ Mode Mux ──→ PWM Gen ──→ Gate Driver
                 │            │               ↑
                 └──→ FPPT Engine ────────────┤
                              │               │
                 ┌──→ Battery Health ───→ Fuzzy Supervisor
                 │            │
              I2C Bus ──→ INA226 Reader
```
