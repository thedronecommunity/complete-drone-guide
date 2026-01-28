# Guide 05: Software Architecture

[← Previous: Drone Types](04-drone-types.md) | [Back to Index](../README.md) | [Next: Communication →](06-communication-protocols.md)

---

## Three-Layer System

Modern drones use a layered software architecture:

```
┌─────────────────────────────────────────────────────────────┐
│  LAYER 3: GROUND CONTROL (Laptop/Tablet)                    │
│  ─────────────────────────────────────────                  │
│  User interface, monitoring, mission planning               │
│  Examples: QGroundControl, Mission Planner                  │
│  Response: User interaction speed                           │
├─────────────────────────────────────────────────────────────┤
│  LAYER 2: COMPANION COMPUTER (Raspberry Pi)                 │
│  ──────────────────────────────────────────                 │
│  Complex tasks: AI, computer vision, data logging           │
│  Communication: MAVLink                                     │
│  Response: 50-200ms                                         │
├─────────────────────────────────────────────────────────────┤
│  LAYER 1: FIRMWARE (Flight Controller)                      │
│  ─────────────────────────────────────                      │
│  Real-time: motor control, sensor fusion, stabilization     │
│  Examples: ArduPilot, PX4, Betaflight                       │
│  Response: <1ms                                             │
└─────────────────────────────────────────────────────────────┘
```

### Why Three Layers?

| Layer | Responsibility | Why Separate? |
|-------|---------------|---------------|
| **Firmware** | Keep drone flying | Needs real-time, safety-critical |
| **Companion** | Smart decisions | Needs compute power, flexibility |
| **Ground** | Human interface | Needs display, input devices |

---

## Layer 1: Firmware

### ArduPilot

The most popular open-source autopilot firmware.

**Philosophy:** Stability first, extensive testing, community-driven.

**Flight Modes (Copter):**

| Mode | Description | Use Case |
|------|-------------|----------|
| **Stabilize** | Manual + auto-level | Learning, aerobatics |
| **Alt Hold** | Maintains altitude | Easy flying |
| **Loiter** | Holds GPS position | Photography, inspection |
| **Auto** | Follows waypoints | Autonomous missions |
| **RTL** | Return to launch | Emergency, end of flight |
| **Land** | Controlled landing | Autonomous landing |
| **Guided** | Real-time commands | Computer control |
| **Acro** | Full manual | Expert flying |

**Advanced Features:**
- Precision landing (camera/beacon)
- Object avoidance
- Follow Me mode
- Terrain following
- Geofencing
- Rally points

### PX4

Research-focused autopilot with cutting-edge features.

**Philosophy:** Innovation first, academic partnerships.

**Key Differences from ArduPilot:**

| Feature | ArduPilot | PX4 |
|---------|-----------|-----|
| New algorithms | Slower adoption | Faster adoption |
| Stability | Very stable | Sometimes experimental |
| ROS integration | Via MAVLink | Native support |
| Simulation | SITL available | Strong support |
| Custom modes | Requires modification | Built-in framework |

### Betaflight

Racing and freestyle focused firmware.

**Philosophy:** Maximum performance for FPV flying.

**Features:**
- Ultra-fast loop times (8kHz+)
- Extensive PID tuning options
- OSD (On-Screen Display) configuration
- Blackbox logging for analysis
- Active community with rapid updates

**Not suitable for:** Autonomous flight, GPS navigation, commercial use.

### Firmware Configuration

All firmwares have 1000+ parameters:

| Category | Examples |
|----------|----------|
| **Frame setup** | Motor layout, arm length |
| **Sensor orientation** | FC mounting direction |
| **Radio channels** | Channel mapping, endpoints |
| **Battery monitoring** | Voltage calibration, failsafe |
| **PID gains** | Stability tuning |
| **Filters** | Noise reduction |
| **Failsafes** | RTL triggers, battery actions |

### Tuning Process

