# Guide 23: Telemetry Systems

[← Previous: Ground Control Stations](22-ground-control-stations.md) | [Back to Index](../README.md) | [Next: Payload Integration →](24-payload-integration.md)

---

## What Is Telemetry?

Telemetry is the wireless transmission of flight data from the drone to the ground. It lets you see attitude, GPS position, battery voltage, altitude, speed, and sensor health in real time.

    ┌──────────────────────────────────────────────────┐
    │              TELEMETRY DATA FLOW                 │
    │                                                  │
    │  Flight Controller                               │
    │  ├── IMU data (attitude, acceleration)           │
    │  ├── GPS (position, speed, satellites)           │
    │  ├── Battery (voltage, current, remaining)       │
    │  ├── RC input (stick positions)                  │
    │  ├── Motor outputs                               │
    │  └── Sensor health                               │
    │         │                                        │
    │         ↓ UART (MAVLink / CRSF / MSP)           │
    │  Telemetry Radio (air module)                    │
    │         │                                        │
    │         ↓ RF (433/868/915 MHz or 2.4 GHz)       │
    │  Telemetry Radio (ground module)                 │
    │         │                                        │
    │         ↓ USB/UART                               │
    │  GCS Software                                    │
    └──────────────────────────────────────────────────┘

---

## Telemetry Protocols

| Protocol | Used By | Data Rate | Direction | Primary Use |
|----------|---------|-----------|-----------|-------------|
| **MAVLink** | ArduPilot, PX4 | 57600 baud | Bidirectional | Full GCS telemetry |
| **CRSF (Crossfire)** | TBS, ELRS | Variable | Bidirectional | RC + telemetry |
| **FrSky SmartPort** | FrSky | 9600 baud | Bidirectional | RC + basic telemetry |
| **MSP** | Betaflight | 115200 baud | Bidirectional | Configurator + OSD |
| **LTM** | iNav | 9600 baud | One-way | Lightweight telemetry |

---

## MAVLink Telemetry (ArduPilot/PX4)

### MAVLink Message Types

| Message | Frequency | Contains |
|---------|-----------|---------|
| HEARTBEAT | 1 Hz | Alive status, flight mode, armed state |
| ATTITUDE | 10-50 Hz | Roll, pitch, yaw angles |
| GLOBAL_POSITION_INT | 5-10 Hz | Lat, lon, alt, velocity |
| SYS_STATUS | 1-2 Hz | Battery voltage, current, CPU load |
| GPS_RAW_INT | 5 Hz | GPS fix type, satellites, HDOP |
| VFR_HUD | 5 Hz | Airspeed, groundspeed, heading, throttle |
| RC_CHANNELS | 5 Hz | All RC channel values |
| STATUSTEXT | Event-based | Text messages (warnings, errors) |

### Setting Up SiK Radio Telemetry

    Hardware connection:
    Flight Controller TELEM1 port ←→ SiK Air Module
    SiK Ground Module ←→ USB to Laptop

    Wiring (air side):
    FC TELEM1 TX ──→ SiK RX
    FC TELEM1 RX ──→ SiK TX
    FC 5V ──────────→ SiK VCC
    FC GND ─────────→ SiK GND

    ArduPilot Parameters:
    SERIAL1_PROTOCOL = 2 (MAVLink2)
    SERIAL1_BAUD = 57 (57600)

    SiK Radio Configuration (via Mission Planner):
    ├── Baud: 57600
    ├── Air Speed: 64
    ├── Net ID: 25 (match both radios)
    ├── Tx Power: 20 (max)
    ├── ECC: On
    ├── Mavlink: MavLink2
    └── Duty Cycle: 100%

---

## ELRS Telemetry

