# Guide 30: Power Systems & Energy Harvesting

[← Previous: Long Range & BVLOS](29-long-range-bvlos.md) | [Back to Index](../README.md) | [Next: Antenna Design & RF →](31-antenna-rf.md)

---

## Power System Architecture

Every drone has a power distribution system that takes battery voltage and delivers clean, regulated power to each subsystem.

    ┌──────────────────────────────────────────────────┐
    │              POWER DISTRIBUTION                  │
    │                                                  │
    │  Battery (VBAT: 14.8V-25.2V for 4S-6S)         │
    │    │                                             │
    │    ├── ESC (direct VBAT) → Motors               │
    │    ├── 5V BEC → Flight controller, GPS, Rx      │
    │    ├── 5V BEC → Companion computer              │
    │    ├── 12V BEC → VTx, FPV camera                │
    │    ├── 3.3V LDO → Sensors (off FC 5V rail)     │
    │    └── Direct VBAT → Servo, spotlight, heater   │
    │                                                  │
    │  Current measurement:                            │
    │    Battery → Current sensor (shunt) → PDB       │
    │    FC reads voltage + current for monitoring     │
    └──────────────────────────────────────────────────┘

---

## Power Module / PDB

### Types

| Type | Features | Price | Best For |
|------|----------|-------|----------|
| **PDB (PCB)** | Solder pads, 5V/12V BECs | ₹300-₹800 | Custom builds |
| **4-in-1 ESC with PDB** | Integrated, clean | Included | FPV quads |
| **Mauch Power Module** | Precision V+I sensing | ₹3,000-₹5,000 | ArduPilot/PX4 |
| **Holybro PM02** | PixHawk compatible | ₹1,500-₹2,500 | Pixhawk builds |

### Voltage Regulators

| Type | Efficiency | Noise | Use Case |
|------|-----------|-------|----------|
| **LDO** | Low (40-60%) | Very low | Sensors, FC (low current) |
| **Switching (buck)** | High (85-95%) | Higher | BECs, servos, companion computers |
| **Buck-boost** | High | Medium | Constant output from variable battery |

    BEC sizing:
    ├── FC + GPS + Rx: ~500mA → 1A 5V BEC is enough
    ├── Raspberry Pi 4: 2-3A → Dedicated 5V 3A+ BEC
    ├── Jetson Orin Nano: 5-15W → 5V 5A BEC or direct barrel
    ├── Servos: 1-5A each → Separate high-current BEC
    └── VTx: 0.5-2A → 12V BEC (or 9V for some VTx)

---

## Current Sensing

    Why measure current:
    ├── Calculate mAh consumed → know remaining capacity
    ├── Detect motor/ESC failures (current spike or dropout)
    ├── Optimize efficiency (log current vs throttle)
    └── Trigger warnings and failsafe

    ArduPilot parameters:
    BATT_MONITOR = 4 (analog voltage + current)
    BATT_VOLT_PIN = (depends on FC)
    BATT_CURR_PIN = (depends on FC)
    BATT_VOLT_MULT = (calibrate with multimeter)
    BATT_AMP_PERVLT = (calibrate with current meter)

    BATT_CAPACITY = 5000 (mAh)
    BATT_LOW_VOLT = 3.5 (per cell)
    BATT_CRT_VOLT = 3.3 (per cell)
    BATT_FS_LOW_ACT = 2 (RTL on low battery)
    BATT_FS_CRT_ACT = 1 (Land on critical)

---

## Power Budget Calculator

    Step 1: List all consumers
    ┌──────────────────┬────────┬──────┬─────────┐
    │ Component        │ Volts  │ Amps │ Watts   │
    ├──────────────────┼────────┼──────┼─────────┤
    │ 4× Motors (hover)│ VBAT   │ 10A  │ 150W    │
    │ Flight controller│ 5V     │ 0.3A │ 1.5W    │
    │ GPS              │ 5V     │ 0.05A│ 0.25W   │
    │ Receiver         │ 5V     │ 0.1A │ 0.5W    │
    │ VTx              │ 12V    │ 0.5A │ 6W      │
    │ Raspberry Pi 4   │ 5V     │ 2A   │ 10W     │
    │ Camera           │ 5V     │ 0.5A │ 2.5W    │
    ├──────────────────┼────────┼──────┼─────────┤
    │ TOTAL (hover)    │        │      │ ~171W   │
    └──────────────────┴────────┴──────┴─────────┘

    Step 2: Calculate flight time
    Battery: 6S 5000mAh = 22.2V × 5Ah = 111Wh
    Usable (80%): 89Wh
    Flight time: 89 / 171 × 60 = ~31 minutes hover

---

## Solar and Energy Harvesting

### Solar-Powered Drones

    Current solar cell efficiency: 22-24% (monocrystalline)

    For a 1 kg drone drawing 150W in hover:
    Solar irradiance (India, clear day): ~1000 W/m²
    Solar panel needed: 150 / (1000 × 0.22) = 0.68 m²

    This is impractical for multirotors.
    Solar makes sense for:
    ├── Fixed-wing drones (large wing area, low power draw)
    ├── Charging stations (land, charge, fly again)
    └── Ground-based systems (GCS power)

### Solar Charging Station

    Concept: Autonomous landing pad with solar charging
    ├── Solar panel: 100W (₹3,000-₹5,000)
    ├── Charge controller: MPPT (₹2,000)
    ├── Buffer battery: 12V 20Ah LiFePO4 (₹5,000)
    ├── Drone charger: LiPo balance charger (₹3,000)
    ├── Landing pad: Precision landing with ArUco marker
    └── Auto-connect: Spring-loaded pogo pins

---

## Hydrogen Fuel Cells

    Emerging tech for long-endurance drones:
    ├── Energy density: 3-5× LiPo
    ├── Flight times: 2-4 hours achievable
    ├── Weight: Heavier than equivalent LiPo for short flights
    ├── Cost: Very expensive (₹2-5 lakh per system)
    ├── Refueling: Requires hydrogen supply infrastructure
    └── Status: Commercial products exist (Intelligent Energy, Doosan)

---

[← Previous: Long Range & BVLOS](29-long-range-bvlos.md) | [Back to Index](../README.md) | [Next: Antenna Design & RF →](31-antenna-rf.md)
