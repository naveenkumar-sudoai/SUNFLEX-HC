# SUNFLEX-HC Battery Compatibility

This document describes the battery chemistries, auto-detection logic, charging profiles, and safety considerations supported by SUNFLEX-HC.

---

## Supported Battery Chemistries

| Chemistry | Nominal V (12V) | Nominal V (24V) | Nominal V (36V) | Nominal V (48V) | Float V/cell | Absorption V/cell | Equalize V/cell |
|-----------|:---------------:|:---------------:|:---------------:|:---------------:|:------------:|:-----------------:|:---------------:|
| **Lead-Acid (Flooded)** | 6 cells | 12 cells | 18 cells | 24 cells | 2.25V | 2.40V | 2.50V |
| **Lead-Acid (AGM)** | 6 cells | 12 cells | 18 cells | 24 cells | 2.27V | 2.40V | N/R |
| **Lead-Acid (Gel)** | 6 cells | 12 cells | 18 cells | 24 cells | 2.25V | 2.35V | N/R |
| **LiFePO₄ (LFP)** | 4S | 8S | 12S | 16S | 3.35V | 3.55V | N/R |
| **Li-ion (NMC)** | 3S | 7S | 10S | 13S/14S | 4.05V | 4.20V | N/R |
| **Li-ion (LTO)** | 5S | 10S | — | 20S | 2.25V | 2.80V | N/R |

> **N/R = Not Recommended.** Equalization is harmful to sealed and lithium batteries.

### Per-Battery-Voltage Specs

| Battery Voltage | Lead-Acid Float | Lead-Acid Absorb | LiFePO₄ Float | LiFePO₄ Absorb | Li-ion (NMC) Float | Li-ion (NMC) Absorb |
|:--------------:|:--------------:|:----------------:|:------------:|:--------------:|:------------------:|:-------------------:|
| **12V** | 13.5V | 14.4V | 13.4V | 14.2V | 12.15V | 12.6V |
| **24V** | 27.0V | 28.8V | 26.8V | 28.4V | 28.35V | 29.4V |
| **36V** | 40.5V | 43.2V | 40.2V | 42.6V | 40.5V | 42.0V |
| **48V** | 54.0V | 57.6V | 53.6V | 56.8V | 54.7V | 58.8V |

---

## Auto-Detection Logic

When a battery is connected (before charging starts), the ESP32-S3 runs the following detection sequence:

```
                 ┌─────────────┐
                 │ Battery     │
                 │ Connected   │
                 └──────┬──────┘
                        │
                        ▼
              ┌─────────────────────┐
              │ Measure V_open       │
              │ (no load, 100 ms)    │
              └──────────┬──────────┘
                         │
                         ▼
              ┌─────────────────────────────┐
              │ Classify by voltage range:   │
              │  9.5–15.0V  → 12V system    │
              │ 18.0–30.0V  → 24V system    │
              │ 27.0–45.0V  → 36V system    │
              │ 36.0–60.0V  → 48V system    │
              └──────────────┬──────────────┘
                             │
                             ▼
              ┌─────────────────────────────┐
              │ Apply 0.1C pulse for 1 sec  │
              │ Measure ΔV / ΔI             │
              │ Estimate R_int              │
              └──────────────┬──────────────┘
                             │
                             ▼
              ┌─────────────────────────────┐
              │ Classify by R_int:          │
              │  < 5 mΩ   → LiFePO₄         │
              │  5–15 mΩ  → Li-ion (NMC)    │
              │  15–50 mΩ → Lead-Acid AGM   │
              │  > 50 mΩ  → Lead-Acid Flooded│
              └──────────────┬──────────────┘
                             │
                             ▼
              ┌─────────────────────────────┐
              │ Confirm: Apply charge for   │
              │ 30 sec @ 0.05C              │
              │ Monitor dV/dt               │
              │ LiFePO₄: flat curve         │
              │ Lead-Acid: linear rise      │
              │ Li-ion (NMC): steep rise    │
              └──────────────┬──────────────┘
                             │
                             ▼
                 ┌───────────────────┐
                 │ Chemistry +       │
                 │ Voltage CONFIRMED │
                 │ → Select profile  │
                 └───────────────────┘
```

### Manual Override

Users can override auto-detection via:
- Web UI: Settings → Battery → Manual Configuration
- MQTT: `sunflex-hc/battery/config/set`
- Serial CLI: `battery config manual 12v lifepo4`

---

## Charging Profiles

### Multi-Stage Charging (Lead-Acid)

```
  V
  │
  │  ┌──────────┐
  │  │  Bulk    │  ┌──────┐                   ┌──────────┐
  │  │  (CC)    │  │Absorb│                   │  Float   │
  │  │          │  │ (CV) │                   │  (CV)    │
  │  │  I=I_max │  │      │  ┌──────────┐     │          │
  │  │          │  │      │  │  Rest    │     │          │
  │──┘          └──┘      └──┘          └─────┘          └───
  │
  │  I_max ──┐          I_taper → I_float/10  I_float
  │          │  ┌──────┐
  └──────────┘  │      └───────────────────────────────────
     SOC: 0%      SOC: 80%   SOC: 95%      SOC: 100%

  Bulk:      Constant current at I_max until V_absorb reached
  Absorption: Constant voltage at V_absorb until I < I_float/10
  Float:     Constant voltage at V_float (maintenance)
  Equalize:  Manual only — V_eq for 2 hours (flooded lead-acid only)
```

