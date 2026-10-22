# Guide 29: Long Range & BVLOS Operations

[← Previous: Drone Racing](28-drone-racing.md) | [Back to Index](../README.md) | [Next: Power Systems & Energy Harvesting →](30-power-systems.md)

---

## Long Range Flying

Long range (LR) drones prioritize endurance and range over agility. BVLOS (Beyond Visual Line of Sight) operations add regulatory and safety complexity.

    ┌──────────────────────────────────────────────────┐
    │           RANGE FACTORS                          │
    │                                                  │
    │  Range = f(battery, efficiency, wind, link)      │
    │                                                  │
    │  Battery:  Li-Ion >> LiPo for energy density    │
    │  Efficiency: Large props, low KV, light frame   │
    │  Wind:     Headwind cuts range by 30-50%        │
    │  Link:     900MHz ELRS or 4G for 20+ km        │
    └──────────────────────────────────────────────────┘

---

## Long Range Build

### Optimized for Range (7" Platform)

| Component | Choice | Weight | Price |
|-----------|--------|--------|-------|
| **Frame** | 7" lightweight (carbon) | 120-180g | ₹3,000-₹5,000 |
| **Motors** | 2806.5 1300KV (6S) | 140g (×4) | ₹6,000 (set) |
| **Props** | 7×3.5 biblade | 30g (×4) | ₹300 |
| **FC + ESC** | F7 + 35A 4-in-1 | 35g | ₹5,000 |
| **Battery** | 6S 3000mAh Li-Ion (Molicel P28A) | 280g | ₹4,000 |
| **FPV** | DJI O3 / HDZero | 40-80g | ₹10,000-₹18,000 |
| **RC Link** | ELRS 900MHz | 5g | ₹1,500 |
| **GPS** | BN-880 (GPS + compass) | 10g | ₹800 |
| **AUW** | | ~750-900g | |
| **Est. flight time** | | 35-50 min | |
| **Est. range** | | 20-40 km | |

### Key Design Principles

    1. Weight reduction is king
       Every gram saved = meters of range gained

    2. Prop efficiency > thrust
       Large slow props > small fast props for cruise

    3. Li-Ion > LiPo for range
       2-3× energy density (Wh/kg)
       Trade: lower burst current (no acro)

    4. Low drag
       Clean frame, flush mounts, minimize frontal area

    5. Altitude = efficiency
       Higher cruise altitude = less ground effect turbulence
       But stay within legal limits (120m AGL in India)

---

## RC Link for Long Range

| System | Frequency | Range | Latency | Price |
|--------|-----------|-------|---------|-------|
| **ELRS 900MHz** | 868/915 MHz | 30-100+ km | 5-15ms | ₹1,500-₹2,500 |
| **ELRS 2.4GHz** | 2.4 GHz | 10-30 km | 2-5ms | ₹1,000-₹2,000 |
| **TBS Crossfire** | 868/915 MHz | 40+ km | 5ms | ₹8,000-₹12,000 |
| **ImmersionRC Ghost** | 2.4 GHz | 15+ km | 5ms | ₹10,000 |

### ELRS 900MHz Setup

    # ELRS Tx module settings for long range:
    Packet rate: 50Hz (best range) or 100Hz (balanced)
    TX power: 1W (1000mW)
    Switch mode: Wide (8 channels)

    # On Betaflight:
    set serialrx_provider = CRSF
    set rssi_channel = 12

    # Failsafe: GPS Rescue
    set failsafe_procedure = GPS-RESCUE
    set gps_rescue_min_sats = 8

---

## Video Link for Long Range

| System | Range | Latency | Resolution | Price |
|--------|-------|---------|-----------|-------|
| **DJI O3** | 10-15 km | 30-40ms | 1080p | ₹15,000 |
| **HDZero** | 5-10 km | 1ms | 720p-1080p | ₹12,000-₹18,000 |
| **Analog 1.2W** | 5-10 km | <1ms | 480p | ₹3,000 |
| **4G streaming** | Unlimited | 200-500ms | 720p+ | ₹3,000-₹5,000 |

---

## BVLOS Regulations (India)

    DGCA BVLOS requirements:
    ├── Type Certificate with BVLOS approval
    ├── Detect and Avoid (DAA) system
    ├── Redundant communication links
    ├── ADS-B transponder (if required)
    ├── Real-time tracking via DigitalSky
    ├── UAOP with BVLOS endorsement
    ├── Flight corridor approval from AAI
    ├── Emergency recovery system (parachute)
    └── Insurance covering BVLOS operations

    Current status (2026):
    ├── BVLOS experimental flights: Permitted with exemptions
    ├── Commercial BVLOS: Limited approvals (Zipline, TechEagle)
    └── Regulatory framework still evolving

---

## Safety Systems for Long Range

    Required redundancy:
    ├── Dual GPS modules
    ├── Dual compass
    ├── Battery monitoring with auto-RTL
    ├── GPS rescue failsafe
    ├── Geofence (hard boundary)
    ├── Parachute for > 2kg
    └── 4G backup telemetry

    GPS Rescue (Betaflight):
    set gps_rescue_angle = 30
    set gps_rescue_return_alt = 60
    set gps_rescue_ground_speed = 1000  # cm/s = 10 m/s
    set gps_rescue_descent_dist = 100   # meters from home
    set gps_rescue_throttle_min = 1200
    set gps_rescue_throttle_max = 1600
    set gps_rescue_min_sats = 8
    set gps_rescue_sanity_checks = RESCUE_SANITY_ON

---

[← Previous: Drone Racing](28-drone-racing.md) | [Back to Index](../README.md) | [Next: Power Systems & Energy Harvesting →](30-power-systems.md)
