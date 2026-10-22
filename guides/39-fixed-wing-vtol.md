# Guide 39: Fixed-Wing & VTOL Design

[← Previous: Commercial Operations](38-commercial-operations.md) | [Back to Index](../README.md) | [Next: Underwater & Hybrid Drones →](40-underwater-hybrid.md)

---

## Fixed-Wing vs Multi-Rotor

    ┌──────────────────────────────────────────────────┐
    │         COMPARISON                              │
    │                                                  │
    │  Multi-Rotor:          Fixed-Wing:              │
    │  ├── Hover: Yes ✓      ├── Hover: No ✗         │
    │  ├── VTOL: Yes ✓       ├── VTOL: No (needs runway)│
    │  ├── Endurance: 20-40m ├── Endurance: 1-4 hours │
    │  ├── Speed: 30-100km/h ├── Speed: 50-150km/h   │
    │  ├── Range: 5-30 km    ├── Range: 50-200 km    │
    │  ├── Payload: Good     ├── Payload: Fair        │
    │  └── Complexity: Low   └── Complexity: Medium   │
    │                                                  │
    │  VTOL (Hybrid):                                 │
    │  ├── Hover: Yes ✓ (using rotors)               │
    │  ├── Efficient cruise: Yes ✓ (using wing)      │
    │  ├── Endurance: 1-3 hours                      │
    │  ├── No runway needed                          │
    │  └── Complexity: High                          │
    └──────────────────────────────────────────────────┘

---

## Fixed-Wing Basics

### Forces of Flight

    Lift (wing) ↑
         │
    ← Drag ── Aircraft ── Thrust → (motor)
         │
    Weight ↓ (gravity)

    Lift > Weight = Climb
    Thrust > Drag = Accelerate
    Balanced = Level flight

### Wing Types

| Wing Type | Efficiency | Stability | Build Difficulty | Use Case |
|-----------|-----------|-----------|-----------------|----------|
| **Flying wing** | High | Moderate | Easy | FPV, mapping |
| **Conventional** | High | Very stable | Medium | Survey, delivery |
| **Delta** | Medium | Good | Easy | Fast FPV |
| **Tandem wing** | High | Very stable | Hard | Heavy payload |

### Airfoil Selection

    Common airfoils for drone-scale fixed wings:
    ├── Clark Y: Good lift, easy to build, forgiving
    ├── NACA 2412: General purpose, good for survey drones
    ├── MH 45: Low Reynolds number, efficient for small wings
    ├── S1223: High lift, good for slow flight
    └── NACA 0012: Symmetric, for aerobatic

---

## VTOL Configurations

### Types

    QuadPlane (ArduPilot):
    Conventional plane + 4 vertical motors
    ├── Simplest VTOL to build
    ├── Extra weight from quad motors
    └── Best supported in firmware

    Tailsitter:
    Entire aircraft tilts vertical for hover
    ├── No extra motors needed
    ├── Complex transition
    └── Limited payload during hover

    Tiltrotor:
    Motors tilt from vertical to horizontal
    ├── Most efficient (motors used in both modes)
    ├── Mechanically complex (tilt mechanism)
    └── Requires precise servo control

### ArduPilot QuadPlane Setup

    # Frame type
    Q_ENABLE = 1
    Q_FRAME_CLASS = 1 (Quad)
    Q_FRAME_TYPE = 1 (X configuration)

    # Transition parameters
    Q_TRANSITION_MS = 5000 (transition time in ms)
    Q_ASSIST_SPEED = 15 (m/s, below this quad motors assist)
    Q_RTL_MODE = 1 (VTOL RTL)

    # Quad motor outputs
    SERVO5_FUNCTION = 33 (motor 1)
    SERVO6_FUNCTION = 34 (motor 2)
    SERVO7_FUNCTION = 35 (motor 3)
    SERVO8_FUNCTION = 36 (motor 4)

---

## Building a Fixed-Wing

### Budget Mapping Wing (~₹15,000)

    Components:
    ├── EPP foam flying wing kit (900-1200mm span)
    ├── 2× SG90 servo (ailerons/elevons)
    ├── 2212 1400KV motor
    ├── 30A ESC
    ├── 9×6 prop
    ├── 3S 2200mAh LiPo
    ├── GPS module
    ├── ArduPilot-compatible FC (Matek F405-Wing)
    └── ELRS receiver

    Performance:
    ├── Endurance: 30-45 minutes
    ├── Speed: 40-80 km/h
    ├── Range: 15-30 km
    └── Payload: 200-300g (camera)

---

## Fixed-Wing Flight Modes (ArduPilot Plane)

| Mode | Description |
|------|-------------|
| **MANUAL** | Direct servo control, no stabilization |
| **STABILIZE** | Self-leveling, pilot controls bank angle |
| **FBWA** | Fly By Wire A, altitude hold with stick |
| **FBWB** | Fly By Wire B, airspeed + altitude hold |
| **AUTO** | Autonomous waypoint following |
| **RTL** | Return to launch, circle overhead |
| **LOITER** | Circle at current position |
| **QHOVER** | VTOL quad hover mode |
| **QLOITER** | VTOL GPS position hold |
| **QRTL** | VTOL return and land vertically |

---

[← Previous: Commercial Operations](38-commercial-operations.md) | [Back to Index](../README.md) | [Next: Underwater & Hybrid Drones →](40-underwater-hybrid.md)
