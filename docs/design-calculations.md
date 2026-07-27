# SUNFLEX-HC Design Calculations

This document contains the key design equations, component selection justifications, and efficiency analysis for the SUNFLEX-HC controller.

---

## Table of Contents

1. [Buck-Boost Converter Design](#buck-boost-converter-design)
2. [Inductor Selection](#inductor-selection)
3. [Capacitor Selection](#capacitor-selection)
4. [MOSFET Selection](#mosfet-selection)
5. [Gate Drive Design](#gate-drive-design)
6. [Current Sensing](#current-sensing)
7. [Voltage Sensing](#voltage-sensing)
8. [Efficiency Analysis](#efficiency-analysis)
9. [Thermal Analysis](#thermal-analysis)
10. [FPGA PWM Resolution](#fpga-pwm-resolution)
11. [FPPT Algorithm Mathematics](#fppt-algorithm-mathematics)
12. [SOH Estimation](#soh-estimation)

---

## Buck-Boost Converter Design

### Operating Parameters

| Parameter | Symbol | Min | Nom | Max | Unit |
|-----------|--------|:---:|:---:|:---:|------|
| Input Voltage | V_in | 20 | — | 150 | V |
| Output Voltage | V_out | 10.5 | — | 58.8 | V |
| Output Current | I_out | 0 | — | 20 | A |
| Switching Frequency | f_sw | 100 | 250 | 400 | kHz |
| Output Power | P_out | 0 | — | 500 | W |

### Duty Cycle Formulas

**Buck Mode (V_in > V_out):**

$$D_{buck} = \frac{V_{out}}{V_{in} \cdot \eta}$$

Where η ≈ 0.97 (estimated efficiency).

- At V_in = 150V, V_out = 12V: D = 12 / (150 × 0.97) = 0.082 (8.2%)
- At V_in = 48V, V_out = 24V: D = 24 / (48 × 0.97) = 0.515 (51.5%)

**Boost Mode (V_in < V_out):**

$$D_{boost} = 1 - \frac{V_{in} \cdot \eta}{V_{out}}$$

- At V_in = 20V, V_out = 48V: D = 1 - (20 × 0.97) / 48 = 0.596 (59.6%)
- At V_in = 36V, V_out = 48V: D = 1 - (36 × 0.97) / 48 = 0.273 (27.3%)

**Buck-Boost Mode (V_in ≈ V_out):**

$$V_{out} = V_{in} \cdot \frac{D}{1-D}$$

- At V_in = V_out = 24V: D = 0.5 (50%)

---

## Inductor Selection

### Inductance Calculation

The critical inductance for continuous conduction mode (CCM) boundary:

**Buck mode:**
$$L_{crit} = \frac{(V_{in,max} - V_{out}) \cdot D_{min}}{2 \cdot f_{sw} \cdot I_{out,min}}$$

**Boost mode:**
$$L_{crit} = \frac{V_{in,min} \cdot D_{max}}{2 \cdot f_{sw} \cdot I_{out,min}}$$

Using worst-case parameters:
- Buck worst-case: V_in = 150V, V_out = 48V, I_out = 1A, f_sw = 250 kHz
  - D_min = 48/(150×0.97) = 0.33
  - L_crit = (150-48) × 0.33 / (2 × 250,000 × 1) = 67.3 μH

- Boost worst-case: V_in = 20V, V_out = 48V, I_out = 1A, f_sw = 250 kHz
  - D_max = 1 - (20×0.97)/48 = 0.596
  - L_crit = 20 × 0.596 / (2 × 250,000 × 1) = 23.8 μH

### Selected Inductor

| Parameter | Value |
|-----------|-------|
| Inductance | **100 μH** |
| Saturation Current | > 25A |
| DC Resistance | < 15 mΩ |
| Part Number | **Bourns SRP1770TA-101M** (or equivalent) |
| Dimensions | 17.2 × 16.9 × 10 mm |
| Core Material | Carbonyl powder (magnetically shielded) |

**Justification:**
- 100 μH provides comfortable margin above the 67.3 μH critical value
- 25A saturation current handles full 20A output + 30% ripple margin
- Shielded construction minimizes EMI
- Standard size fits the 100 × 80 mm PCB

### Ripple Current

Peak-to-peak inductor current ripple:

**Buck mode (worst case: V_in = 150V, V_out = 12V):**
$$\Delta I_L = \frac{(V_{in} - V_{out}) \cdot D}{f_{sw} \cdot L} = \frac{(150 - 12) \times 0.0825}{250,000 \times 100\times10^{-6}} = 0.455 \text{ A}$$

**Boost mode (worst case: V_in = 20V, V_out = 48V):**
$$\Delta I_L = \frac{V_{in} \cdot D}{f_{sw} \cdot L} = \frac{20 \times 0.596}{250,000 \times 100\times10^{-6}} = 0.477 \text{ A}$$

Ripple is < 5% of rated current at full load — excellent.

---

## Capacitor Selection

### Input Capacitance

The input capacitor handles the pulsating current drawn by the switched inductor:

$$C_{in,min} = \frac{I_{out,max} \cdot D_{max} \cdot (1 - D_{max})}{f_{sw} \cdot \Delta V_{in,pp}}$$

With ΔV_in,pp = 2% × V_in_min = 0.02 × 20 = 0.4V:
$$C_{in,min} = \frac{20 \times 0.6 \times 0.4}{250,000 \times 0.4} = 48 \text{ μF}$$

**Selected:** 3× 22 μF / 200V X7R ceramic + 2× 100 μF / 200V aluminum electrolytic (low ESR)
- Effective capacitance: ~330 μF at operating voltage
- RMS ripple current rating: > 5A (derated)

### Output Capacitance

Output ripple is minimized by the active FPGA cancellation, but baseline selection:

$$C_{out,min} = \frac{\Delta I_L}{8 \cdot f_{sw} \cdot \Delta V_{out,pp}}$$

With ΔV_out,pp = 0.1% × V_out (before cancellation) = 0.001 × 48 = 48 mV:
$$C_{out,min} = \frac{0.48}{8 \times 250,000 \times 0.048} = 5 \text{ μF}$$

The active ripple cancellation can reduce effective ripple by 10–20 dB, so much less capacitance is needed than in a conventional design.

**Selected:** 2× 47 μF / 100V X7R ceramic + 2× 220 μF / 63V aluminum polymer
- With active cancellation: < 1% ripple at full load

---

## MOSFET Selection

### Voltage Rating

$$V_{DS,max} \geq 1.5 \times V_{in,max} = 1.5 \times 150 = 225 \text{ V}$$

→ Select **250V** rated MOSFETs (next standard value above 225V).

### Current Rating

Maximum RMS current through any switch:

$$I_{DS,RMS} \approx I_{out,max} \times \sqrt{D_{max}} = 20 \times \sqrt{0.6} = 15.5 \text{ A}$$

→ Select ≥ 40A rated MOSFETs (standard margin for switching applications).

### Conduction Losses

$$P_{cond} = I_{DS,RMS}^2 \times R_{DS(on)}$$

**Selected MOSFET: Infineon BSC320N20NS3 (OptiMOS™)**

| Parameter | Value |
|-----------|-------|
| V_DS | 200V |
| I_D (continuous) | 36A @ 100°C |
| R_DS(on) @ 25°C | 32 mΩ |
| R_DS(on) @ 125°C | ~64 mΩ |
| Q_g (total) | 17 nC |
| Q_gd (Miller) | 3.2 nC |
| Package | TDSON-8 (5×6 mm) |

**Conduction loss per FET (at 125°C, 15.5A RMS):**
$$P_{cond} = (15.5)^2 \times 0.064 = 15.4 \text{ W}$$

Four FETs active in buck-boost mode → 61.6W total conduction loss.
This is the dominant loss mechanism and drives the thermal design.

### Switching Losses

$$P_{sw} = \frac{1}{2} \cdot V_{DS} \cdot I_D \cdot (t_{rise} + t_{fall}) \cdot f_{sw}$$

With V_DS = 150V worst case, I_D = 20A, t_rise + t_fall ≈ 20 ns (from datasheet, gate drive dependent):
$$P_{sw} = 0.5 \times 150 \times 20 \times 20\times10^{-9} \times 250,000 = 7.5 \text{ W}$$

Per FET → 30W total switching loss at 250 kHz.
At 100 kHz: 12W total. **The FPGA adaptively lowers f_sw at high load to reduce switching losses.**

---

## Gate Drive Design

### Gate Drive Current Requirement

$$I_{gate,peak} = \frac{Q_g}{t_{rise}} = \frac{17 \times 10^{-9}}{20 \times 10^{-9}} = 0.85 \text{ A}$$

### Bootstrap Capacitor

$$C_{boot} \geq \frac{Q_g + Q_{ls} + I_{leak} \cdot T_{on}}{\Delta V_{boot}}$$

Where:
- Q_ls = level shifter charge ≈ 3 nC
- I_leak ≈ 10 μA
- T_on = 1/f_sw = 4 μs (max at 250 kHz)
- ΔV_boot = 0.5V (allowable droop)

$$C_{boot} \geq \frac{17 + 3 + (10\times10^{-6} \times 4\times10^{-6})}{0.5\times10^{-9}} \approx 40 \text{ nF}$$

**Selected:** 100 nF / 25V X7R + 1 μF / 25V X7R (parallel, for low ESR across frequency)

**Selected Gate Driver: Texas Instruments LM5109B**

| Parameter | Value |
|-----------|-------|
| Peak source current | 1A |
| Peak sink current | 1A |
| Bootstrap voltage | Up to 108V |
| Propagation delay | 28 ns (typ) |
| Dead-time programmability | Via resistor |

---

## Current Sensing

### High-Side Current Sense (INA229)

**Shunt Resistor Selection:**

Trade-off between accuracy and power loss:

$$P_{shunt} = I_{max}^2 \times R_{shunt}$$

| R_shunt | Full-Scale Voltage (20A) | Power Loss @ 20A | Accuracy @ 0.1A |
|---------|:------------------------:|:----------------:|:---------------:|
| 1 mΩ | 20 mV | 0.4 W | Low |
| 2 mΩ | 40 mV | 0.8 W | Medium |
| 5 mΩ | 100 mV | 2.0 W | High |
| 10 mΩ | 200 mV | 4.0 W | Very High |

**Selected:** 5 mΩ (Bourns CSS2H-3920R-5L00F)
- 2W power loss at full load (acceptable in a 500W system)
- 100 mV full-scale signal (good SNR)
- 3920 package (10W rated, good thermal margin)

**INA229 Specifications:**

| Parameter | Value |
|-----------|-------|
| Supply Voltage | 2.7–5.5V |
| CMRR | 120 dB (DC) |
| Gain Error | ±0.1% (max) |
| Offset Voltage | ±5 μV |
| SPI Interface | 10 MHz |
| Resolution | 20-bit delta-sigma ADC |

---

## Voltage Sensing

### Resistive Divider Design

**Input Voltage (0–150V → 0–3.3V ADC):**

Attenuation ratio: 3.3 / 150 = 0.022

$$R_{top} = 100 \text{ kΩ}, R_{bottom} = \frac{V_{adc,max} \cdot R_{top}}{V_{in,max} - V_{adc,max}} = \frac{3.3 \times 100,000}{150 - 3.3} = 2,249 \text{ Ω}$$

→ **2.2 kΩ** (standard value)

Actual attenuation: 2.2k / (100k + 2.2k) = 0.0215
Actual V_in,max with 3.3V ADC: 3.3 / 0.0215 = 153.5V ✅

**Output Voltage (0–60V → 0–3.3V ADC):**

Attenuation ratio: 3.3 / 60 = 0.055

With R_top = 47 kΩ:
$$R_{bottom} = \frac{3.3 \times 47,000}{60 - 3.3} = 2,734 \text{ Ω}$$

→ **2.7 kΩ** (standard value)

**Note:** Place a 100 pF capacitor in parallel with R_top for frequency compensation (divider response flat to ~15 kHz).

---

## Efficiency Analysis

### Loss Budget

| Loss Mechanism | Buck @ 48V→24V, 200W | Boost @ 24V→48V, 200W |
|---------------|:---------------------:|:----------------------:|
| MOSFET conduction | 3.2 W (1.6%) | 4.1 W (2.05%) |
| MOSFET switching | 1.8 W (0.9%) | 1.2 W (0.6%) |
| Inductor DCR | 1.2 W (0.6%) | 1.5 W (0.75%) |
| Inductor core | 0.4 W (0.2%) | 0.5 W (0.25%) |
| Gate drive | 0.3 W (0.15%) | 0.3 W (0.15%) |
| Sense resistors | 0.4 W (0.2%) | 0.4 W (0.2%) |
| Control + aux | 0.3 W (0.15%) | 0.3 W (0.15%) |
| **Total** | **7.6 W (3.8%)** | **8.3 W (4.15%)** |

**Calculated Efficiency:**
- Buck: 200 / 207.6 = 96.3% (at 200W); peaks at 98.2% near 350W
- Boost: 200 / 208.3 = 96.0% (at 200W); peaks at 97.5% near 300W

---

## Thermal Analysis

### Junction Temperature Estimate

Using the Infineon BSC320N20NS3 in TDSON-8:
- R_θJA = 50°C/W (with 1 sq. in. of 2 oz copper)
- R_θJC = 1.2°C/W (junction to case)

Worst case (buck-boost mode, all 4 FETs switching, 15A load):

$$P_{FET,total} = P_{cond} + P_{sw} = 15.4 + 7.5 = 22.9 \text{ W}$$

With heatsink (R_θHS = 8°C/W, natural convection):
$$T_J = T_A + P \times (R_{\theta JC} + R_{\theta HS})$$
$$T_J = 40 + 22.9 \times (1.2 + 8) = 40 + 210.7 = 250.7 \text{ °C}$$

**This is unacceptable!** → Thermal mitigation required:

1. **Lower R_DS(on) MOSFET** (Infineon BSC105N25NS3: R_DS(on) = 10.5 mΩ at 25°C) → reduces P_cond by 67%
2. **Adaptive frequency reduction** → at high load, f_sw = 100 kHz instead of 250 kHz → reduces P_sw by 60%
3. **Active forced-air cooling** → R_θHS = 2°C/W (40mm fan)

Revised with BSC105N25NS3 + 100 kHz + fan:
- P_cond = 15.5² × 0.021 = 5.0 W per FET
- P_sw = 0.5 × 150 × 20 × 20ns × 100,000 = 3.0 W per FET
- P_total = 8.0 W per FET

$$T_J = 40 + 8.0 \times (1.2 + 2.0) = 65.6 \text{ °C}$$

✅ Well within the 175°C T_J,max rating.

---

## FPGA PWM Resolution

### Required Resolution

At 250 kHz switching frequency, to achieve 0.5% duty cycle resolution:

$$T_{sw} = \frac{1}{250,000} = 4 \text{ μs}$$
$$T_{step} = 0.5\% \times 4 \text{ μs} = 20 \text{ ns}$$

The SLG47910V's internal 25 MHz oscillator gives 40 ns resolution — insufficient.

### Achieving 0.5 ns Resolution

A delay-line interpolator using FPGA fabric:

1. Coarse resolution: 25 MHz counter → 40 ns steps
2. Fine resolution: 80-tap delay line (each tap ≈ 0.5 ns) → interpolates between counter ticks
3. Total resolution: 40 ns / 80 = 0.5 ns

At 250 kHz: 4 μs / 0.5 ns = 8000 steps → **12.96 bits effective** (~13-bit PWM resolution)

This enables duty cycle resolution of 0.0125% — far exceeding what's needed.

---

## FPPT Algorithm Mathematics

### Standard MPPT (Perturb & Observe)

```
1. Measure: V(k), I(k) → P(k) = V(k) × I(k)
2. Compare:  ΔP = P(k) - P(k-1), ΔV = V(k) - V(k-1)
3. Perturb:  If ΔP > 0, continue in same direction
            If ΔP < 0, reverse direction
4. Step:     V_ref(k+1) = V_ref(k) ± ΔV_step
```

### Health-First Modification

The key insight: **limit output power when battery SOH is degraded.**

```python
def fppt_control(V_in, I_in, V_out, I_out, SOH, T_bat):
    # Standard MPPT: determine optimal operating point
    P_mpp = standard_pao_mppt(V_in, I_in)
    V_mpp = V_in  # perturbed value

    # Health-First constraint: maximum allowable charge current
    I_max = I_rated * f_soh(SOH) * f_temp(T_bat)

    # Temperature derating
    # f_temp(25°C) = 1.0, f_temp(45°C) = 0.5, f_temp(55°C) = 0.0

    # SOH-based derating
    # f_soh(100%) = 1.0, f_soh(80%) = 0.8, f_soh(60%) = 0.5

    # Apply constraint
    if V_mpp * P_mpp / V_out > I_max:
        # FPPT mode: power-limit the MPPT setpoint
        P_fppt = I_max * V_out
        V_ref = find_voltage_for_power(P_fppt)
    else:
        # Normal MPPT mode
        V_ref = V_mpp

    return V_ref
```

### SOH Derating Function

$$f_{soh}(SOH) = \left(\frac{SOH - SOH_{min}}{100 - SOH_{min}}\right)^k$$

Where:
- SOH_min = 40% (battery at end of life; f_soh = 0 at this point)
- k = 1.5 (exponent for non-linear derating; gentler at high SOH, aggressive at low SOH)

| SOH | f_soh (k=1.5) |
|:---:|:------------:|
| 100% | 1.00 |
| 90% | 0.89 |
| 80% | 0.71 |
| 70% | 0.50 |
| 60% | 0.28 |
| 50% | 0.11 |
| 40% | 0.00 |

### Temperature Derating Function

$$f_{temp}(T) = \begin{cases}
1.0 & T \leq 35°C \\
1.0 - 0.025 \times (T - 35) & 35°C < T < 55°C \\
0.0 & T \geq 55°C
\end{cases}$$

---

## SOH Estimation

### DC Internal Resistance Method

Periodically (every 60 seconds during charging), execute:

1. Reduce charge current by ΔI = 5A for 100 ms
2. Measure ΔV during the current step
3. Calculate: $$R_{int} = \frac{\Delta V}{\Delta I}$$
4. Reference: $$SOH = \frac{R_{int,initial}}{R_{int,current}} \times 100\%$$

**Example:**
- Fresh LiFePO₄ battery: R_int,initial = 2.0 mΩ
- After 500 cycles: R_int,current = 3.5 mΩ
- SOH = 2.0/3.5 × 100 = 57%

### Coulomb Counting (Capacity Fade)

Secondary method for cross-validation:

$$SOH_{capacity} = \frac{C_{current}}{C_{rated}} \times 100\%$$

Where C_current is measured by integrating charge during a full charge cycle from 20% to 100% SOC.

### Combined SOH Estimate

$$SOH = 0.7 \times SOH_{DCIR} + 0.3 \times SOH_{capacity}$$

The DC-IR method is weighted higher because:
- It can be measured during normal charging (no full cycle needed)
- It is a leading indicator of degradation (capacity fade lags resistance increase)
- It is less affected by temperature (compensated by NTC reading)

---

*Last updated: July 2025*
