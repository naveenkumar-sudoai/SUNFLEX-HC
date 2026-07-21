# Firmware – STM32 (Phase 1)

## Overview
STM32F407-based firmware implementing MPPT, FPPT, and PWM control loops with fuzzy-logic mode supervision.

## Directory Structure
```
firmware-stm32/
├── src/
│   ├── main.c              # Entry point, system init
│   ├── mppt.c              # P&O / InCond MPPT algorithm
│   ├── fppt.c              # Fixed-power PI control loop
│   ├── pwm_mode.c          # Fixed duty-cycle PWM
│   ├── fuzzy_logic.c       # Mamdani fuzzy inference engine
│   ├── battery_health.c    # Internal resistance estimation
│   ├── sensors.c           # INA226 driver (I2C)
│   ├── hrtim.c             # High-resolution PWM driver
│   └── uart_cmd.c          # Manual override / debug CLI
├── inc/
│   ├── mppt.h
│   ├── fppt.h
│   ├── pwm_mode.h
│   ├── fuzzy_logic.h
│   ├── battery_health.h
│   ├── sensors.h
│   ├── hrtim.h
│   └── uart_cmd.h
└── README.md
```

## Build
```bash
# Using STM32CubeIDE (recommended)
# File > Import > Existing Projects into Workspace

# Or with arm-none-eabi-gcc + Makefile
make -j$(nproc)
```

## Flash
```bash
# Via ST-Link
st-flash write build/firmware.bin 0x08000000

# Or via OpenOCD
openocd -f board/stm32f4discovery.cfg -c "program build/firmware.elf verify reset exit"
```

## Key Parameters
| Parameter | Value | Macro |
|-----------|-------|-------|
| PWM Frequency | 100 kHz | `PWM_FREQ_HZ` |
| MPPT Update Rate | 100 Hz | `MPPT_RATE_HZ` |
| FPPT Update Rate | 10 Hz | `FPPT_RATE_HZ` |
| Fuzzy Eval Period | 1 s | `FUZZY_PERIOD_S` |
| IR Estimation Interval | 60 s | `IR_EST_INTERVAL_S` |
| ADC Sampling Rate | 1 kHz | `ADC_SAMPLE_RATE_HZ` |

## Fuzzy Rule Base
See `src/fuzzy_logic.c` for the full 27-rule table. Summary:

| SoH | SoC | P_load | P_solar | → Mode |
|-----|-----|--------|---------|--------|
| GOOD | LOW | - | HIGH | MPPT |
| GOOD | HIGH | LOW | - | PWM |
| GOOD | MED | HIGH | HIGH | MPPT |
| WEAK | HIGH | LOW | - | PWM |
| WEAK | MED | - | - | FPPT |
| WEAK | LOW | - | HIGH | MPPT |
| POOR | - | - | - | FPPT |

## Debug CLI Commands
```
> mode mppt       # Force MPPT mode
> mode fppt 50    # Force FPPT with 50W limit
> mode pwm 75     # Force PWM at 75% duty
> mode auto       # Resume fuzzy auto-select
> health          # Print SoH, IR, cycle count
> sensors         # Print all V/I readings
> stats           # Print mode histogram
```
