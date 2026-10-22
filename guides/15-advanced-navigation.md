# Guide 15: Advanced Navigation & Sensors

[← Previous: Custom PCB Design](14-custom-pcb-design.md) | [Back to Index](../README.md)

---

## Beyond GPS

GPS works great outdoors with clear sky. But drones need to fly indoors, under bridges, in warehouses, through forests, and in GPS-denied environments. This guide covers the sensors and techniques that make that possible.

```
┌──────────────────────────────────────────────────┐
│           NAVIGATION SENSOR HIERARCHY            │
│                                                  │
│  GPS + Compass          ← Outdoor, open sky      │
│     │                                            │
│     ↓ (fails indoors)                            │
│  Optical Flow + Rangefinder  ← Indoor hover      │
│     │                                            │
│     ↓ (needs texture, stable surface)            │
│  Visual Inertial Odometry   ← Any environment    │
│     │                                            │
│     ↓ (adds mapping)                             │
│  SLAM (full mapping + localization)              │
│     │                                            │
│     ↓ (adds obstacle avoidance)                  │
│  Full Autonomous Navigation ← The goal           │
└──────────────────────────────────────────────────┘
```

---

## Optical Flow

Optical flow uses a downward-facing camera to track ground movement, like a computer mouse tracks your desk surface.

### How It Works

```
Frame 1          Frame 2
┌─────────┐      ┌─────────┐
│  * . .  │      │ . * . . │   ← Ground features shift
│ . . * . │  →   │. . . * .│      between frames
│. * . . .│      │ * . . . │
└─────────┘      └─────────┘

Pixel shift = drone movement (with known altitude)
```

The sensor compares consecutive image frames and calculates how much the ground pattern has shifted. Combined with a rangefinder (to know altitude), this gives you horizontal velocity — enough for stable hover without GPS.

### Common Optical Flow Sensors

| Sensor | Resolution | Interface | Max Speed | Price |
|--------|-----------|-----------|-----------|-------|
| **PMW3901** | 35×35 px | SPI | ~1.5 m/s | ₹600-₹1,000 |
| **PAA5100JE** | 35×35 px | SPI | ~2 m/s | ₹800-₹1,200 |
| **PX4FLOW** | 64×64 px | I2C/UART | ~3 m/s | ₹3,000-₹5,000 |
| **Camera-based** | Variable | USB/CSI | Variable | ₹1,000+ |

### Rangefinders (Paired with Optical Flow)

| Sensor | Type | Range | Interface | Price |
|--------|------|-------|-----------|-------|
| **VL53L1X** | ToF laser | 4m | I2C | ₹400-₹700 |
| **VL53L5CX** | ToF array (8×8) | 4m | I2C | ₹1,500 |
| **TFMini-S** | LiDAR | 12m | UART | ₹2,500 |
| **Benewake TF-Luna** | LiDAR | 8m | UART/I2C | ₹2,000 |
| **Ultrasonic (HC-SR04)** | Sound | 4m | GPIO | ₹100 |

### Optical Flow Limitations

| Limitation | Why | Workaround |
|------------|-----|------------|
| Needs textured ground | Can't track featureless surfaces | Don't fly over glass, water, plain concrete |
| Height limited | Most sensors work < 3-5m | Use LiDAR rangefinder for higher altitude |
| Speed limited | Blur at high speed | Keep under 1-2 m/s |
| Lighting dependent | Needs visible ground features | Add LED for dark environments |
| Drift over time | No absolute position reference | Fuse with other sensors |

### ArduPilot Optical Flow Setup

```
Parameters:
FLOW_TYPE = 6 (PMW3901)
RNGFND1_TYPE = 25 (VL53L1X via I2C)
RNGFND1_MAX_CM = 400
RNGFND1_MIN_CM = 5

EK3_SRC1_POSXY = 0  (None — no GPS)
EK3_SRC1_VELXY = 5  (Optical Flow)
EK3_SRC1_POSZ = 1   (Rangefinder)

Flight Mode:
FLTMODE_CH = LOITER (uses optical flow for position hold)
```

---

## Visual Inertial Odometry (VIO)

