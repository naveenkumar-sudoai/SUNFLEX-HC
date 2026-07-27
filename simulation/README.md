# SUNFLEX-HC Simulations

## Overview

This directory contains simulation models for both the power electronics (LTspice) and the control algorithms (MATLAB/Simulink). All simulations are validated against hardware measurements.

## LTspice — Power Stage

### Files
- `ltspice/sunflex_hc_buck.asc` — Buck mode simulation (Vin > Vout)
- `ltspice/sunflex_hc_boost.asc` — Boost mode simulation (Vin < Vout)
- `ltspice/sunflex_hc_buck_boost.asc` — Buck-boost transition simulation
- `ltspice/efficiency_vs_load.asc` — Efficiency sweep across load range
- `ltspice/ripple_cancellation.asc` — Active ripple cancellation model
- `ltspice/lib/` — Custom component models (MOSFETs, inductors)

### Running Simulations

1. Install [LTspice](https://www.analog.com/en/design-center/design-tools-and-calculators/ltspice-simulator.html)
2. Open any `.asc` file
3. Run simulation (Ctrl+R)
4. Probe signals of interest

## MATLAB — Control Algorithm

### Files
- `matlab/fppt_simulation.m` — Main FPPT vs MPPT comparison simulation
- `matlab/battery_model.m` — LiFePO₄ aging model (capacity fade + IR growth)
- `matlab/solar_panel_model.m` — Single-diode PV model with irradiance/temperature
- `matlab/mppt_po.m` — Standard Perturb & Observe MPPT
- `matlab/fppt_health_first.m` — Health-First FPPT with SOH derating
- `matlab/results/` — Saved simulation outputs and figures

### Running Simulations

```matlab
% From MATLAB command window
cd simulation/matlab
run_fppt_simulation

% This runs a 1-year accelerated simulation comparing:
%  - Standard MPPT: battery SOH drops to 68%
%  - Health-First FPPT: battery SOH drops to 82%
%  - Improvement: 14 percentage points = ~30-50% life extension
```

## Results Summary

| Metric | Standard MPPT | Health-First FPPT | Improvement |
|--------|:------------:|:-----------------:|:-----------:|
| Year 1 energy harvested | 100% (baseline) | 92% | –8% energy |
| Battery SOH after 1 year | 68% | 82% | +14% SOH |
| Battery life to 60% SOH | 2.1 years | 3.4 years | +62% life |
| Total lifetime energy | 100% (baseline) | 138% | +38% total |

**Conclusion:** The 8% annual energy sacrifice yields a 62% lifetime extension and 38% more total energy delivered over the battery's life.

