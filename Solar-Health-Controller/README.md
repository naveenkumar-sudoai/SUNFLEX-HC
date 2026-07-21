# Solar Health Controller

A hybrid solar charge controller that intelligently switches between MPPT, FPPT, and PWM modes based on real-time battery health monitoring to extend battery lifespan by 30-50%.

## Features
- **MPPT (Maximum Power Point Tracking)** – Extracts maximum available power from the solar array under varying irradiance and temperature conditions.
- **FPPT (Fixed Power Point Tracking)** – Clamps output to a configurable power limit when the battery is near full charge, reducing stress on the cells.
- **PWM (Pulse Width Modulation)** – Falls back to simple PWM regulation for trickle/top-off charging, minimizing gassing and thermal degradation.
- **Battery Health Monitoring** – Continuously estimates internal resistance (DC-IR) to track State of Health (SoH) in real time.
- **Fuzzy Logic Mode Switching** – A fuzzy inference engine selects the optimal operating mode based on battery SoH, state of charge, load demand, and solar availability.
- **Manual Override** – User-selectable fixed-mode operation via onboard switch or serial command.
- **FPGA-Ready Control Core** – Control algorithms are designed for eventual migration from STM32 to FPGA (Lattice iCE40 / Xilinx Spartan-7).
- **Active Voltage Ripple Reduction** – Synchronous buck converter with adaptive dead-time control minimizes input/output ripple.

## Architecture
<!-- Add your block diagram here -->
```
   +-----------+       +-----------------+       +-----------+
   |  Solar    | ----> |  Buck Converter  | ----> |  Battery  |
   |  Panel    |       |  (Synchronous)   |       |  (Li-ion  |
   +-----------+       +-----------------+       |  / LiFePO4)|
                           |      |              +-----------+
                   +--------+      +--------+
                   |                        |
           +-------v-------+       +-------v-------+
           |  Gate Driver  |       |  V/I Sensors   |
           |  (IR2110)     |       |  (INA226)      |
           +-------+-------+       +-------+-------+
                   |                        |
           +-------v------------------------v-------+
           |           STM32F407 / FPGA            |
           |   ┌─────────────────────────────────┐ |
           |   │  Fuzzy Logic Controller          │ |
           |   │  Mode Select (MPPT/FPPT/PWM)     │ |
           |   │  MPPT Algorithm (P&O / InCond)   │ |
           |   │  Battery Health Estimator        │ |
           |   │  PWM Generator (HRTIM)           │ |
           |   └─────────────────────────────────┘ |
           +---------------------------------------+
```

## Project Status
| Phase | Description | Status |
|-------|-------------|--------|
| Phase 1 | STM32 Prototype | 🟡 In Progress |
| Phase 2 | FPGA Migration | ⚪ Planned |
| Phase 3 | PCB Design | ⚪ Planned |

## Repository Structure
```
Solar-Health-Controller/
├── README.md
├── docs/
│   ├── project-overview.pdf
│   ├── system-architecture.md
│   └── references.md
├── hardware/
│   ├── schematics/
│   ├── pcb-design/
│   └── bill-of-materials.md
├── firmware-stm32/
│   ├── src/
│   ├── inc/
│   └── README.md
├── firmware-fpga/
│   ├── vhdl/
│   ├── constraints/
│   └── README.md
├── simulation/
│   ├── matlab/
│   └── ltspice/
└── results/
    ├── oscilloscope-screenshots/
    └── test-data/
```

## Documentation
- [Project Overview](docs/project-overview.pdf)
- [System Architecture](docs/system-architecture.md)
- [References & Citations](docs/references.md)

## Getting Started

### Prerequisites
- **STM32CubeIDE** (for firmware-stm32)
- **GHDL + GtkWave** or **ModelSim** (for firmware-fpga VHDL simulation)
- **MATLAB / Simulink** (for control system simulation)
- **LTSpice** (for power-stage simulation)

### Building the STM32 Firmware
```bash
cd firmware-stm32
# Open STM32CubeIDE and import the project, or use the Makefile:
make -j$(nproc)
```

### Simulating the FPGA Design
```bash
cd firmware-fpga/vhdl
ghdl -a --work=work *.vhd
ghdl -e --work=work tb_top
ghdl -r --work=work tb_top --vcd=wave.vcd
gtkwave wave.vcd
```

## How It Works

1. **Sensing** – Voltage and current are sampled on both the panel and battery sides via INA226 I²C power monitors at 1 kHz.
2. **Health Estimation** – Battery internal resistance is estimated periodically using a low-current pulse technique; SoH is derived from the ratio of initial IR to current IR.
3. **Fuzzy Inference** – A Mamdani-type fuzzy controller takes SoH, SoC, load demand, and available solar power as inputs and outputs the recommended operating mode (MPPT / FPPT / PWM).
4. **Control Execution** – The selected mode's control law (P&O for MPPT, constant-power PI loop for FPPT, duty-cycle lookup for PWM) drives the synchronous buck converter via high-resolution PWM.

## License
This project is dual-licensed under:

- [MIT License](https://opensource.org/licenses/MIT)
- [Apache License 2.0](https://www.apache.org/licenses/LICENSE-2.0)

You may choose either license at your option.

---

*Built with ☀️ for longer-lasting off-grid energy storage.*
