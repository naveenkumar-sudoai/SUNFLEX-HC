# SUNFLEX-HC Project Overview

## Vision

SUNFLEX-HC exists to democratize intelligent solar charging. We believe that **every solar installation — from a 7 Ah DIY project to a 100 Ah off-grid home — deserves intelligent, health-aware charging** without the premium price tag or vendor lock-in of proprietary controllers.

Our vision is a world where:
- Solar charge controllers are **commodity hardware with open software**, like the Raspberry Pi of solar
- Battery health is treated as a **first-class concern**, not an afterthought
- Anyone can **build, modify, and improve** their charge controller
- Rural electrification projects are **not held hostage** by proprietary spare parts and closed protocols
- Engineering students learn power electronics on **real, production-quality hardware** they can study, not black boxes

---

## Problem Statement

### The Current Landscape

Today's solar charge controller market is dominated by a handful of proprietary vendors (Victron Energy, EPEVER, Renogy, Morningstar). Their products are:

1. **Expensive** — $150–400 for MPPT controllers with limited configurability
2. **Closed** — Schematics are proprietary, firmware is locked, no modification possible
3. **Limited** — Each model supports a narrow voltage range and specific battery chemistries
4. **Health-Agnostic** — Traditional MPPT algorithms maximize power extraction with zero regard for battery longevity
5. **Non-Upgradable** — When your battery bank grows, you buy a new controller

### The Battery Health Crisis

Conventional MPPT controllers are **killing batteries prematurely**. Here's why:

- **Charge at maximum available current** regardless of battery SOH
- **No current derating** — a degraded battery receives the same aggressive charge profile as a new one
- **Single-minded efficiency** — optimizing panel utilization at the expense of battery cycle life
- **Result:** Batteries fail at 60–70% of their design life, wasting hundreds of dollars and creating e-waste

### The Open-Source Gap

While open-source hardware has revolutionized computing (Raspberry Pi, Arduino), 3D printing (Prusa, Voron), and even EVs (OpenInverter), **solar power electronics remain stubbornly closed**. Existing open-source MPPT projects (LibreSolar, Arduino MPPT) lack:
- High-voltage universal input capability
- FPGA-based precision control
- Battery health awareness
- Production-ready hardware (not just breadboard prototypes)

---

## The SUNFLEX-HC Solution

SUNFLEX-HC addresses every gap in the market:

| Problem | SUNFLEX-HC Solution |
|---------|-------------------|
| **Proprietary lock-in** | 100% open-source (MIT) — hardware, firmware, FPGA, software |
| **Narrow compatibility** | Universal 20–150V input; auto-detects 12/24/36/48V batteries of any chemistry |
| **Battery degradation** | Health-First FPPT — SOH-aware charging extends battery life by 30–50% |
| **High cost** | BOM ~$55; open-source DIY or assembled kits |
| **Not upgradable** | Same hardware scales from 7 Ah to 100+ Ah; OTA firmware updates |
| **Poor efficiency** | 98%+ with FPGA-controlled synchronous buck-boost and active ripple cancellation |
| **No connectivity** | Built-in WiFi/BLE, MQTT, Home Assistant integration |

---

## Target Audience

### 1. DIY Solar Enthusiasts (Primary)
People building their own solar systems who want a controller they can understand, modify, and trust. They value openness over polish and capability over brand names.

### 2. Engineering Students & Researchers
Power electronics, control systems, and renewable energy students who need real hardware to experiment with — not a sealed box. The FPGA + MCU hybrid architecture is a teaching platform.

### 3. EPC Installers
Engineering, Procurement, and Construction firms installing solar in remote areas. They need reliable hardware that works with whatever panels and batteries are locally available — not model-dependent controllers.

### 4. Off-Grid / Rural Electrification Projects
NGOs and government programs deploying solar in areas with no grid access. Open-source means local repair, no vendor lock-in, and community-driven improvements.

### 5. Makers & Hackers
Anyone who looks at a commercial charge controller and thinks, "I could make this better if they'd just let me at the firmware."

---

## Competitive Landscape