VIO fuses camera images with IMU data to estimate position and orientation — no GPS, no ground requirements, works in 3D space.

### How It Works

```
┌──────────────────────────────────────────────────┐
│                  VIO PIPELINE                     │
│                                                  │
│  Stereo Camera ──→ Feature Detection             │
│       +            (corners, edges)              │
│  IMU Data ──────→ Feature Tracking               │
│                    (frame to frame)              │
│                        │                         │
│                        ↓                         │
│              State Estimation                    │
│              (Extended Kalman Filter              │
│               or Factor Graph)                   │
│                        │                         │
│                        ↓                         │
│              Position + Orientation              │
│              (6 DOF pose estimate)               │
└──────────────────────────────────────────────────┘
```

**Key insight:** IMU gives high-frequency motion data (500-1000Hz) but drifts. Camera gives low-frequency position fixes (30-60Hz) but with no drift. VIO fuses both to get the best of each.

### VIO Hardware Options

| Platform | Camera | Compute | Weight | Cost |
|----------|--------|---------|--------|------|
| **Intel RealSense T265** | Stereo fisheye + IMU | Built-in VPU | 55g | Discontinued (find used) |
| **Intel RealSense D435i** | Stereo depth + IMU | Needs companion | 72g | ₹20,000+ |
| **OAK-D Lite** | Stereo + IMU | Built-in VPU | 61g | ₹8,000-₹12,000 |
| **Raspberry Pi + Stereo cam** | Custom stereo | Pi 4/5 compute | 100g+ | ₹5,000-₹10,000 |

### VIO Software

| Algorithm | Runs On | Source |
|-----------|---------|--------|
| **VINS-Fusion** | CPU (ARM/x86) | HKUST Aerial Robotics |
| **ORB-SLAM3** | CPU (ARM/x86) | Universidad de Zaragoza |
| **MSCKF** | CPU | Multiple implementations |
| **SVO2** | CPU | University of Zurich (RPG) |
| **Kimera-VIO** | CPU | MIT SPARK Lab |

### VIO + ArduPilot Integration

```
Camera+VPU ──→ ROS2 ──→ MAVROS ──→ ArduPilot

Message flow:
VIO node publishes: /mavros/vision_pose/pose (geometry_msgs/PoseStamped)
ArduPilot receives: VISION_POSITION_ESTIMATE MAVLink message

ArduPilot Parameters:
VISO_TYPE = 1 (MAVLink)
EK3_SRC1_POSXY = 6 (ExternalNav)
EK3_SRC1_POSZ = 6 (ExternalNav)
EK3_SRC1_YAW = 6 (ExternalNav)
```

---

## SLAM (Simultaneous Localization and Mapping)

SLAM builds a map of the environment while tracking the drone's position within it. It's VIO plus persistent mapping.

### Types of SLAM

| Type | Sensor | Map Output | Best For |
|------|--------|------------|----------|
| **Visual SLAM** | Camera(s) | 3D point cloud | Indoor, lightweight |
| **LiDAR SLAM** | 2D/3D LiDAR | Occupancy grid | Precise mapping, outdoors |
| **Visual-Inertial SLAM** | Camera + IMU | Dense/sparse map | General purpose |

### LiDAR Sensors for Drones

| Sensor | Type | Range | Points/sec | Weight | Price |
|--------|------|-------|-----------|--------|-------|
| **RPLIDAR A1** | 2D, 360° | 12m | 8000 | 170g | ₹8,000 |
| **RPLIDAR A2** | 2D, 360° | 18m | 16000 | 190g | ₹15,000 |
| **Livox Mid-360** | 3D | 40m | 200K | 265g | ₹45,000 |
| **Ouster OS0** | 3D, 360° | 35m | 1.3M | 447g | ₹1,00,000+ |

**For sub-250g builds:** 2D/3D LiDAR is too heavy. Use camera-based VIO/SLAM instead.

### SLAM Software Stack

```
Sensor Input ──→ SLAM Algorithm ──→ Map + Pose
                      │
                      ↓
              Path Planning ──→ Motor Commands
              (A*, RRT*, D*)

Common stack:
├── SLAM: ORB-SLAM3, LIO-SAM, FAST-LIO2
├── Planning: HKUST EGO-Planner, Fast-Planner
├── Control: PX4/ArduPilot
└── Middleware: ROS2
```