```
1. Start with defaults
       ↓
2. Basic PID tuning (or auto-tune)
       ↓
3. Advanced refinement
       ↓
4. Flight testing
       ↓
5. Iterative improvement
       ↓
   (repeat 4-5 until satisfied)
```

---

## Layer 2: Companion Computer

### Why Add a Companion Computer?

**Flight controller limitations:**
- Few megabytes memory
- Simple processor (real-time only)
- No internet connectivity
- Limited to C++ typically

**Companion computer advantages:**
- Gigabytes of memory
- Python, Node.js, any language
- WiFi/Ethernet
- Camera interfaces
- GPIO for additional sensors
- AI/ML capability

### Common Companion Computers

| Computer | CPU | RAM | Power | Best For |
|----------|-----|-----|-------|----------|
| Raspberry Pi 4 | Quad-core 1.5GHz | 4-8GB | 5V/3A | General purpose |
| Raspberry Pi 5 | Quad-core 2.4GHz | 4-8GB | 5V/5A | More compute |
| Jetson Nano | Quad-core + GPU | 4GB | 5V/4A | Computer vision |
| Jetson Orin | 8-core + GPU | 8-16GB | 15W | Advanced AI |

### Communication with FC

Companion computer communicates with flight controller via **MAVLink protocol** over:
- Serial (UART) - most common
- USB - convenient for testing
- Ethernet - higher bandwidth

---

## Layer 3: Ground Control Software

### QGroundControl

Cross-platform ground control for ArduPilot and PX4.

**Pre-flight Functions:**
- Firmware install/update
- Sensor calibration
- Radio setup
- Parameter configuration

**Flight Operations:**
- Real-time telemetry display
- Manual override capability
- Emergency controls
- Flight mode switching

**Mission Planning:**
- Visual waypoint creation
- Survey pattern generation
- Mission validation
- Upload/download missions

**Post-flight:**
- Flight log review
- Performance metrics
- Parameter backup/restore

### Mission Planner

Windows-focused ground control for ArduPilot.

**Advantages over QGC:**
- More detailed parameter editing
- Advanced scripting
- Better log analysis
- 3D flight visualization

**Disadvantages:**
- Windows only
- Steeper learning curve
- Older interface

### Choosing Ground Control

| Need | Choose |
|------|--------|
| Cross-platform | QGroundControl |
| Advanced tuning | Mission Planner |
| PX4 development | QGroundControl |
| Scripting/automation | Mission Planner |
| Simple interface | QGroundControl |

---

## Configuration Workflow

### Initial Setup

```
1. Install firmware
   └── QGC: Vehicle Setup → Firmware
   
2. Select frame type
   └── Quad X, Hexa, etc.
   
3. Calibrate sensors
   ├── Accelerometer (6 positions)
   ├── Gyroscope (hold still)
   ├── Compass (rotate all axes)
   └── Level horizon
   
4. Configure radio
   ├── Bind receiver
   ├── Calibrate sticks
   └── Set flight modes
   
5. Configure battery
   ├── Cell count
   ├── Voltage calibration
   └── Failsafe thresholds
   
6. Safety settings
   ├── Geofence
   ├── RTL altitude
   └── Failsafe actions
```

### Parameter Categories

| Category | Key Parameters | Purpose |
|----------|---------------|---------|
| **ARMING** | ARMING_CHECK | Pre-arm safety checks |
| **BATT** | BATT_CAPACITY, BATT_LOW_VOLT | Battery monitoring |
| **COMPASS** | COMPASS_USE, COMPASS_DEC | Compass configuration |
| **EKF** | EK2_*, EK3_* | State estimation |
| **FENCE** | FENCE_ENABLE, FENCE_ALT_MAX | Geofencing |
| **GPS** | GPS_TYPE, GPS_AUTO_CONFIG | GPS settings |
| **INS** | INS_GYRO_FILTER, INS_ACCEL_FILTER | Sensor filtering |
| **MOT** | MOT_PWM_MIN, MOT_PWM_MAX | Motor output |
| **PSC** | PSC_POSXY_P, PSC_VELXY_P | Position control |
| **RC** | RC*_MIN, RC*_MAX | Radio calibration |
| **SERIAL** | SERIAL*_PROTOCOL, SERIAL*_BAUD | Serial ports |