ExpressLRS provides RC control AND telemetry in one system:

    ELRS Telemetry Sensors:
    ├── Battery voltage
    ├── Battery current
    ├── GPS position
    ├── GPS speed
    ├── GPS altitude
    ├── Satellite count
    ├── Flight mode
    ├── Pitch / Roll / Yaw
    └── Link quality (LQ)

    Setup (Betaflight):
    # Enable CRSF on UART
    set serialrx_provider = CRSF

    # In Ports tab: Set UART to "Serial RX"
    # ELRS automatically sends telemetry back via CRSF

    Viewing on radio:
    ├── EdgeTX/OpenTX: Telemetry screen shows all sensors
    ├── Custom LUA scripts for OSD on radio screen
    └── Real-time voice alerts (altitude, battery, etc.)

---

## OSD (On-Screen Display)

OSD overlays telemetry data on your FPV video feed:

    ┌──────────────────────────────────────────┐
    │ 14.8V  32A     GPS: 12 sats    STAB     │
    │                                          │
    │ ALT: 42m                    SPD: 12 m/s  │
    │                                          │
    │          ───┼───                         │
    │             │                            │
    │                                          │
    │ mAh: 850    RSSI: -65    DIST: 230m      │
    │ Timer: 3:24              HOME: 180°      │
    └──────────────────────────────────────────┘

### OSD Configuration (Betaflight)

    # In Betaflight Configurator → OSD tab:
    # Drag and drop elements to position them

    Essential OSD elements:
    ├── Battery voltage (top left)
    ├── Current draw (top left)
    ├── mAh used (bottom left)
    ├── Flight timer (bottom left)
    ├── RSSI / Link quality (top right)
    ├── GPS coordinates (bottom right)
    ├── Altitude (right side)
    ├── Speed (right side)
    ├── Crosshair (center)
    ├── Warnings (center, auto-triggers)
    └── Flight mode (top right)

---

## Cellular Telemetry (4G/5G)

For beyond visual line of sight (BVLOS) or long-range operations:

    ┌────────────────────────────────────────┐
    │  Drone → 4G Module → Cell Tower →     │
    │  Internet → Cloud Server → GCS        │
    │                                        │
    │  Hardware options:                     │
    │  ├── SIM7600 4G HAT (Raspberry Pi)    │
    │  ├── Quectel EC25 module              │
    │  └── USB 4G dongle                    │
    │                                        │
    │  Protocol: MQTT (lightweight pub/sub) │
    │  Latency: 50-200ms                    │
    │  Range: Wherever cell coverage exists  │
    └────────────────────────────────────────┘

### MQTT Telemetry Setup

    # On drone (Raspberry Pi companion)
    import paho.mqtt.client as mqtt
    from pymavlink import mavutil

    broker = "your-server.com"
    client = mqtt.Client("drone_01")
    client.connect(broker, 1883)

    conn = mavutil.mavlink_connection('/dev/ttyACM0', baud=115200)

    while True:
        msg = conn.recv_match(type='GLOBAL_POSITION_INT', blocking=True)
        payload = {
            "lat": msg.lat / 1e7,
            "lon": msg.lon / 1e7,
            "alt": msg.relative_alt / 1000,
            "hdg": msg.hdg / 100
        }
        client.publish("drone/01/position", str(payload))

---

## Telemetry Range and Reliability

| System | Typical Range | Frequency | Reliability |
|--------|-------------|-----------|-------------|
| SiK 433MHz | 1-2 km | 433 MHz | Very good |
| SiK 915MHz | 1-2 km | 915 MHz | Very good |
| RFD900x | 40+ km | 900 MHz | Excellent |
| ELRS 900MHz | 30+ km | 868/915 MHz | Excellent |
| ELRS 2.4GHz | 10+ km | 2.4 GHz | Good |
| Wi-Fi | 100-300m | 2.4/5 GHz | Fair |
| 4G LTE | Cell coverage | Cellular | Good (coverage dependent) |

---

[← Previous: Ground Control Stations](22-ground-control-stations.md) | [Back to Index](../README.md) | [Next: Payload Integration →](24-payload-integration.md)
