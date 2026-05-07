# Guide 22: Ground Control Stations

[← Previous: 3D Printing](21-3d-printing.md) | [Back to Index](../README.md) | [Next: Telemetry Systems →](23-telemetry-systems.md)

---

## What is a GCS?

A Ground Control Station is the software and hardware you use to plan missions, monitor flights, and control your drone from the ground.

    ┌──────────────────────────────────────────────────┐
    │              GCS COMPONENTS                      │
    │                                                  │
    │  Software:                                       │
    │  ├── Map display (satellite/terrain)             │
    │  ├── Flight instruments (HUD)                    │
    │  ├── Mission planner (waypoints)                 │
    │  ├── Parameter editor                            │
    │  ├── Telemetry display                           │
    │  └── Log viewer                                  │
    │                                                  │
    │  Hardware:                                       │
    │  ├── Laptop/tablet                               │
    │  ├── Telemetry radio (ground module)             │
    │  ├── Antenna (directional for long range)        │
    │  └── Optional: joystick, tracker antenna         │
    └──────────────────────────────────────────────────┘

---

## GCS Software Comparison

| Software | OS | Firmware | Best For | Price |
|----------|------|---------|----------|-------|
| **Mission Planner** | Windows | ArduPilot | Full-featured, desktop ops | Free |
| **QGroundControl** | All | ArduPilot + PX4 | Cross-platform, clean UI | Free |
| **APM Planner 2** | All | ArduPilot | Lightweight alternative | Free |
| **MAVProxy** | Linux/Mac | ArduPilot | CLI power users | Free |
| **Tower (DroidPlanner)** | Android | ArduPilot | Field ops on phone | Free |
| **Solex** | Android | ArduPilot | Advanced Android GCS | ₹1,500 |
| **UgCS** | Desktop | Multiple | Survey/mapping missions | Paid |

### Mission Planner (Recommended for ArduPilot)

    Installation:
    1. Download from https://firmware.ardupilot.org/Tools/MissionPlanner/
    2. Install (Windows only, or Wine on Linux)
    3. Connect via telemetry radio (COM port) or TCP/UDP

    Key Features:
    ├── Auto-waypoint generation for survey
    ├── Terrain following mission planning
    ├── Full parameter tree editor
    ├── Log analysis and replay
    ├── Firmware update over telemetry
    ├── Joystick control
    └── Camera trigger planning

### QGroundControl (Cross-Platform)

    Installation:
    # Linux
    chmod +x QGroundControl.AppImage
    ./QGroundControl.AppImage

    # Or via Flatpak
    flatpak install flathub org.mavlink.qgroundcontrol

    Key Features:
    ├── Works with ArduPilot AND PX4
    ├── Mobile version (Android/iOS)
    ├── Clean, modern interface
    ├── Vehicle setup wizard
    ├── Offline maps
    └── MAVLink inspector

### MAVProxy (CLI)

    # Install
    pip install pymavlink mavproxy

    # Connect to serial telemetry
    mavproxy.py --master=/dev/ttyUSB0 --baudrate=57600

    # Connect to SITL
    mavproxy.py --master=tcp:127.0.0.1:5760

    # Connect to UDP stream
    mavproxy.py --master=udp:0.0.0.0:14550

    # Useful commands
    mode GUIDED          # Switch flight mode
    arm throttle         # Arm motors
    wp list              # List waypoints
    param show ATC*      # Show attitude control params
    param set FENCE_ENABLE 1  # Set parameter
    module load map      # Load map module
    module load console  # Load instrument console

---

## Mission Planning

### Waypoint Mission

    ┌──────────────────────────────────────────┐
    │           MISSION PLANNING               │
    │                                          │
    │   Set waypoints on map:                  │
    │                                          │
    │   ① Home (auto-set on arm)              │
    │   ② Takeoff (altitude)                   │
    │   ③ Waypoint 1 (lat, lon, alt)          │
    │   ④ Waypoint 2 + action (photo)         │
    │   ⑤ Waypoint 3 (speed change)           │
    │   ⑥ RTL (return to launch)              │
    │                                          │
    │   Each waypoint can have:                │
    │   ├── Position (lat, lon, alt)           │
    │   ├── Speed                              │
    │   ├── Delay (loiter time)                │
    │   ├── Actions (camera, servo, yaw)       │
    │   └── Acceptance radius                  │
    └──────────────────────────────────────────┘

