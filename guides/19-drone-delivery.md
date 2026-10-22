# Guide 19: Drone Delivery Systems

[← Previous: Swarm Drones](18-swarm-drones.md) | [Back to Index](../README.md) | [Next: Weather & Environmental Factors →](20-weather-environment.md)

---

## Drone Delivery Overview

Drone delivery is moving packages from point A to point B autonomously. This guide covers the technical architecture needed to build a delivery drone system from scratch.

    ┌──────────────────────────────────────────────────┐
    │           DELIVERY SYSTEM ARCHITECTURE           │
    │                                                  │
    │  Cloud Backend                                   │
    │  ├── Order management                            │
    │  ├── Route planning                              │
    │  ├── Fleet management                            │
    │  └── Regulatory compliance                       │
    │         │                                        │
    │         ↓ (4G/5G)                                │
    │  Ground Control Station                          │
    │  ├── Real-time monitoring                        │
    │  ├── Emergency override                          │
    │  └── Airspace deconfliction                      │
    │         │                                        │
    │         ↓ (telemetry link)                       │
    │  Drone (onboard)                                 │
    │  ├── Flight controller                           │
    │  ├── Companion computer                          │
    │  ├── Navigation (GPS + VIO)                      │
    │  ├── Payload mechanism                           │
    │  └── Sense & avoid                               │
    └──────────────────────────────────────────────────┘

---

## Delivery Drone Design Requirements

### Payload vs Range Tradeoff

| Class | Max Payload | Range | Use Case |
|-------|-----------|-------|----------|
| **Micro** | 250g | 2-5 km | Medicine, small parcels |
| **Light** | 1 kg | 5-15 km | Food delivery, e-commerce |
| **Medium** | 5 kg | 10-25 km | Mid-mile logistics |
| **Heavy** | 20+ kg | 20-50 km | Industrial, rural supply |

### Key Design Parameters

    Payload: 1 kg delivery drone example

    Specs:
    ├── Frame: Quad-X, 550-650mm wheelbase
    ├── Motors: 2812-900KV or similar
    ├── Props: 13-15 inch
    ├── Battery: 6S 5000-8000mAh Li-Ion
    ├── AUW (empty): ~2.5 kg
    ├── AUW (loaded): ~3.5 kg
    ├── Thrust-to-weight: 2:1 minimum
    ├── Flight time (loaded): 20-30 min
    ├── Cruise speed: 40-60 km/h
    └── Range: 10-15 km (with reserves)

---

## Navigation for Delivery

### Waypoint-Based Delivery Mission

    ┌─────────────────────────────────────────────┐
    │                                             │
    │   HOME ──→ WAYPOINT 1 ──→ WAYPOINT 2       │
    │    ↑          (climb)        (cruise)       │
    │    │                            │           │
    │    │                            ↓           │
    │   RTL  ←── WAYPOINT 4 ←── DELIVERY POINT   │
    │              (cruise)    (descend + drop)    │
    │                                             │
    │   Altitude profile:                         │
    │   60m ────────────────────────               │
    │   40m                        ╲              │
    │   20m                         ╲             │
    │    0m ╱                        ╲── Drop     │
    │      Home                    Destination     │
    └─────────────────────────────────────────────┘

### Precision Landing

For accurate package delivery, GPS alone (3-5m accuracy) is not enough:

| Method | Accuracy | Range | Hardware |
|--------|----------|-------|----------|
| **GPS** | 3-5m | Global | Standard GPS |
| **RTK GPS** | 2 cm | Base station range | RTK module + base |
| **ArUco markers** | 2-5 cm | < 10m altitude | Camera + OpenCV |
| **IR beacon** | 10 cm | < 20m | IR camera + beacon |
| **UWB anchors** | 10 cm | 100m radius | UWB tags + anchors |

### ArduPilot Precision Landing Setup

    # Enable precision landing with IR or camera
    PLND_ENABLED = 1
    PLND_TYPE = 1 (companion computer)
    PLND_EST_TYPE = 0 (raw sensor)

    # Companion computer sends:
    # LANDING_TARGET MAVLink message with x, y offsets

---

## Payload Release Mechanisms

### Common Mechanisms

| Type | Payload | Complexity | Reliability |
|------|---------|-----------|-------------|
| **Servo hook** | < 2 kg | Low | High |
| **Electromagnetic** | < 5 kg | Low | Very high |
| **Winch/tether** | < 10 kg | Medium | High |
| **Gripper (servo)** | Variable | Medium | Medium |
| **Parachute drop** | < 3 kg | Low | Medium |

