# Example Configuration: 48V 100Ah LiFePO₄ Off-Grid Home

A high-capacity setup for partial off-grid home power, workshop, or remote cabin.

---

## Use Case

- **Application:** Off-grid cabin / home backup / workshop power
- **Load:** Refrigerator (120W), LED lighting (50W), laptop charging (65W), water pump (200W intermittent)
- **Runtime:** ~24 hours (moderate load) on battery alone
- **Budget:** Under $1,500 total (panels + battery + controller)

---

## Hardware Bill of Materials

| Component | Part | Spec | Qty | Approx. Cost | Source |
|-----------|------|------|:---:|:------------:|--------|
| Solar Panel | 72-cell 400W Monocrystalline | Voc ~49V, Vmp ~41V, Imp ~10A | 2 | $400 | Solar distributor |
| Battery | 48V 100Ah LiFePO₄ (16S) | Server rack style, with BMS | 1 | $750 | Signature Solar / EG4 |
| Controller | SUNFLEX-HC Rev A | 48V auto-detect, 20A max | 1 | $55 | DIY / Kit |
| Combiner Box | 2-string PV combiner | 15A fuses, surge protection | 1 | $45 | Amazon |
| Disconnect | DC isolator switch | 600V, 32A | 1 | $25 | Electrical supplier |
| Breaker Panel | AC/DC distribution | 6-circuit | 1 | $40 | Home Depot |
| Wiring | 10 AWG PV wire, 6 AWG battery cable | Various lengths | — | $60 | Solar supplier |
| Enclosure | NEMA 3R outdoor enclosure | 300×250×150mm | 1 | $30 | Electrical supplier |
| Grounding | Ground rod, 6 AWG copper, clamps | NEC compliant | 1 | $35 | Home Depot |
| **Total** | | | | **~$1,440** | |

---

## Wiring Diagram

```
                        ┌───────────────────┐
  ┌──────────┐          │   Combiner Box    │
  │ 400W PV │ + ───────│+  (2×15A fused)  +│────┐
  │ Panel #1 │ – ───────│–                 –│──┐ │
  └──────────┘          └───────────────────┘  │ │
                                               │ │
  ┌──────────┐          ┌───────────────────┐  │ │
  │ 400W PV │ + ───────│+                 +│──┘ │
  │ Panel #2 │ – ───────│–                 –│────┘
  └──────────┘          └───────────────────┘
                                   │
                          ┌────────▼────────┐
                          │  DC Isolator     │
                          │  (600V / 32A)    │
                          └────────┬────────┘
                                   │
                          ┌────────▼─────────────────────────┐
                          │        SUNFLEX-HC                │
                          │  Vin: 60–90V (2 panels series)   │
                          │  Vout: 48V → auto-detect         │
                          │  Imax: 20A (~960W max charge)    │
                          └────────┬─────────────────────────┘
                                   │
                          ┌────────▼────────┐
                          │  DC Breaker      │
                          │  (63A, 2-pole)   │
                          └────────┬────────┘
                                   │
                          ┌────────▼─────────────────────────┐
                          │  48V 100Ah LiFePO₄ Battery       │
                          │  (16S, with internal BMS)        │
                          └────────┬─────────────────────────┘
                                   │
                          ┌────────▼────────┐
                          │  DC Distribution │
                          │  (6-circuit)     │
                          └──┬──┬──┬──┬──┬──┘
                             │  │  │  │  │
                       ┌─────┘  │  │  │  └─────┐
                       ▼        ▼  ▼  ▼        ▼
                   ┌──────┐  (Loads connected via DC breakers)
                   │Invert│  Refrigerator, Lights,
                   │  er  │  Laptops, Water Pump
                   └──────┘
```

---

## SUNFLEX-HC Configuration

### Firmware Settings (via Web UI or config.h)