### Survey/Grid Mission

    For mapping or agricultural survey:

    ┌──────────────────────────┐
    │  ←─────────────────────  │
    │  ─────────────────────→  │
    │  ←─────────────────────  │
    │  ─────────────────────→  │
    │  ←─────────────────────  │
    │  ─────────────────────→  │
    └──────────────────────────┘

    Parameters:
    ├── Altitude: 50-100m (varies by camera)
    ├── Overlap: 70-80% front, 60-70% side
    ├── Speed: 5-10 m/s
    ├── Camera trigger: Distance interval
    └── Turnaround: Smooth curves or stop-and-go

---

## Custom GCS Development

### Python + MAVLink (Minimal GCS)

    from pymavlink import mavutil
    import time

    # Connect
    conn = mavutil.mavlink_connection('/dev/ttyUSB0', baud=57600)
    conn.wait_heartbeat()
    print(f"Connected to system {conn.target_system}")

    # Request data streams
    conn.mav.request_data_stream_send(
        conn.target_system, conn.target_component,
        mavutil.mavlink.MAV_DATA_STREAM_ALL, 4, 1)

    # Read telemetry loop
    while True:
        msg = conn.recv_match(blocking=True, timeout=1)
        if msg:
            msg_type = msg.get_type()
            if msg_type == 'GLOBAL_POSITION_INT':
                lat = msg.lat / 1e7
                lon = msg.lon / 1e7
                alt = msg.relative_alt / 1000
                print(f"Position: {lat:.6f}, {lon:.6f}, Alt: {alt:.1f}m")
            elif msg_type == 'SYS_STATUS':
                voltage = msg.voltage_battery / 1000
                current = msg.current_battery / 100
                remaining = msg.battery_remaining
                print(f"Battery: {voltage:.1f}V, {current:.1f}A, {remaining}%")

### Web-Based GCS (Node.js + Leaflet)

    A web GCS lets you monitor from any browser:

    Architecture:
    Drone ←→ Telemetry Radio ←→ Serial ←→ Node.js Server ←→ WebSocket ←→ Browser

    npm packages:
    ├── serialport (read telemetry radio)
    ├── node-mavlink (parse MAVLink)
    ├── ws (WebSocket server)
    └── express (serve web UI)

    Frontend:
    ├── Leaflet.js (map)
    ├── Leaflet.Rotatedmarker (drone icon)
    └── Chart.js (telemetry graphs)

---

## Hardware Setup

### Telemetry Radio Configuration

| Radio | Range | Frequency | Interface | Price |
|-------|-------|-----------|-----------|-------|
| **SiK 433MHz** | 1-2 km | 433 MHz | UART | ₹1,500-₹2,500 |
| **SiK 915MHz** | 1-2 km | 915 MHz | UART | ₹1,500-₹2,500 |
| **RFD900x** | 40+ km | 900 MHz | UART | ₹15,000+ |
| **ESP32 Wi-Fi** | 100-300m | 2.4 GHz | Wi-Fi | ₹500 |
| **4G Module** | Unlimited | Cellular | USB/UART | ₹3,000-₹5,000 |

### Antenna Upgrades

    Stock dipole: 2 dBi, omnidirectional, ~2 km
    Patch antenna: 8-12 dBi, directional, ~5-10 km
    Yagi antenna: 12-16 dBi, narrow beam, ~15-20 km
    Tracking antenna: Auto-points at drone for max range

---

[← Previous: 3D Printing](21-3d-printing.md) | [Back to Index](../README.md) | [Next: Telemetry Systems →](23-telemetry-systems.md)
