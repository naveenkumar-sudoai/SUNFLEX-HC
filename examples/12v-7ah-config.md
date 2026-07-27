# Example Configuration: 12V 7Ah Lead-Acid Backup

A small-scale, low-cost setup ideal for learning, experimentation, and emergency backup power.

---

## Use Case

- **Application:** Home office UPS / learning platform
- **Load:** Router (12V, 10W), LED lights (12V, 15W), phone charging (5V, 10W)
- **Runtime:** ~4–6 hours on battery alone
- **Budget:** Under $150 total (panel + battery + controller)

---

## Hardware Bill of Materials

| Component | Part | Spec | Approx. Cost | Source |
|-----------|------|------|:------------:|--------|
| Solar Panel | 12V 50W Monocrystalline | Voc ~22V, Isc ~3A | $35 | Amazon / eBay |
| Battery | 12V 7Ah Sealed Lead-Acid (AGM) | Common UPS battery | $18 | Amazon / Local |
| Controller | SUNFLEX-HC Rev A | Assembled PCB | $55 | DIY / Kit |
| Enclosure | ABS project box 120×100×60mm | IP65 | $8 | Amazon |
| Wiring | 14 AWG silicone wire, MC4 connectors | 2m pair | $10 | Amazon |
| Fuse | Inline fuse holder + 10A fuse | Automotive blade | $4 | Auto store |
| **Total** | | | **~$130** | |

---

## Wiring Diagram

```
  ┌─────────────┐         ┌─────────────────┐         ┌─────────────┐
  │  50W Solar  │ + ──────│+  SUNFLEX-HC   +│─────────│+  12V 7Ah   │
  │  Panel      │         │    Controller   │         │   Battery   │
  │  (22V Voc)  │ – ──────│–               –│─────────│–  (AGM)     │
  └─────────────┘         │                 │         └──────┬──────┘
                          │  Load Terminal  │                │
                          │  +         –    │                │
                          └──┬─────────┬────┘                │
                             │         │                     │
                      ┌──────┘         └──────┐              │
                      ▼                       ▼              ▼
                 ┌─────────┐           ┌─────────┐    ┌──────────┐
                 │ Router  │           │LED Light│    │ USB Charger│
                 │ 12V/10W │           │ 12V/15W │    │ 5V/10W   │
                 └─────────┘           └─────────┘    └──────────┘
```

---

## SUNFLEX-HC Configuration

### Firmware Settings (via Web UI or config.h)

```c
// Battery Configuration
#define BATTERY_TYPE           BATTERY_LEAD_ACID_AGM
#define BATTERY_VOLTAGE        BATTERY_12V
#define BATTERY_CAPACITY_AH    7.0f

// Charging Parameters
#define CHARGE_CURRENT_MAX_A   1.4f     // 0.2C for 7Ah battery
#define CHARGE_VOLTAGE_FLOAT   13.5f    // AGM float voltage
#define CHARGE_VOLTAGE_ABSORB  14.4f    // AGM absorption voltage

// FPPT Health-First Settings
#define FPPT_ENABLE_SOH_LIMIT  true
#define SOH_MIN_PERCENT        40.0f

// Load Control
#define LOAD_LVC_CUTOFF        11.5f    // Low voltage cutoff
#define LOAD_LVC_RECONNECT     12.5f    // Reconnect when battery recovers

// WiFi
#define WIFI_MODE              WIFI_MODE_AP
#define WIFI_AP_SSID           "SUNFLEX-12V-7Ah"
```

### Topology Mode

With a 12V panel (Voc ~22V) and a 12V battery:
- **Default mode:** Buck (22V → 14.4V max charge)
- **At low light:** May transition to buck-boost (panel drops to ~14V in clouds)

---

## Expected Performance

| Metric | Value |
|--------|-------|
| Peak charge current | 1.4A (0.2C) |
| Charge time (full sun, 0→100%) | ~5 hours |
| Ripple (with active cancellation) | < 100 mVpp |
| Idle power consumption | 0.3W (controller) |
| Battery life extension (FPPT vs MPPT) | ~35% (estimated) |

---

## Testing & Validation

- [ ] Measure V_out ripple with and without FPGA active cancellation
- [ ] Verify low-voltage cutoff at 11.5V
- [ ] Confirm auto-restart after reconnect threshold (12.5V)
- [ ] Run 48-hour burn-in at 0.1C continuous charge
- [ ] Test over-temperature protection (place in sun, verify derating)

---

## Photos / Build Log

*Submit your build photos to the `#build-showcase` Discord channel!*

---

*Example last updated: July 2025*
