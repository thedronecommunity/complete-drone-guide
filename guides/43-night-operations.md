# Guide 43: Night Operations

[← Previous: Photography & Videography](42-photography-videography.md) | [Back to Index](../README.md) | [Next: Thermal Imaging →](44-thermal-imaging.md)

---

## Night Flying Challenges

Night operations introduce unique challenges: reduced visibility, disorientation, regulatory restrictions, and the need for specialized equipment.

    ┌──────────────────────────────────────────────────┐
    │           NIGHT OPS CHALLENGES                  │
    │                                                  │
    │  Visibility:                                     │
    │  ├── Can't see drone beyond 50-100m             │
    │  ├── Can't see obstacles (trees, wires)         │
    │  ├── Orientation lost easily                    │
    │  └── Depth perception degraded                   │
    │                                                  │
    │  Technical:                                      │
    │  ├── Camera needs low-light capability          │
    │  ├── Navigation lights required by law          │
    │  ├── FPV camera performance degrades            │
    │  └── Battery performance drops (cold nights)    │
    │                                                  │
    │  Safety:                                         │
    │  ├── Harder to spot other aircraft              │
    │  ├── Emergency landing zones hard to identify   │
    │  └── Bystanders may not see/hear drone          │
    └──────────────────────────────────────────────────┘

---

## Required Equipment

### Navigation Lights (Anti-Collision)

    DGCA / International requirements:
    ├── Visible from 3 statute miles (5 km) in clear air
    ├── Flashing anti-collision strobe (white or red)
    ├── Position lights optional but recommended:
    │   ├── Red: Left (port)
    │   ├── Green: Right (starboard)
    │   └── White: Rear (aft)
    └── Must be on during entire flight

    Hardware:
    ├── Matek 2812ARM LED strip (addressable RGB)
    ├── Flytron Strobon navigation lights (₹1,500/pair)
    ├── Generic strobe (₹200-₹500)
    └── Custom WS2812B setup via Betaflight LED strip

### Spotlight Systems

| Light | Lumens | Weight | Power | Price |
|-------|--------|--------|-------|-------|
| **Lume Cube** | 1500 lm | 99g | Own battery | ₹8,000 |
| **Custom LED (10W)** | 1000+ lm | 30g | 12V from drone | ₹500 |
| **Custom LED (30W)** | 3000+ lm | 80g | 12V from drone | ₹1,500 |
| **Searchlight gimbal** | 5000+ lm | 200g+ | Battery | ₹5,000+ |

---

## Camera Settings for Night

### FPV Camera

    Night settings:
    ├── WDR (Wide Dynamic Range): ON
    ├── Day/Night mode: Night or Auto
    ├── Exposure: Extended (longer integration time)
    ├── Gain: Allow higher auto gain
    └── Note: Night FPV image will be grainy

### Photography/Videography at Night

    Camera settings:
    ├── ISO: 3200-12800 (as needed)
    ├── Aperture: Wide open (f/2.8 or wider)
    ├── Shutter: 1/30s-1/60s (long exposure = blur)
    ├── Format: RAW (essential for noise reduction in post)
    ├── ND filter: REMOVE (you need all the light)
    └── White balance: Manual (streetlights are orange)

    For long exposure (tripod shots from drone):
    ├── Hover in LOITER mode (stable platform)
    ├── Use 2-5 second exposure
    ├── Very calm wind required
    └── Result: Light trails, city glow

---

## Night Navigation

    Best practices:
    ├── Always fly FPV for orientation awareness
    ├── Set up LED orientation lights (front = one color, rear = another)
    ├── Use GPS position hold (LOITER) as primary mode
    ├── Set conservative geofence
    ├── Pre-plan mission during daylight
    ├── Scout landing area in daylight
    └── Carry a bright flashlight for the landing zone

    Betaflight LED strip config:
    # In CLI:
    # Front arms: Green
    # Rear arms: Red
    # All: Flash on disarm, solid when armed
    set ledstrip_visual_beeper = ON

---

## Regulations (India)

    DGCA Night Flying:
    ├── Permitted with anti-collision lights
    ├── Visual observer recommended
    ├── Enhanced telemetry monitoring
    ├── Reduced operational area recommended
    ├── All normal category rules still apply
    └── Additional risk assessment documented

---

## Applications

| Application | Equipment Needed | Notes |
|-------------|-----------------|-------|
| **SAR night operations** | Thermal + spotlight | Most valuable application |
| **Security patrol** | Thermal + RGB camera | Perimeter monitoring |
| **Event videography** | Low-light camera + lights | Concerts, festivals |
| **Night photography** | Good camera, stable hover | Cityscapes, light trails |
| **Wildlife monitoring** | Thermal camera | Minimal disturbance |
| **Infrastructure inspection** | Thermal camera | Detect heat anomalies |

---

[← Previous: Photography & Videography](42-photography-videography.md) | [Back to Index](../README.md) | [Next: Thermal Imaging →](44-thermal-imaging.md)