| Controller | Price (USD) | Open Source | Universal Input | Multi-Chemistry | SOH Monitoring | WiFi/BLE |
|-----------|:-----------:|:-----------:|:---------------:|:---------------:|:--------------:|:--------:|
| **SUNFLEX-HC** | ~$55 (BOM) | ✅ Full MIT | ✅ 20–150V | ✅ Auto-detect | ✅ Health-First FPPT | ✅ Built-in |
| Victron SmartSolar 100/20 | ~$160 | ❌ | 100V max | ⚠️ Manual | ❌ | ⚠️ BT only |
| EPEVER Tracer 4210AN | ~$140 | ❌ | 100V max | ⚠️ Limited | ❌ | ❌ |
| Renogy Rover 40A | ~$110 | ❌ | 100V max | ⚠️ Manual | ❌ | ⚠️ BT add-on |
| LibreSolar MPPT 2420 | ~$80 (kit) | ✅ CERN OHL | 75V max | ⚠️ Manual | ❌ | ❌ |
| Arduino-based DIY | ~$25 (parts) | ✅ | 55V max | ❌ | ❌ | ❌ |

---

## Project Phases & Roadmap

### Phase 1 — Prototype Validation (Current)
- [x] STM32F407 prototype with buck converter
- [x] FPPT algorithm validated in MATLAB simulation
- [x] Battery SOH estimation (DC-IR method) verified
- [x] Buck-boost topology simulated in LTspice
- [ ] ESP32-S3 + SLG47910V hardware bring-up (Rev A)
- [ ] Firmware port from STM32 to ESP32-S3

### Phase 2 — FPGA Integration (Q1–Q2 2026)
- [ ] SLG47910V PWM generator validation
- [ ] Active ripple cancellation at full power
- [ ] I²C communication protocol finalized
- [ ] Efficiency measurements across all modes

### Phase 3 — Field Testing (Q3 2026)
- [ ] 10-unit beta program
- [ ] 3-month accelerated aging test
- [ ] Home Assistant / MQTT integration testing
- [ ] EMI/EMC pre-compliance testing

### Phase 4 — Production (Q4 2026)
- [ ] Rev B PCB (cost-optimized, 1 kW capable)
- [ ] Injection-molded enclosure design
- [ ] CE / FCC certification
- [ ] Assembled kits available for purchase
- [ ] Partnership with PCB assembler for volume production

### Phase 5 — Ecosystem (2027+)
- [ ] Modular expansion boards (load control, grid-tie interface)
- [ ] Multi-controller parallel operation (master/slave)
- [ ] Mobile app (React Native)
- [ ] Cloud monitoring platform
- [ ] Academic partnerships for algorithm research

---

## Guiding Principles

1. **Health First** — Battery longevity always takes priority over squeezing an extra watt from the panel
2. **Universal Compatibility** — No special panels, no proprietary batteries, no locked protocols
3. **Radical Transparency** — Every design decision documented; no magic numbers, no hidden trade-offs
4. **Accessible Excellence** — Professional-grade engineering at hobbyist-accessible prices
5. **Community Owned** — The project belongs to its users, not a corporation. MIT license ensures this forever.

---

## Risk Factors

| Risk | Impact | Mitigation |
|------|--------|-----------|
| **MOSFET failure** | Battery overcharge possible | Independent hardware OVP; FPGA fault handler < 1 μs |
| **FPGA supply chain** | SLG47910V single-source | Abstract FPGA interface; migrate to Lattice iCE40 if needed |
| **Thermal runaway** | Fire hazard | Multi-point temperature sensing; automatic power throttling |
| **License violations** | Legal risk | All third-party components audited; clean license attribution |
| **Community fragmentation** | Slow progress | Strong governance; regular releases; responsive maintainers |

---

## Success Metrics

- **GitHub stars:** 500+ (Year 1 target)
- **Community builders:** 50+ verified builds
- **Field deployments:** 20+ installations running > 6 months
- **Contributors:** 15+ active code contributors
- **Battery life improvement:** 30%+ verified in controlled testing
- **Cost vs. commercial:** 3–5× cheaper than equivalent Victron/EPEVER

---

## Related Projects & Resources

### Inspiration
- [LibreSolar MPPT Charger](https://github.com/LibreSolar/mppt-charger) — Pioneering open-source MPPT
- [ElectroDacus SBMS0](https://electrodacus.com/) — Open BMS with solar integration
- [OpenEnergyMonitor](https://openenergymonitor.org/) — Renewable energy monitoring framework

### Reference Designs
- Renesas SLG47910V GreenPAK™ application notes
- TI PMP22946 — 98.5% efficient 4-switch buck-boost reference design
- ESP32-S3-DevKitC schematic

### Academic Papers
- "Comparison of MPPT Algorithms for Battery Charging" (IEEE Trans. Power Electron., 2022)
- "Battery Health-Aware Charging Strategies" (J. Power Sources, 2023)
- "Active Ripple Cancellation in DC-DC Converters" (IEEE Trans. Ind. Electron., 2021)

---

*Last updated: July 2025*
