# SUNFLEX-HC Firmware (ESP32-S3)

## Overview

This directory contains the ESP32-S3 firmware that implements the high-level control logic for SUNFLEX-HC. The firmware handles battery management, the Health-First FPPT algorithm, WiFi/BLE connectivity, and communication with the SLG47910V FPGA via I²C.

## Architecture

```
firmware/
├── platformio.ini          # PlatformIO project configuration
├── partitions_custom.csv   # Custom flash partition table (16 MB)
├── src/                    # Source files
│   ├── main.cpp            # Entry point — FreeRTOS task initialization
│   ├── fppt_engine.cpp     # Health-First FPPT algorithm
│   ├── battery_manager.cpp # Battery auto-detection, charging profiles
│   ├── adc_manager.cpp     # Multi-channel ADC sampling (DMA, 8 channels)
│   ├── fpga_comm.cpp       # I²C communication driver for SLG47910V
│   ├── soh_monitor.cpp     # SOH estimation (DC-IR, Coulomb counting)
│   ├── protection.cpp      # Fault detection and safety enforcement
│   ├── wifi_manager.cpp    # WiFi AP/STA mode management, captive portal
│   ├── mqtt_client.cpp     # MQTT telemetry + Home Assistant auto-discovery
│   ├── ota_manager.cpp     # Over-the-air firmware updates
│   └── data_logger.cpp     # Onboard data logging to SPIFFS
├── inc/                    # Header files
│   ├── config.h            # User-configurable defaults
│   ├── fppt_engine.h       # FPPT algorithm interface
│   ├── battery_manager.h   # Battery profiles and detection
│   ├── adc_manager.h       # ADC configuration and calibration
│   ├── fpga_comm.h         # FPGA register map and I²C protocol
│   ├── soh_monitor.h       # SOH estimation constants
│   ├── protection.h        # Fault thresholds and recovery logic
│   ├── wifi_manager.h      # WiFi configuration
│   └── pinout.h            # ESP32-S3 GPIO assignments
└── test/                   # Unit tests (PlatformIO native)
    ├── test_fppt_engine/
    ├── test_battery_manager/
    └── test_protection/
```

## Prerequisites

- [PlatformIO IDE](https://platformio.org/install) (recommended) or PlatformIO Core CLI
- USB-C cable for programming
- SUNFLEX-HC hardware (Rev A or prototype)

## Quick Start

```bash
# Install PlatformIO (if not already installed)
pip install platformio

# Build the firmware
cd firmware
platformio run

# Flash to ESP32-S3
platformio run --target upload

# Monitor serial output
platformio device monitor

# Run unit tests (native, no hardware needed)
platformio test -e native

# Run integration tests (requires hardware)
platformio test -e esp32-s3-devkitc-1
```

## Configuration

User-configurable settings are in `inc/config.h`. Key parameters:

```c
// Battery defaults (auto-detected but can be overridden)
#define BATTERY_DEFAULT_CHEMISTRY   BATTERY_AUTO
#define BATTERY_DEFAULT_VOLTAGE     BATTERY_AUTO

// Charging limits
#define CHARGE_CURRENT_MAX_A        20.0f   // Hardware maximum
#define CHARGE_CURRENT_DEFAULT_A    10.0f   // Conservative default

// FPPT Health-First Parameters
#define FPPT_ENABLE_SOH_LIMIT       true
#define SOH_DERATE_EXPONENT         1.5f
#define SOH_MIN_PERCENT             40.0f

// Protection Thresholds
#define OV_INPUT_V                  155.0f
#define OV_OUTPUT_V                 60.0f
#define OC_OUTPUT_A                 22.0f
#define OT_FET_C                    100.0f
#define OT_BATTERY_C                55.0f

// WiFi
#define WIFI_HOSTNAME               "sunflex-hc"
#define WIFI_AP_SSID                "SUNFLEX-HC-Setup"
```

## Build Flags

| Flag | Description |
|------|-------------|
| `-DFPPT_SOH_DERATE_EXPONENT=1.5` | SOH derating curve exponent |
| `-DCHARGE_CURRENT_MAX_A=20.0` | Maximum charge current (hardware limit) |
| `-DBOARD_HAS_PSRAM` | Enable external PSRAM (required for web assets) |
| `-DTESTING` | Enable test mode (native environment only) |

## Coding Style

See [`community/CONTRIBUTING.md`](../community/CONTRIBUTING.md) for the full C/C++ style guide.

Key points:
- 4-space indentation
- `snake_case` for variables and functions
- `PascalCase` for classes
- Doxygen comments on all public functions
- No dynamic memory allocation in the control loop (stack only)

## License

MIT License — see [LICENSE](../LICENSE) for details.
