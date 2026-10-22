# Guide 24: Payload Integration

[← Previous: Telemetry Systems](23-telemetry-systems.md) | [Back to Index](../README.md) | [Next: Mapping & Photogrammetry →](25-mapping-photogrammetry.md)

---

## Payload Basics

Any equipment carried by the drone beyond its flight systems is payload. Payload directly trades off against flight time and maneuverability.

    ┌──────────────────────────────────────────────────┐
    │           PAYLOAD BUDGET PLANNING                │
    │                                                  │
    │  Total Thrust (all motors at 100%)               │
    │  - Frame weight                                  │
    │  - Battery weight                                │
    │  - Electronics weight                            │
    │  - Safety margin (2:1 thrust-to-weight min)      │
    │  ────────────────────────────                    │
    │  = Available payload capacity                    │
    │                                                  │
    │  Example (5" quad):                              │
    │  Total thrust: 4 × 1200g = 4800g                │
    │  Frame + FC + ESC: 250g                          │
    │  Battery: 200g                                   │
    │  Misc: 50g                                       │
    │  2:1 safety = need 2× (500g + payload)           │
    │  Max payload = (4800/2) - 500 = 1900g            │
    │  Practical payload: ~500-800g (for good flight)  │
    └──────────────────────────────────────────────────┘

---

## Common Payloads

| Payload | Weight | Power | Mount Type | Application |
|---------|--------|-------|-----------|-------------|
| **Action camera** (GoPro) | 120-150g | Own battery | Soft mount / TPU | FPV, video |
| **Compact camera** (Sony RX0) | 110g | Own battery | Hard mount | Photography |
| **Mirrorless camera** | 400-800g | Own battery | Gimbal | Pro photography |
| **Multispectral sensor** | 200-500g | 5V/12V from drone | Vibration isolated | Agriculture |
| **Thermal camera** (FLIR) | 50-300g | 5V USB | Fixed mount | Inspection, SAR |
| **LiDAR scanner** | 200-500g | 5-12V | Vibration isolated | Mapping, survey |
| **Speaker** | 100-300g | 12V amplifier | Fixed mount | Public address |
| **Spotlight** | 50-200g | Direct from battery | Fixed mount | Night operations |
| **Delivery box** | 200-500g | None | Release mechanism | Delivery |

---

## Mounting Techniques

### Vibration Isolation

    Cameras and sensors HATE vibrations from motors.

    ┌──────────────────────────────────────┐
    │  Hard mount (bad for video):         │
    │  Frame ═══ Camera                    │
    │  All vibrations transfer directly    │
    │                                      │
    │  Soft mount (good):                  │
    │  Frame ~~~ [dampener] ~~~ Camera     │
    │  Vibrations absorbed by dampener     │
    │                                      │
    │  Gimbal mount (best):                │
    │  Frame ~~~ [dampener] ~~~ [gimbal]   │
    │  Active stabilization + isolation    │
    └──────────────────────────────────────┘

    Dampener options:
    ├── Silicone O-rings (cheapest, ₹50)
    ├── TPU 3D-printed mounts (custom, ₹100 in filament)
    ├── Gel pads (good, ₹200-₹500)
    ├── Wire rope isolators (professional, ₹1,000+)
    └── Moon gel (drum dampener pads, ₹300)

### CG (Center of Gravity) Management

    The payload must not shift the CG too far from center.

    Top view:
         FRONT
      M ─────── M
      │    CG    │  ← CG should stay within
      │    ⊕     │     this center region
      M ─────── M
         BACK

    Rules:
    ├── Mount payload as close to CG as possible
    ├── If offset, add counterweight (battery position helps)
    ├── Heavier = lower (below CG for pendulum stability)
    └── Test hover before mission — trim should be near zero

---

## Gimbal Systems

### Gimbal Types

| Type | Axes | Weight | Payload Capacity | Price |
|------|------|--------|-----------------|-------|
| **1-axis (tilt)** | Pitch only | 50-100g | 200g | ₹2,000-₹5,000 |
| **2-axis (tilt+roll)** | Pitch + Roll | 100-200g | 300g | ₹5,000-₹10,000 |
| **3-axis** | Pitch + Roll + Yaw | 200-400g | 500g+ | ₹10,000-₹30,000 |

### Gimbal Control

    ArduPilot servo outputs for gimbal:
    SERVO9_FUNCTION = 6 (mount_pan / yaw)
    SERVO10_FUNCTION = 7 (mount_tilt / pitch)
    SERVO11_FUNCTION = 8 (mount_roll)

    MNT1_TYPE = 1 (servo)
    MNT1_PITCH_MIN = -90
    MNT1_PITCH_MAX = 0
    MNT1_RC_RATE = 30 (degrees per second via RC)

    # Control gimbal pitch via RC channel 6
    RC6_OPTION = 0
    # Map in SERVO10 input

---

## Power Distribution for Payloads

    Main battery → PDB / Power module
                    ├── 5V BEC → Flight controller, receiver, GPS
                    ├── 5V BEC → Camera (USB), companion computer
                    ├── 12V BEC → Gimbal, FPV camera, VTx
                    └── Direct VBAT → Spotlight, heater

    Important:
    ├── Separate BEC for payload if current > 2A
    ├── Add LC filter for camera power (reduces noise)
    ├── Fuse high-current payload circuits
    └── Never share BEC with servos + FC (servo noise crashes FC)

---

## Camera Trigger

### ArduPilot Camera Trigger for Mapping

    # Trigger camera via servo (hot shoe cable)
    CAM1_TYPE = 1 (servo)
    SERVO12_FUNCTION = 10 (camera trigger)

    # Distance-based trigger (for survey)
    CAM1_TRIGG_DIST = 10 (meters between shots)

    # Or time-based
    CAM1_TRIGG_TYPE = 0 (servo)

    # For Sony/Canon cameras: use USB trigger cable
    # For GoPro: use GoPro Labs QR code for time-lapse

---

## Custom Payload Communication

### MAVLink Payload Interface

    # Send data TO payload via MAVLink
    # Use COMMAND_LONG or DATA16/DATA32 messages

    # Receive data FROM payload
    # Use companion computer as bridge:
    Payload (UART/I2C) → RPi → MAVLink → GCS

    # Example: Read sensor on payload
    import serial
    sensor = serial.Serial('/dev/ttyAMA1', 9600)
    data = sensor.readline()
    # Forward via MAVLink STATUSTEXT or custom message

---

[← Previous: Telemetry Systems](23-telemetry-systems.md) | [Back to Index](../README.md) | [Next: Mapping & Photogrammetry →](25-mapping-photogrammetry.md)
