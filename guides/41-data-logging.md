# Guide 41: Data Logging & Flight Analysis

[← Previous: Underwater & Hybrid Drones](40-underwater-hybrid.md) | [Back to Index](../README.md) | [Next: Drone Photography & Videography →](42-photography-videography.md)

---

## Why Log Flight Data?

Flight logs are the black box of your drone. They record everything: sensor data, commands, motor outputs, errors, and GPS tracks. Essential for debugging crashes, tuning, and compliance.

    ┌──────────────────────────────────────────────────┐
    │           WHAT GETS LOGGED                      │
    │                                                  │
    │  Sensors:     IMU, GPS, baro, compass, airspeed │
    │  Control:     RC inputs, PID outputs, setpoints  │
    │  Motors:      RPM, PWM output, current per motor │
    │  Navigation:  EKF state, position estimate       │
    │  System:      CPU load, voltage, temperature     │
    │  Events:      Mode changes, arm/disarm, errors   │
    │  Custom:      Payload data, companion messages   │
    └──────────────────────────────────────────────────┘

---

## ArduPilot Logging

### Log Types

| Log Type | Storage | Access | Use Case |
|----------|---------|--------|----------|
| **Dataflash logs** | SD card on FC | Download post-flight | Full analysis |
| **Telemetry logs (.tlog)** | GCS computer | Real-time | GCS-side record |

### Enabling Detailed Logging

    # Log bitmask (enable all)
    LOG_BITMASK = 65535

    # Or selective:
    # Bit 0: Fast Attitude
    # Bit 1: Medium Attitude
    # Bit 2: GPS
    # Bit 3: System Performance
    # Bit 7: IMU
    # Bit 9: Motors
    # Bit 10: Camera

    # Log file format
    LOG_FILE_DSRMROT = 1 (new file each disarm)
    LOG_FILE_BUFSIZE = 200 (KB, buffer size)

### Downloading Logs

    # Via Mission Planner:
    # DataFlash Logs → Download DataFlash Log via Mavlink

    # Via MAVProxy:
    log list
    log download latest

    # Via MAVLink directly (pymavlink):
    # Use LOG_REQUEST_LIST and LOG_REQUEST_DATA messages

---

## Betaflight Blackbox

    # Enable Blackbox
    set blackbox_device = SPIFLASH  # or SDCARD
    set blackbox_rate_num = 1
    set blackbox_rate_denom = 2  # Log at half gyro rate

    # Download via Betaflight Configurator:
    # Blackbox tab → Save to file

    # Analyze with:
    ├── Betaflight Blackbox Explorer
    ├── PIDtoolbox (advanced PID analysis)
    └── PlasmaTree Blackbox Log Viewer

---

## Log Analysis Tools

| Tool | Platform | Best For | Price |
|------|----------|----------|-------|
| **UAV Log Viewer** | Web (plot.ardupilot.org) | ArduPilot quick analysis | Free |
| **Mission Planner** | Windows | ArduPilot detailed analysis | Free |
| **MAVExplorer** | Python | Scripted analysis | Free |
| **Blackbox Explorer** | Desktop | Betaflight PID analysis | Free |
| **PIDtoolbox** | MATLAB | Advanced PID + filter tuning | Free |
| **FlightPlot** | Java | PX4 log analysis | Free |

### UAV Log Viewer (Quickest)

    1. Go to https://plot.ardupilot.org
    2. Upload .bin log file
    3. Click any data field to plot
    4. Common plots:
       ├── ATT (attitude: roll, pitch, yaw)
       ├── GPS (position, speed, satellites)
       ├── BAT (battery voltage, current)
       ├── RCIN (RC stick inputs)
       ├── RCOU (motor outputs)
       ├── VIBE (vibration levels)
       └── EKF (filter health)

---

## Post-Crash Analysis

    Step-by-step crash investigation:

    1. Download log immediately (before battery dies)
    2. Check RCIN — was pilot input the cause?
    3. Check RCOU — did motors respond correctly?
    4. Check ERR — any error codes logged?
    5. Check VIBE — was vibration excessive?
    6. Check EKF — did navigation fail?
    7. Check BAT — was there a power issue?
    8. Check GPS — was position accurate?

    Common crash signatures:
    ├── Motor failure: One RCOU channel maxes out while others compensate
    ├── Compass error: Sudden yaw change, toilet bowl before crash
    ├── Brown-out: Battery voltage drops to 0V in log
    ├── Pilot error: RCIN shows unintended input
    ├── Flyaway: GPS glitch + compass error combination
    └── Prop strike: Sudden vibration spike followed by attitude error

---

## Vibration Analysis

    Good vibration levels (ArduPilot):
    ├── X axis: < 15 m/s²
    ├── Y axis: < 15 m/s²
    ├── Z axis: < 15 m/s² (offset from -9.8)
    └── Clipping: 0 events

    Causes of bad vibration:
    ├── Unbalanced props (most common)
    ├── Bent motor shaft
    ├── Loose motor screws
    ├── FC not soft-mounted
    ├── Loose frame components
    └── Prop damaged/chipped

    Fix: Balance props, soft-mount FC, tighten everything

---

[← Previous: Underwater & Hybrid Drones](40-underwater-hybrid.md) | [Back to Index](../README.md) | [Next: Drone Photography & Videography →](42-photography-videography.md)