---

## Flight Modes Deep Dive

### Stabilize (Manual)

```
Pilot input → Flight controller → Motor adjustment
                    ↓
              (Auto-level only, no altitude/position hold)
```

- Pilot controls attitude directly
- FC only provides leveling
- Requires constant attention
- Good for learning control feel

### Altitude Hold

```
Pilot input → Flight controller → Motor adjustment
                    ↓
              (Barometer maintains altitude)
```

- Throttle stick = climb rate, not motor power
- Release stick = hold current altitude
- Still need to control position manually

### Loiter (GPS Hold)

```
Pilot input → Flight controller → Motor adjustment
                    ↓
              (GPS + Barometer maintain position)
```

- Release sticks = hold position and altitude
- Move sticks = move relative to current position
- Requires good GPS lock (8+ satellites)

### Auto (Waypoint)

```
Mission → Flight controller → Motor adjustment
                    ↓
              (Autonomous flight along waypoints)
```

- Follows pre-programmed mission
- No pilot input required (but can override)
- Executes commands at each waypoint

### Guided (Computer Control)

```
Companion computer → Flight controller → Motor adjustment
                            ↓
                      (Real-time commands)
```

- External computer sends position/velocity targets
- FC handles stabilization and navigation
- Used for AI, computer vision, dynamic missions

---

## Safety Systems

### Failsafes

| Trigger | Default Action | Configurable? |
|---------|---------------|---------------|
| Radio loss | RTL | Yes |
| Low battery | RTL then Land | Yes |
| GPS loss | Land or continue | Yes |
| Geofence breach | RTL | Yes |
| Motor failure (hex+) | Continue degraded | Yes |

### Pre-arm Checks

Before arming, the FC verifies:
- Accelerometer calibrated
- Gyroscope calibrated
- Compass calibrated
- GPS lock (if required)
- Radio calibrated
- Battery voltage OK
- No critical errors

Can be disabled with `ARMING_CHECK` parameter (not recommended).

### Geofencing

Virtual boundaries that trigger failsafe:

| Fence Type | Function |
|------------|----------|
| **Altitude max** | Prevents flying too high |
| **Altitude min** | Prevents flying too low |
| **Circle** | Maximum distance from home |
| **Polygon** | Custom inclusion/exclusion zones |

---

## Logging and Analysis

### Flight Logs

Automatically recorded data:
- GPS position, altitude, speed
- Attitude (roll, pitch, yaw)
- Motor outputs
- Battery voltage/current
- RC inputs
- Sensor readings
- Flight mode changes
- Error messages

### Log Analysis Tools

| Tool | Platform | Features |
|------|----------|----------|
| **QGC Log Viewer** | All | Basic plots, timeline |
| **Mission Planner** | Windows | Detailed analysis, 3D path |
| **MAVExplorer** | All | Command-line, scripting |
| **Flight Review** | Web | PX4 online analysis |

### What to Look For

| Issue | Log Signature |
|-------|---------------|
| Vibration | IMU noise, motor oscillation |
| Compass interference | Large heading errors |
| GPS glitches | Position jumps |
| Power issues | Voltage drops under load |
| PID problems | Oscillation in attitude |

---

## Key Takeaways

1. **Three layers**: Firmware (real-time), Companion (smart), Ground (human)
2. **ArduPilot**: Stability-focused, great for beginners and commercial
3. **PX4**: Innovation-focused, great for research
4. **Betaflight**: Racing-focused, no autonomous capability
5. **QGroundControl**: Cross-platform, good for most users
6. **Always configure failsafes** before flying

---

[← Previous: Drone Types](04-drone-types.md) | [Back to Index](../README.md) | [Next: Communication →](06-communication-protocols.md)
