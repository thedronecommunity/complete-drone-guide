# Guide 40: Underwater & Hybrid Drones

[← Previous: Fixed-Wing & VTOL](39-fixed-wing-vtol.md) | [Back to Index](../README.md) | [Next: Data Logging & Flight Analysis →](41-data-logging.md)

---

## Beyond Air: Underwater ROVs

Underwater drones (ROVs - Remotely Operated Vehicles) share many principles with aerial drones but operate in a completely different environment.

    ┌──────────────────────────────────────────────────┐
    │         AIR vs WATER COMPARISON                 │
    │                                                  │
    │  Property      │ Air         │ Water            │
    │  ──────────────┼─────────────┼────────────       │
    │  Density       │ 1.2 kg/m³   │ 1000 kg/m³      │
    │  RF signals    │ Work well   │ Blocked          │
    │  GPS           │ Available   │ Not available    │
    │  Communication │ RF/Wi-Fi    │ Tether/acoustic  │
    │  Power         │ Battery     │ Battery/tether   │
    │  Navigation    │ GPS/IMU     │ IMU/DVL/sonar    │
    │  Buoyancy      │ N/A         │ Critical         │
    └──────────────────────────────────────────────────┘

---

## ROV Components

### Thruster Configuration

    Typical ROV layout (6 thruster):

    Top view:
    T₁ ╲      ╱ T₂     T₁, T₂: Horizontal (forward/rotate)
        ╲    ╱
         [ROV]
        ╱    ╲
    T₃ ╱      ╲ T₄     T₃, T₄: Horizontal (strafe)

    Side view:
       T₅ ── [ROV] ── T₆   T₅, T₆: Vertical (depth)

### Key Components

| Component | Air Drone | Underwater ROV |
|-----------|----------|---------------|
| **Propulsion** | Propellers in air | Thrusters in water |
| **Frame** | Carbon fiber | Waterproof enclosure + frame |
| **Buoyancy** | N/A | Syntactic foam, ballast |
| **Sealing** | N/A | O-rings, potted connectors |
| **Comms** | RF wireless | Tether (fiber/copper) |
| **Navigation** | GPS + IMU | Depth sensor + IMU + DVL |
| **Camera** | Standard | Underwater housing, lights |

---

## Open-Source ROV Platforms

| Platform | Depth Rating | Thrusters | Price | Notes |
|----------|-------------|-----------|-------|-------|
| **BlueROV2** | 100m | 6 (T200) | ₹3,00,000+ | Industry standard |
| **OpenROV/Sofar Trident** | 100m | 3 | ₹1,50,000 | Consumer grade |
| **DIY PVC ROV** | 10-30m | 4 | ₹15,000-₹30,000 | Educational |

### ArduSub (ArduPilot for Underwater)

    ArduSub runs on the same ArduPilot codebase:
    ├── Same GCS (QGroundControl)
    ├── Same MAVLink protocol
    ├── Supports 6DOF control
    ├── Depth hold mode
    └── Video streaming via tether

    # ArduSub frame configuration
    FRAME_CONFIG = 5 (vectored 6DOF)

---

## Hybrid Aerial-Aquatic Drones

    Concepts:
    ├── Drone that lands on water and dives
    ├── Drone that drops a tethered underwater probe
    └── Transforming drone (folds props, uses thrusters)

    Challenges:
    ├── Waterproofing electronics that also need airflow
    ├── Prop design for both air and water
    ├── Buoyancy management (need to float and fly)
    ├── Communication switching (RF to tether)
    └── Weight penalty for dual-medium operation

---

## Waterproofing for Aerial Drones

    Even aerial drones sometimes need water protection:

    IP Ratings:
    ├── IPX0: No protection
    ├── IPX4: Splash resistant (light rain)
    ├── IPX5: Low-pressure water jets
    ├── IPX6: Strong water jets
    ├── IPX7: Submersion to 1m for 30 min
    └── IPX8: Continuous submersion

    DIY waterproofing for aerial drones:
    ├── Conformal coating on PCBs (MG 422B)
    ├── Potted ESCs (epoxy encapsulated)
    ├── O-ring sealed enclosures for electronics
    ├── Marine-grade connectors
    └── Drain holes in frame (water will get in)

---

[← Previous: Fixed-Wing & VTOL](39-fixed-wing-vtol.md) | [Back to Index](../README.md) | [Next: Data Logging & Flight Analysis →](41-data-logging.md)