---

## Obstacle Avoidance

### Sensor Options

| Sensor Type | Range | FOV | Weight | Best For |
|------------|-------|-----|--------|----------|
| **Ultrasonic** | 0.02-4m | ~30° cone | 5g | Close range, slow speed |
| **IR ToF (VL53L1X)** | 0.04-4m | ~25° | 2g | Lightweight, single-point |
| **ToF Array (VL53L5CX)** | 0.04-4m | 63° (8×8 zones) | 3g | Low-resolution depth |
| **Stereo Camera** | 0.3-10m | 60-90° | 30-70g | Rich depth, AI-ready |
| **2D LiDAR** | 0.15-12m | 360° | 170g+ | Full surround detection |

### Avoidance Strategies

```
Level 1: STOP
├── Detect obstacle within threshold
├── Hold position
└── Wait for clear path or human input

Level 2: SIMPLE AVOID
├── Detect obstacle direction
├── Move perpendicular to obstacle
└── Resume original heading when clear

Level 3: PATH PLANNING
├── Build local costmap from sensor data
├── Plan collision-free path (A*, RRT)
├── Follow planned path with safety margins
└── Replan continuously as new obstacles appear

Level 4: FULL AUTONOMY
├── Global map from SLAM
├── Dynamic obstacle tracking
├── Predictive avoidance (moving obstacles)
└── Multi-objective planning (fastest + safest)
```

### ArduPilot Obstacle Avoidance (Simple)

Using rangefinders for basic avoidance:

```
# Front-facing rangefinder
RNGFND2_TYPE = 20 (Benewake TFMini)
RNGFND2_ORIENT = 0 (Forward)
RNGFND2_MAX_CM = 1200

# Enable avoidance
AVOID_ENABLE = 7 (all sources)
AVOID_MARGIN = 2.0 (meters buffer)
PRX_TYPE = 4 (rangefinders as proximity)
```

---

## Sensor Fusion

No single sensor is reliable alone. The magic happens when you combine them.

### EKF (Extended Kalman Filter)

ArduPilot and PX4 use EKF to fuse all sensor data:

```
┌──────────────────────────────────────────────────┐
│                      EKF                          │
│                                                  │
│  IMU (1000Hz) ─────┐                            │
│  GPS (10Hz) ───────┤                            │
│  Barometer (50Hz) ──┤──→ [State Estimate] ──→   │
│  Compass (50Hz) ────┤    Position               │
│  Optical Flow (50Hz)┤    Velocity               │
│  VIO (30Hz) ────────┤    Orientation             │
│  Rangefinder (50Hz)─┘    Acceleration            │
│                                                  │
│  Each sensor has a noise model (R matrix)        │
│  EKF weights sensors by reliability              │
│  Bad data gets rejected automatically            │
└──────────────────────────────────────────────────┘
```

### Source Switching (ArduPilot EK3)

ArduPilot lets you define primary and fallback sensor sources:

| Parameter | Source 1 (Outdoor) | Source 2 (Indoor) |
|-----------|-------------------|-------------------|
| POSXY | GPS | ExternalNav (VIO) |
| VELXY | GPS | Optical Flow |
| POSZ | Baro | Rangefinder |
| YAW | Compass | ExternalNav (VIO) |

You can switch between source sets in-flight via RC channel or MAVLink command — the drone seamlessly transitions from GPS to VIO when flying indoors.

```
# Configure source switching on RC channel 8
RC8_OPTION = 90 (EKF Source Set)

Channel low  = Source Set 1 (GPS outdoor)
Channel mid  = Source Set 2 (VIO indoor)
Channel high = Source Set 3 (Optical Flow fallback)
```

---

## Practical Navigation Builds

### Indoor Hover (Budget: ₹2,000)

```
Components:
├── PMW3901 optical flow sensor (SPI)
├── VL53L1X rangefinder (I2C)
└── Flight controller running ArduPilot

Capability:
├── Stable indoor hover
├── Position hold without GPS
├── Altitude hold with rangefinder
└── Range: < 3m altitude, < 2 m/s speed
```

