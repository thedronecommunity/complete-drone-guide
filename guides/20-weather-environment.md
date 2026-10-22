# Guide 20: Weather & Environmental Factors

[← Previous: Drone Delivery](19-drone-delivery.md) | [Back to Index](../README.md) | [Next: 3D Printing for Drones →](21-3d-printing.md)

---

## Weather Impact on Drones

Weather is the #1 external factor that determines whether you should fly. Understanding its effects separates safe pilots from reckless ones.

    ┌──────────────────────────────────────────────────┐
    │          WEATHER DECISION MATRIX                 │
    │                                                  │
    │  Wind < 15 km/h + No rain + Visibility > 1km    │
    │  = GO FLY ✓                                     │
    │                                                  │
    │  Wind 15-30 km/h = Fly with caution ⚠️          │
    │  Wind > 30 km/h = DO NOT FLY ✗                  │
    │  Any rain = DO NOT FLY ✗                        │
    │  Fog/low visibility = DO NOT FLY ✗              │
    │  Thunderstorm nearby = DO NOT FLY ✗             │
    └──────────────────────────────────────────────────┘

---

## Wind

### Wind Effects by Speed

| Wind Speed | Effect on Drone | Action |
|-----------|----------------|--------|
| **0-10 km/h** | Negligible | Fly normally |
| **10-20 km/h** | Noticeable drift, reduced flight time | Fly with awareness |
| **20-30 km/h** | Significant drift, battery drain 20-40% faster | Experienced pilots only |
| **30-40 km/h** | Many drones can't maintain position | Do not fly small drones |
| **40+ km/h** | Dangerous for all consumer/hobby drones | Ground all flights |

### Wind and Battery Drain

    Calm conditions:     Moderate wind (20 km/h):
    Battery lasts 20 min  Battery lasts 12-15 min

    Why: Motors must work harder on the upwind side
    to maintain position, drawing 30-50% more current.

    Wind at altitude is ALWAYS stronger than at ground level.
    Rule: If wind feels moderate at ground → it's strong at 50m+.

### Beaufort Scale (Relevant Portion)

| Force | Speed | Visual Cue | Fly? |
|-------|-------|-----------|------|
| 0 | < 2 km/h | Smoke rises vertically | Yes |
| 1-2 | 2-11 km/h | Leaves rustle | Yes |
| 3 | 12-19 km/h | Leaves and twigs move | Caution |
| 4 | 20-28 km/h | Small branches move, dust blows | Experienced only |
| 5 | 29-38 km/h | Small trees sway | No |
| 6+ | 39+ km/h | Large branches move | Absolutely not |

---

## Temperature

### Cold Weather (< 10°C)

    Problems:
    ├── LiPo capacity drops 10-30%
    ├── LiPo internal resistance increases
    ├── Voltage sag is more severe
    ├── LCD screens may not work
    └── Fingers go numb (lose stick feel)

    Mitigations:
    ├── Warm battery before flight (keep in jacket pocket)
    ├── Use battery insulation wrap
    ├── Reduce flight time by 20-30%
    ├── Monitor voltage more carefully
    └── Land at higher voltage than usual (3.6V/cell)

### Hot Weather (> 35°C)

    Problems:
    ├── ESCs and motors overheat faster
    ├── LiPo may puff/swell
    ├── Electronics thermal throttle
    ├── Air density decreases (less lift per RPM)
    └── Companion computers throttle

    Mitigations:
    ├── Fly early morning or late evening
    ├── Reduce hover time, keep moving (airflow cooling)
    ├── Allow longer cool-down between flights
    ├── Don't leave batteries in direct sun
    └── Check motor temps after each flight

### Air Density and Altitude

    Air density decreases with altitude and temperature.
    Less dense air = less lift = motors work harder.

    Sea level, 15°C:  ρ = 1.225 kg/m³ (standard)
    1500m, 30°C:      ρ ≈ 1.05 kg/m³ (Pune in summer)
    3000m, 10°C:      ρ ≈ 0.90 kg/m³ (mountain ops)

    At 3000m altitude, your drone generates ~25% less thrust
    than at sea level with the same motors and props.

    For high-altitude ops: use larger props or higher-KV motors.

---

## Rain and Moisture

    Water + Electronics = Bad

    Effects:
    ├── Short circuits on exposed PCBs
    ├── Corrosion of connectors over time
    ├── Reduced visibility
    ├── Wet props = changed aerodynamics
    └── Foggy camera lens

    Waterproofing levels:
    ├── None (most drones): No rain whatsoever
    ├── Splash resistant: Light drizzle OK (conformal coat)
    ├── Water resistant (IP54): Rain OK, no submersion
    └── Waterproof (IP67): Full rain + splash

### DIY Waterproofing

    Budget waterproofing:
    1. Conformal coating spray on all PCBs (₹400/can)
       - MG Chemicals 422B or similar
       - Cover FC, ESC, receiver, PDB
       - Do NOT coat connectors or buttons

    2. Hot glue on connector joints
    3. Silicone seal on camera/antenna penetrations
    4. Corrosion-X on metal contacts
    5. 3D print a rain cover for the top plate

---

## Electromagnetic Interference

### Common EMI Sources

| Source | Effect | Mitigation |
|--------|--------|-----------|
| **Power lines** | Compass interference, GPS issues | Fly 30m+ away |
| **Cell towers** | RC link interference | Avoid direct proximity |
| **Metal structures** | GPS multipath, compass deviation | Fly above structure height |
| **Wi-Fi hotspots** | 2.4GHz link interference | Use 900MHz or ELRS |
| **Other drones** | Video/control interference | Coordinate frequencies |

### Compass Interference

    Compass errors are the #1 cause of flyaways.

    Sources on the drone itself:
    ├── Power wires carrying high current
    ├── Motors (magnetic)
    ├── Battery
    └── Buzzer

    Fix:
    ├── Mount compass on GPS mast (away from electronics)
    ├── Twist power wires to cancel fields
    ├── Use external compass module
    └── Calibrate compass in flight location, not at home

---

## Flying in India - Seasonal Guide

| Season | Months | Conditions | Tips |
|--------|--------|-----------|------|
| **Winter** | Nov-Feb | Cool, clear, low wind | Best flying season |
| **Summer** | Mar-May | Hot, dusty, thermals | Fly early AM or late PM |
| **Monsoon** | Jun-Sep | Rain, wind, humidity | Very limited flying |
| **Post-monsoon** | Oct-Nov | Clearing, moderate | Good with caution |

### Monsoon Specifics (India)

    During monsoon (June-September):
    ├── Unpredictable rain cells can appear in minutes
    ├── Wind gusts up to 60+ km/h during storms
    ├── Humidity > 90% causes condensation on electronics
    ├── Lightning risk is very real
    └── Visibility drops dramatically

    Rule: If you see cumulonimbus clouds building, pack up.

---

## Pre-Flight Weather Check

    Checklist:
    □ Check wind speed and direction (Windy.com app)
    □ Check precipitation forecast (next 2 hours)
    □ Look for approaching storm cells
    □ Note temperature (battery performance)
    □ Check visibility
    □ Assess turbulence (gusty = bad)
    □ Check KP index if using compass (geomagnetic storm)

    Apps:
    ├── Windy (best overall weather for flying)
    ├── UAV Forecast (drone-specific: wind, GPS sats, KP)
    ├── AirMap (airspace + weather)
    └── AccuWeather MinuteCast (rain prediction by minute)

---

[← Previous: Drone Delivery](19-drone-delivery.md) | [Back to Index](../README.md) | [Next: 3D Printing for Drones →](21-3d-printing.md)