### Two-Stage Charging (LiFePO₄)

```
  V
  │
  │  ┌──────────────────┐
  │  │  CC              │  ┌──────┐
  │  │  I = I_max       │  │  CV  │
  │  │  (SOH-limited)   │  │      │
  │  │                  │  │      │
  │──┘                  └──┘      └─── NO FLOAT
  │
  │  I_max ──────────── I_taper → I_terminate
  │
  └─────────────────────────────────────
     SOC: 0%              SOC: 95%    SOC: 100%

  CC:  Constant current until V_absorb reached
  CV:  Constant voltage until I < I_terminate (typically 0.05C)
  End: Charge stops completely (no float for LFP — float degrades LFP)
```

### CC-CV Charging (Li-ion NMC)

```
Identical to LiFePO₄ profile but with higher voltages per cell.
Adds: Temperature monitoring — charge prohibited below 0°C and above 45°C.
```

---

## Safety Limits

### Voltage Limits (Hardware + Firmware)

| Parameter | Limit | Action |
|-----------|-------|--------|
| V_out max (hardware) | 60V | Zener clamp on ADC input |
| V_out max (firmware) | 58.8V (14S NMC absorb) | PWM shutdown |
| V_out min (firmware) | 9.0V | Deep discharge protection |
| V_cell max (any chemistry) | Config per-profile | Terminate charge |
| Reverse polarity | — | TVS + fuse protection |

### Current Limits

| Parameter | Limit | Action |
|-----------|-------|--------|
| I_charge max | 20A (hardware), configurable | Cycle-by-cycle FPGA limit |
| I_charge max (degraded SOH) | I_max × f_soh(SOH) | FPPT dynamic limit |
| I_charge max (high temp) | I_max × f_temp(T) | Temperature derating |
| Short circuit | > 40A | FPGA < 1 μs shutdown |

### Temperature Limits

| Chemistry | Charge Min | Charge Max | Discharge Min | Discharge Max |
|-----------|:----------:|:----------:|:-------------:|:-------------:|
| Lead-Acid | –20°C | 50°C | –40°C | 60°C |
| LiFePO₄ | 0°C | 45°C | –20°C | 60°C |
| Li-ion (NMC) | 0°C | 45°C | –20°C | 60°C |

> **Critical:** Lithium batteries MUST NOT be charged below 0°C (lithium plating risk, permanent damage). SUNFLEX-HC enforces this in firmware with a hardware-temperature-independent backup.

---

## SOH-Based Current Derating

The FPPT algorithm's health-first mode applies a current multiplier based on SOH:

```
I_charge = I_nominal × f_soh(SOH) × f_temp(T_bat)

where:
  f_soh(SOH) = ((SOH - 40) / 60)^1.5    (SOH in %, clamped to [0, 1])
  f_temp(T)  = 1.0 if T ≤ 35°C
              = 1.0 - 0.025×(T-35) if 35 < T < 55°C
              = 0.0 if T ≥ 55°C
```

**Example — 100 Ah LiFePO₄ battery, 0.5C nominal charge rate (50A max for controller, but hardware limited to 20A):**

| SOH | Temp | f_soh | f_temp | I_charge |
|:---:|:----:|:-----:|:------:|:--------:|
| 100% | 25°C | 1.00 | 1.00 | 20.0A |
| 90% | 25°C | 0.89 | 1.00 | 17.8A |
| 80% | 25°C | 0.71 | 1.00 | 14.2A |
| 70% | 25°C | 0.50 | 1.00 | 10.0A |
| 100% | 45°C | 1.00 | 0.75 | 15.0A |
| 80% | 45°C | 0.71 | 0.75 | 10.7A |

---

## Adding New Battery Chemistries

SUNFLEX-HC is designed for extensibility. To add a new chemistry:

1. **Define profile** in `firmware/inc/battery_profiles.h`:
   ```c
   static const battery_profile_t PROFILE_LTO = {
       .name = "Li-ion (LTO)",
       .cells_per_12v = 5,
       .v_float_per_cell = 2.25,
       .v_absorb_per_cell = 2.80,
       .v_low_cutoff = 1.80,
       .charge_temp_min = -20,
       .charge_temp_max = 45,
       .has_equalize = false,
       .needs_float = false
   };
   ```

2. **Add detection** in `firmware/src/battery_manager.cpp`:
   ```cpp
   if (r_int < 3.0 && v_cell_nominal > 2.0 && v_cell_nominal < 2.6) {
       return BATTERY_CHEM_LTO;
   }
   ```

3. **Submit a PR** with test data showing detection accuracy across temperature range.

---

## Battery Health Monitoring

SUNFLEX-HC tracks the following health metrics per battery:

| Metric | Method | Update Rate |
|--------|--------|:-----------:|
| Internal Resistance (DC-IR) | ΔV/ΔI pulse | Every 60s (during charge) |
| Coulombic Efficiency | Ah_out / Ah_in over full cycle | Per full cycle |
| Capacity Fade | Coulomb counting | Per full cycle |
| Self-Discharge Rate | V_drop during rest periods | Per rest period > 4h |
| Temperature History | Min/Max/Avg since reset | Continuous |

These metrics are:
- Logged to SPIFFS (up to 1 year of daily data)
- Published via MQTT (`sunflex-hc/battery/health`)
- Displayed on the web dashboard
- Used by the FPPT engine for adaptive current limiting

---

*Last updated: July 2025*
