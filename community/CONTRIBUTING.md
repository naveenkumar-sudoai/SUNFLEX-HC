# Contributing to SUNFLEX-HC

Thank you for your interest in contributing to SUNFLEX-HC! This document provides guidelines and workflows to help you contribute effectively.

---

## 📋 Table of Contents

1. [Code of Conduct](#code-of-conduct)
2. [How to Report Issues](#how-to-report-issues)
3. [How to Submit Pull Requests](#how-to-submit-pull-requests)
4. [Code Style Guidelines](#code-style-guidelines)
5. [Hardware Design Guidelines](#hardware-design-guidelines)
6. [Testing Requirements](#testing-requirements)
7. [Documentation Guidelines](#documentation-guidelines)
8. [Communication Channels](#communication-channels)
9. [Recognition](#recognition)

---

## Code of Conduct

This project and everyone participating in it is governed by the [SUNFLEX-HC Code of Conduct](CODE_OF_CONDUCT.md). By participating, you agree to uphold this code. Please report unacceptable behavior to [sunflex-hc-conduct@example.com](mailto:sunflex-hc-conduct@example.com).

---

## How to Report Issues

### Bug Reports

Use the **Bug Report** template when opening an issue. Include:

1. **Describe the bug** — Clear, concise description
2. **To Reproduce** — Step-by-step instructions:
   - Hardware revision (Rev A, prototype, etc.)
   - Firmware version (git commit hash)
   - Battery type and voltage
   - Solar panel specifications
   - Steps to trigger the bug
3. **Expected behavior** — What should have happened
4. **Screenshots / Logs** — Serial console output, oscilloscope screenshots, error messages
5. **Environment**:
   - OS: [e.g., Ubuntu 24.04, Windows 11, macOS 15]
   - PlatformIO version
   - Toolchain version
6. **Additional context** — Any other relevant information

### Feature Requests

Use the **Feature Request** template. Include:

1. **Problem statement** — What problem are you trying to solve?
2. **Proposed solution** — How should SUNFLEX-HC solve it?
3. **Alternatives considered** — What else have you tried or considered?
4. **Scope** — Does this affect hardware, firmware, FPGA, or software?
5. **Additional context** — Links, references, similar implementations

### Hardware Issues

**Safety first!** If you discover a hardware issue that could cause:
- Fire hazard
- Battery damage
- Electric shock risk
- Component failure that could cascade

Please email us directly at [sunflex-hc-safety@example.com](mailto:sunflex-hc-safety@example.com) **before** opening a public issue.

---

## How to Submit Pull Requests

### Workflow

1. **Fork the repository** and create your branch from `main`:
   ```bash
   git checkout -b feature/your-feature-name
   ```
   or
   ```bash
   git checkout -b fix/your-bug-fix
   ```

2. **Follow the branch naming convention:**
   - `feature/` — New features
   - `fix/` — Bug fixes
   - `docs/` — Documentation changes
   - `hw/` — Hardware design changes
   - `fpga/` — FPGA logic changes
   - `test/` — Test additions or changes

3. **Make your changes.** Keep commits atomic and well-described.

4. **Add or update tests** as appropriate.

5. **Update documentation** to reflect your changes.

6. **Run the test suite** (see [Testing Requirements](#testing-requirements)).

7. **Submit a Pull Request** to the `main` branch.

### PR Guidelines

- Keep PRs focused — one feature or bug fix per PR
- Reference related issues using `Closes #123` or `Related to #123`
- Include before/after comparisons if applicable
- For hardware changes, include screenshots of the schematic/PCB
- For firmware changes, include serial output showing the change works
- All CI checks must pass before merge

### Commit Messages

Follow the [Conventional Commits](https://www.conventionalcommits.org/) format:

```
<type>(<scope>): <description>

[optional body]

[optional footer(s)]
```

**Types:** `feat`, `fix`, `docs`, `style`, `refactor`, `perf`, `test`, `chore`, `hw`, `fpga`

**Scopes:** `firmware`, `fpga`, `hardware`, `docs`, `simulation`, `test`, `manufacturing`, `examples`

**Examples:**
```
feat(firmware): add SOH-aware current limiting to FPPT engine

Implement Coulomb counting and periodic DC-IR measurement to estimate
battery State of Health. FPPT algorithm now reduces charge current
proportionally as SOH degrades, preventing accelerated aging.

Closes #42
```

```
hw(schematic): replace IR2110 with LM5109 for better bootstrap performance

The LM5109 provides 1A peak gate drive vs 2A from IR2110 but has much
lower quiescent current (25µA vs 180µA), reducing standby power by 85%.

Related to #58
```

---

## Code Style Guidelines

### C/C++ (ESP32-S3 Firmware)

We follow the **ESP-IDF coding style** with some additions:

```cpp
/**
 * @brief Brief description of the function.
 *
 * @param param1 Description of first parameter
 * @param param2 Description of second parameter
 * @return Description of return value
 */
float calculate_soh(const BatteryState& state, float internal_resistance) {
    // Constants use UPPER_SNAKE_CASE
    const float IR_INITIAL = 0.025f; // ohms, measured at manufacture

    // Variables use snake_case
    float soh_percent = (IR_INITIAL / internal_resistance) * 100.0f;

    // Early returns for edge cases
    if (soh_percent > 100.0f) {
        return 100.0f;
    }

    return soh_percent;
}
```

**Key rules:**
- **Indentation:** 4 spaces (no tabs)
- **Line length:** 120 characters max
- **Naming:** `snake_case` for variables/functions, `PascalCase` for classes, `UPPER_SNAKE_CASE` for constants/macros
- **Comments:** Doxygen-style `/** ... */` for public APIs, `//` for inline
- **Braces:** Allman style (brace on new line) for functions, K&R for control flow
- **Header guards:** `#pragma once` (preferred) or `#ifndef PATH_TO_FILE_H_`
- **Includes:** Group in order: (1) related header, (2) C standard, (3) C++ standard, (4) ESP-IDF, (5) third-party, (6) project headers. Separate groups with blank lines.
- **Avoid:** `using namespace std;`, global variables, `new`/`delete` (use `std::unique_ptr` or stack allocation)

### VHDL (FPGA)

We follow the **Gaisler VHDL coding guidelines**:

```vhdl
--------------------------------------------------------------------------------
-- pwm_generator.vhd — 8-channel phase-shifted PWM generator
--
-- Generates 8 independent PWM channels with programmable frequency (100-400 kHz),
-- duty cycle (0-100%), phase offset, and dead-time insertion. All parameters are
-- updated synchronously via I²C to prevent glitches.
--------------------------------------------------------------------------------

library ieee;
use ieee.std_logic_1164.all;
use ieee.numeric_std.all;

entity pwm_generator is
    generic (
        G_CHANNELS      : positive := 8;
        G_CLK_FREQ_HZ   : positive := 25_000_000;
        G_PWM_RES_BITS  : positive := 12
    );
    port (
        clk_i           : in  std_logic;
        rst_n_i         : in  std_logic;
        -- Configuration (I²C register interface)
        cfg_valid_i     : in  std_logic;
        cfg_data_i      : in  std_logic_vector(15 downto 0);
        -- PWM outputs
        pwm_o           : out std_logic_vector(G_CHANNELS-1 downto 0)
    );
end entity pwm_generator;
```

**Key rules:**
- **Indentation:** 4 spaces
- **Naming:** `C_` for constants, `G_` for generics, `_i` for input ports, `_o` for output ports, `_s` for internal signals
- **Comments:** File header with description, entity/architecture descriptions, inline comments for complex logic
- **One entity/architecture per file**
- **Synchronous resets** preferred (`rst_n_i` — active low)
- **No latches** — all combinational processes must be complete

### Python (Test Scripts / Tools)

- Follow **PEP 8**
- Type hints required for all function signatures
- Use `black` formatter with default settings
- Use `ruff` for linting

---

## Hardware Design Guidelines

### KiCad Rules

1. **Version:** KiCad 8.0 or later
2. **Schematic:**
   - One functional block per sheet where practical
   - Use hierarchical sheets for complex subsystems
   - All components must have values, footprints, and supplier fields populated
   - Power nets: use named labels (`+12V`, `+3V3`, `VBAT`, `GND`)
   - Add test points for all critical signals (PWM, I²C, ADC inputs)
3. **PCB Layout:**
   - 4-layer stackup: Signal (Top) / GND (Inner1) / Power (Inner2) / Signal (Bottom)
   - 2 oz copper on inner layers for power
   - Minimum trace/space: 6/6 mil (standard) or 4/4 mil (advanced)
   - High-current traces: use copper pours and thermal reliefs
   - Power stage: keep switching loop area minimal
   - Separate analog and digital ground planes; single-point connection near ADC
   - Kelvin connections for current sense resistors
   - ESD protection on all external connectors
4. **Footprints:**
   - Use standard KiCad library footprints where possible
   - Custom footprints must include 3D models
   - All connectors must be mechanically robust (through-hole preferred)
5. **DRC:** Must pass KiCad DRC with zero errors before PR submission

### Design Review Checklist

Before submitting hardware PRs, verify:

- [ ] Schematic DRC passes
- [ ] PCB DRC passes
- [ ] All components have manufacturer part numbers
- [ ] All components are in stock at JLCPCB/LCSC, Digi-Key, or Mouser
- [ ] Power dissipation calculations included for all high-current paths
- [ ] Thermal analysis (at least basic) for power FETs
- [ ] Test points accessible for all critical nets
- [ ] No unconnected pins or floating inputs
- [ ] Silkscreen is readable and oriented correctly

---

## Testing Requirements

### Firmware Tests

```bash
# Unit tests (PlatformIO native environment)
cd firmware
platformio test -e native

# Integration tests (requires hardware)
platformio test -e esp32-s3
```

**Requirements:**
- New features must include unit tests
- Bug fixes should include a regression test
- FPPT algorithm changes must pass the MATLAB reference simulation comparison
- Target: > 80% code coverage for critical modules (FPPT engine, battery manager, protection)

### FPGA Tests

```bash
# Run all testbenches with GHDL
cd fpga/simulation
make all        # Compile and run all testbenches
make check      # Verify assertions pass
make view       # Open waveforms with GTKWave
```

**Requirements:**
- Every new VHDL module must have a testbench
- PWM generator: verify all 8 channels with various duty cycles and dead times
- Ripple canceller: verify with simulated ripple waveform inputs
- I²C slave: verify register read/write operations

### Hardware Tests

- Bench test with resistive load before connecting to a battery
- Verify all protection mechanisms (OV, OC, OT, reverse polarity)
- Efficiency measurement at 25%, 50%, 75%, 100% load
- Ripple measurement with and without active cancellation
- 24-hour burn-in test at rated power

---

## Documentation Guidelines

- Keep the README up to date
- Document all new features in the appropriate `docs/` file
- Use Mermaid diagrams for architecture changes (place in `docs/architecture.md`)
- Update the BOM when components change
- Add example configurations for new use cases in `examples/`

---

## Communication Channels

| Channel | Purpose | Link |
|---------|---------|------|
| 💬 **Discord** | Real-time chat, community support, brainstorming | [Join](https://discord.gg/example) |
| 🐙 **GitHub Issues** | Bug reports, feature requests, tracking | [Issues](https://github.com/example/SUNFLEX-HC/issues) |
| 📧 **Mailing List** | Release announcements, major discussions | [Subscribe](https://lists.example.com) |
| 📖 **Documentation Site** | Full project documentation | [docs.sunflex-hc.dev](https://docs.example.com) |
| 📹 **YouTube** | Build videos, tutorials, demos | [Channel](https://youtube.com/@SUNFLEX-HC) |

---

## Recognition

All contributors will be recognized in:
- The `CONTRIBUTORS.md` file
- Release notes
- The project documentation

We follow the [All Contributors](https://allcontributors.org/) specification. Contributions of all kinds are valued — code, documentation, testing, design, community support, and more!

---

<p align="center">
  <sub>Thank you for helping make SUNFLEX-HC better for everyone! ☀️</sub>
</p>