```c
// Battery Configuration
#define BATTERY_TYPE           BATTERY_LIFEPO4
#define BATTERY_VOLTAGE        BATTERY_48V
#define BATTERY_CAPACITY_AH    100.0f

// Charging Parameters
#define CHARGE_CURRENT_MAX_A   20.0f    // 0.2C for 100Ah battery
#define CHARGE_VOLTAGE_ABSORB  56.8f    // 16S × 3.55V = 56.8V
#define CHARGE_VOLTAGE_FLOAT   53.6f    // 16S × 3.35V = 53.6V

// FPPT Health-First Settings
#define FPPT_ENABLE_SOH_LIMIT  true
#define SOH_MIN_PERCENT        40.0f
#define SOH_DERATE_EXPONENT    1.5f

// Temperature Sensing
#define TEMP_SENSOR_BATTERY    true    // External NTC on battery terminal
#define TEMP_CHARGE_MAX_C      45.0f
#define TEMP_CHARGE_MIN_C      0.0f    // LiFePO4 cold charging cutoff

// WiFi (STA mode for Home Assistant)
#define WIFI_MODE              WIFI_MODE_STA
#define WIFI_SSID              "MyHomeWiFi"
#define MQTT_BROKER_IP         "192.168.1.100"
#define MQTT_TOPIC_PREFIX      "sunflex-hc/48v-cabin"

// Home Assistant Auto-Discovery
#define HA_AUTO_DISCOVERY      true
```

### Topology Mode

With 2× 400W panels in series (Vmp ≈ 82V, Voc ≈ 98V) and a 48V battery:
- **Default mode:** Buck (82V → 56.8V max charge)
- **Low-light transition:** Remains in buck down to ~55V input
- **Max charge current:** 20A (limited by controller hardware)
- **Max charge power:** 20A × 56.8V ≈ 1,136W (theoretical; panels supply ~760W max)

---

## Home Assistant Integration

### MQTT Auto-Discovery Entities

```yaml
# Automatically created by SUNFLEX-HC firmware:

sensor:
  - name: "SUNFLEX Cabin — Battery Voltage"
    state_topic: "sunflex-hc/48v-cabin/battery/voltage"
    unit_of_measurement: "V"

  - name: "SUNFLEX Cabin — Charge Current"
    state_topic: "sunflex-hc/48v-cabin/battery/current"
    unit_of_measurement: "A"

  - name: "SUNFLEX Cabin — Solar Power"
    state_topic: "sunflex-hc/48v-cabin/solar/power"
    unit_of_measurement: "W"

  - name: "SUNFLEX Cabin — Battery SOH"
    state_topic: "sunflex-hc/48v-cabin/battery/soh"
    unit_of_measurement: "%"

  - name: "SUNFLEX Cabin — Daily Energy"
    state_topic: "sunflex-hc/48v-cabin/energy/daily"
    unit_of_measurement: "Wh"

switch:
  - name: "SUNFLEX Cabin — Load Output"
    command_topic: "sunflex-hc/48v-cabin/load/set"
    state_topic: "sunflex-hc/48v-cabin/load/state"
```

### Home Assistant Dashboard Card

```yaml
type: entities
title: Solar Charge Controller
entities:
  - entity: sensor.sunflex_cabin_battery_voltage
  - entity: sensor.sunflex_cabin_charge_current
  - entity: sensor.sunflex_cabin_solar_power
  - entity: sensor.sunflex_cabin_battery_soh
  - entity: sensor.sunflex_cabin_daily_energy
  - entity: switch.sunflex_cabin_load_output
```

---

## Expected Performance

| Metric | Value |
|--------|-------|
| Peak charge power | ~760W (panel-limited) |
| Peak charge current | ~13.4A @ 56.8V (panel-limited, within 20A controller limit) |
| Charge time (50%→100%) | ~7 hours (1 sunny day) |
| Average daily harvest | 3.5–4.5 kWh (varies by season/location) |
| Ripple (active cancellation) | < 500 mVpp |
| Idle consumption | 0.5W (controller + WiFi) |
| Battery life (with FPPT) | 8–12 years (vs. 5–8 years without) |

---

## Safety Checklist

- [ ] DC isolator accessible and labeled near the controller
- [ ] Ground rod installed per NEC 690.47
- [ ] Battery in fire-resistant enclosure (LiFePO₄ is safer than NMC but still needs care)
- [ ] Overcurrent protection on both PV input and battery output
- [ ] Surge protection device (SPD) on PV input
- [ ] Emergency shutdown procedure documented and posted
- [ ] Local fire code compliance verified

---

## Photos / Build Log

*Submit your build photos to the `#build-showcase` Discord channel!*

---

*Example last updated: July 2025*
