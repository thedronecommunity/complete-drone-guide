# Guide 37: Drone Security & Counter-Drones

[← Previous: Frame Design](36-frame-design.md) | [Back to Index](../README.md) | [Next: Commercial Drone Operations →](38-commercial-operations.md)

---

## Drone Security Threats

Drones face both cyber and physical security threats. Understanding these is critical for commercial operations and defense applications.

    ┌──────────────────────────────────────────────────┐
    │           DRONE THREAT MODEL                    │
    │                                                  │
    │  Cyber Threats:                                  │
    │  ├── RC link hijacking (command injection)       │
    │  ├── GPS spoofing (fake position data)          │
    │  ├── Telemetry interception (data leakage)      │
    │  ├── Firmware manipulation                       │
    │  └── MAVLink injection (unencrypted protocol)   │
    │                                                  │
    │  Physical Threats:                               │
    │  ├── RF jamming (overwhelm control link)        │
    │  ├── Visual tracking (camera-based)              │
    │  ├── Net/projectile capture                     │
    │  ├── Laser dazzle (blind cameras)               │
    │  └── Bird of prey (yes, really)                 │
    └──────────────────────────────────────────────────┘

---

## Securing Your Drone

### Communication Security

| Layer | Threat | Mitigation |
|-------|--------|-----------|
| **RC Link** | Hijacking | Use encrypted protocols (ELRS, Crossfire) |
| **MAVLink** | Injection | MAVLink 2 signing (message authentication) |
| **Telemetry** | Interception | Encrypt telemetry stream (AES) |
| **Video** | Interception | DJI encrypted video, or AES on analog |
| **Wi-Fi** | MITM, deauth | WPA3, disable when not needed |

### MAVLink 2 Signing

    # Enable MAVLink 2 message signing
    # This prevents unauthorized command injection

    from pymavlink import mavutil

    conn = mavutil.mavlink_connection('/dev/ttyUSB0')
    conn.setup_signing(
        b'my_secret_key_32_bytes_long!!!!!',
        sign_outgoing=True,
        allow_unsigned_callback=None  # Reject unsigned messages
    )

### GPS Spoofing Protection

    Mitigations:
    ├── Multi-constellation GPS (GPS + GLONASS + Galileo)
    ├── IMU cross-check (if GPS jumps but IMU says no movement)
    ├── Dual GPS receivers (compare positions)
    ├── GPS jamming detection (monitor signal quality)
    └── VIO/optical flow fallback (don't rely only on GPS)

    ArduPilot GPS glitch detection:
    GPS_GNSS_MODE = 7 (use all constellations)
    EK3_CHECK = 245 (enable all consistency checks)

---

## Counter-Drone Systems

### Detection Methods

| Method | Range | Accuracy | Cost | Notes |
|--------|-------|----------|------|-------|
| **Radar** | 1-10 km | High | Very high | Military/airport grade |
| **RF detection** | 1-5 km | Medium | Medium | Detects RC/video signals |
| **Acoustic** | 100-500m | Low | Low | Detects motor sound |
| **Visual (camera)** | 200m-2 km | Medium | Medium | AI-assisted tracking |
| **Thermal** | 200m-1 km | Medium | High | Works at night |

### Counter Measures

| Method | Range | Legality | Notes |
|--------|-------|----------|-------|
| **RF jamming** | 1-5 km | Illegal (most countries) | Blocks all RF in area |
| **GPS spoofing** | Variable | Illegal | Forces drone to wrong position |
| **Net gun** | 50-100m | Legal (with authority) | Physical capture |
| **Trained eagles** | Variable | Legal | Netherlands police pioneered |
| **Drone-catching drones** | 1 km | Legal (with authority) | Net-carrying interceptor |
| **Directed energy** | Variable | Military only | Laser/microwave disabling |

---

## Geofencing and No-Fly Zones

    Built-in protections:
    ├── DJI: Automatic geofencing (hardcoded no-fly zones)
    ├── ArduPilot: Configurable geofence (polygon + altitude)
    ├── PX4: Geofence with configurable actions
    └── NPNT (India): No Permission No Takeoff

    ArduPilot Fence:
    FENCE_ENABLE = 1
    FENCE_TYPE = 7 (altitude + circle + polygon)
    FENCE_ACTION = 1 (RTL on breach)
    FENCE_ALT_MAX = 120
    FENCE_RADIUS = 500

---

## Data Security

    Protecting flight data:
    ├── Encrypt onboard logs (SD card encryption)
    ├── Secure telemetry links
    ├── Wipe logs after mission (if sensitive)
    ├── Secure ground station (password-protected)
    └── Encrypted video recording

    Privacy considerations:
    ├── Avoid flying over private property
    ├── Blur faces/plates in published footage
    ├── Follow local data protection laws
    └── Inform nearby people when possible

---

[← Previous: Frame Design](36-frame-design.md) | [Back to Index](../README.md) | [Next: Commercial Drone Operations →](38-commercial-operations.md)