### Indoor Autonomous (Budget: ₹15,000-₹25,000)

```
Components:
├── Raspberry Pi 4/5 (companion computer)
├── OAK-D Lite (stereo + IMU)
├── Flight controller (ArduPilot)
└── RPLIDAR A1 (optional, for 2D mapping)

Capability:
├── VIO-based 6DOF navigation
├── SLAM mapping
├── Basic obstacle avoidance
├── Waypoint following indoors
└── Computer vision (object detection)
```

### Full Autonomous (Budget: ₹50,000+)

```
Components:
├── Jetson Orin Nano (AI compute)
├── Intel RealSense D435i (depth + IMU)
├── Livox Mid-360 (3D LiDAR)
├── Pixhawk 6X (flight controller)
└── GPS + compass (outdoor fallback)

Capability:
├── Full 3D SLAM (indoor + outdoor)
├── Dynamic obstacle avoidance
├── AI-powered object detection + tracking
├── Seamless GPS ↔ VIO switching
├── Autonomous mission execution
└── Real-time 3D map building
```

---

## AI Vision for Navigation

### On-Device Inference

| Platform | TOPS | Power | Weight | Price |
|----------|------|-------|--------|-------|
| **Hailo-8L** | 13 | 2.5W | 5g (M.2) | ₹8,000 |
| **Grove Vision AI V2** | 1 | 0.5W | 15g | ₹2,500 |
| **Jetson Orin Nano** | 40 | 7-15W | ~60g (module) | ₹20,000 |
| **Raspberry Pi AI Kit** | 13 | 3W | 10g (HAT) | ₹5,000 |
| **Google Coral** | 4 | 2W | 5g (M.2) | ₹5,000 |

### Vision AI Use Cases for Navigation

| Use Case | Model | Hardware |
|----------|-------|----------|
| Landing pad detection | YOLO (custom trained) | Any edge AI |
| Person following | YOLO + tracking | Jetson or Hailo |
| Precision landing | AprilTag / ArUco markers | RPi camera + OpenCV |
| Terrain classification | MobileNet | Edge TPU |
| Power line detection | Custom CNN | Jetson |

### Detection Range Reality Check

Resolution matters for detection range:

| Input Resolution | Reliable Detection | Notes |
|-----------------|-------------------|-------|
| 192×192 (WE2 downscale) | ~4-5m (person) | Grove Vision AI V2 limitation |
| 640×640 (YOLO default) | ~15-20m (person) | Standard for most edge AI |
| 1280×720 | ~30-40m (person) | Needs more compute |
| 1920×1080 | ~50m+ (person) | Jetson-tier required |

**Takeaway:** For long-range detection, you need higher resolution input and more compute power. Budget edge AI devices work great for close-range tasks (landing, indoor nav) but struggle at distance.

---

## Key Algorithms Reference

| Algorithm | Purpose | Source |
|-----------|---------|--------|
| **VINS-Fusion** | VIO (stereo/mono + IMU) | HKUST |
| **ORB-SLAM3** | Visual/Visual-Inertial SLAM | UZar |
| **FAST-LIO2** | LiDAR-Inertial Odometry | HKU Mars Lab |
| **EGO-Planner** | Obstacle-free trajectory planning | HKUST |
| **Fast-Planner** | Kinodynamic path planning | HKUST |
| **PX4-Avoidance** | ROS-based obstacle avoidance | PX4 |

---

## Getting Started Path

```
Beginner (Week 1-2):
├── Add optical flow + rangefinder to existing build
├── Configure ArduPilot EK3 for indoor flight
└── Test stable hover without GPS

Intermediate (Month 1-2):
├── Add Raspberry Pi companion computer
├── Install ROS2 + MAVROS
├── Test VIO with stereo camera
└── Basic waypoint following indoors

Advanced (Month 3+):
├── Implement SLAM (ORB-SLAM3 or LIO-SAM)
├── Add path planning (EGO-Planner)
├── Dynamic obstacle avoidance
└── Multi-sensor fusion with source switching
```

---

[← Previous: Custom PCB Design](14-custom-pcb-design.md) | [Back to Index](../README.md)