### Servo Hook (Simplest)

    ┌──────────────────────────────────┐
    │  Servo Hook Mechanism            │
    │                                  │
    │  Locked:        Released:        │
    │   ┌─┐            ┌─┐            │
    │   │S├──╮         │S├──╮         │
    │   └─┘  │ ← hook  └─┘  ╰── open │
    │        ○ ← ring       ○         │
    │        │               ↓ drop   │
    │       [P]             [P]       │
    │   S = Servo                      │
    │   P = Payload                    │
    └──────────────────────────────────┘

    ArduPilot config:
    SERVO9_FUNCTION = 0 (Disabled, use as passthrough)
    # Trigger via MAVLink DO_SET_SERVO command
    # or assign to RC channel

    # Python release command:
    drone.mav.command_long_send(
        drone.target_system, drone.target_component,
        mavutil.mavlink.MAV_CMD_DO_SET_SERVO,
        0, 9, 1100,  # Servo 9, PWM 1100 (release)
        0, 0, 0, 0, 0)

---

## Safety Systems for Delivery

### Parachute System

    Flight controller detects failure
         │
         ↓
    Trigger conditions:
    ├── Attitude error > 30 degrees
    ├── Vibration spike (prop failure)
    ├── Motor output saturation
    └── EKF variance too high
         │
         ↓
    Fire parachute servo/cutter
    + Disarm motors immediately

    ArduPilot Parachute:
    CHUTE_ENABLED = 1
    CHUTE_TYPE = 10 (servo)
    CHUTE_SERVO_ON = 1300
    CHUTE_ALT_MIN = 10 (meters, won't deploy below this)

### Geofence for Delivery Corridor

    # Define a delivery corridor geofence
    FENCE_ENABLE = 1
    FENCE_TYPE = 7 (altitude + circle + polygon)
    FENCE_ALT_MAX = 120 (meters, DGCA limit)
    FENCE_RADIUS = 5000 (meters from home)
    FENCE_ACTION = 1 (RTL on breach)

---

## Regulatory Framework (India)

### DGCA Drone Delivery Rules

| Category | Weight | Requirements |
|----------|--------|-------------|
| **Nano** | < 250g | No registration needed, limited payload |
| **Micro** | 250g-2kg | UIN required, NPNT compliance |
| **Small** | 2-25kg | Type certificate, UIN, UAOP, pilot license |
| **Medium** | 25-150kg | Full certification + insurance |

### Key Requirements for Delivery

    For commercial drone delivery in India:
    ├── Type Certificate (TC) from DGCA
    ├── Unique Identification Number (UIN)
    ├── NPNT (No Permission No Takeoff) compliance
    ├── Unmanned Aircraft Operator Permit (UAOP)
    ├── Remote Pilot License
    ├── Third-party insurance
    ├── DigitalSky platform registration
    └── Corridor approval from AAI

---

## Building a Delivery Pipeline

### Mission State Machine

    IDLE ──→ PREFLIGHT_CHECK ──→ ARM ──→ TAKEOFF
                                            │
    MISSION_COMPLETE ←── CRUISE ←── CLIMB_TO_ALT
         │                                  │
         ↓                                  ↓
    RTL_CLIMB ──→ RTL_CRUISE ──→ RTL_DESCEND ──→ LAND
                                  ↑
    DELIVERY_DESCEND ──→ PAYLOAD_RELEASE ──→ DELIVERY_CLIMB
         ↑
    APPROACH_TARGET ──→ PRECISION_APPROACH

### Communication Stack

    Drone ←→ 4G Module ←→ Cloud Server ←→ GCS Dashboard

    Hardware:
    ├── SIM7600 4G HAT on Raspberry Pi
    ├── MQTT for telemetry (lightweight)
    ├── REST API for commands
    └── WebSocket for real-time dashboard

---

## Companies to Study

| Company | Country | Approach | Status |
|---------|---------|----------|--------|
| **Wing (Google)** | USA/Australia | Small packages, suburban | Operational |
| **Amazon Prime Air** | USA | E-commerce delivery | Limited operations |
| **Zipline** | Rwanda/USA | Medical supply, fixed-wing | Scaled operations |
| **Swiggy (Skygate)** | India | Food delivery | Pilot phase |
| **Dunzo** | India | Quick commerce | Testing |
| **TechEagle** | India | Medical + e-commerce | Operational |
| **Throttle Aerospace** | India | Medical delivery | BVLOS certified |

---

[← Previous: Swarm Drones](18-swarm-drones.md) | [Back to Index](../README.md) | [Next: Weather & Environmental Factors →](20-weather-environment.md)
