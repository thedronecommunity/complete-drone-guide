# The Complete Drone Guide

[![Made with Love](https://img.shields.io/badge/Made%20with-%E2%9D%A4%EF%B8%8F-red.svg)](https://github.com/thedronecommunity)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg)](CONTRIBUTING.md)

> **Everything you need to know about building, programming, and flying custom drones** - from physics fundamentals to autonomous missions with AI integration.

Created by [The Drone Community](https://github.com/thedronecommunity) | Pune, India

---

## What Is This?

A complete, open-source guide covering everything about custom drones. Whether you're a complete beginner or looking to add autonomous capabilities, this guide has you covered.

**45 in-depth guides** • **Practical, not just theory** • **Copy-paste commands** • **Community-driven**

---

## 📚 Guide Index

### Fundamentals (01-06)

| # | Guide | What You'll Learn |
|---|-------|-------------------|
| 01 | [Introduction to Drones](guides/01-introduction.md) | What drones are, consumer vs custom, why build your own |
| 02 | [Physics of Flight](guides/02-physics-of-flight.md) | Four forces, lift, drag, how quadcopters move |
| 03 | [Drone Components](guides/03-components.md) | Flight controllers, motors, ESCs, batteries, sensors |
| 04 | [Drone Types](guides/04-drone-types.md) | Quadcopters, fixed-wing, VTOL, how to choose |
| 05 | [Software & Firmware](guides/05-software-architecture.md) | ArduPilot, PX4, Betaflight, flight modes, tuning |
| 06 | [Communication Protocols](guides/06-communication-protocols.md) | MAVLink, UART, I2C, SPI, ELRS, CRSF |

### Building & Hardware (07, 14, 16, 21, 35-36)

| # | Guide | What You'll Learn |
|---|-------|-------------------|
| 07 | [Building Your Drone](guides/07-assembly-guide.md) | Frame assembly, wiring, calibration, first flight |
| 14 | [Custom PCB Design](guides/14-custom-pcb-design.md) | Flight controller design, schematics, layout, JLCPCB fab |
| 16 | [Battery Technology Deep Dive](guides/16-battery-technology.md) | LiPo chemistry, C ratings, Li-Ion, charging, safety |
| 21 | [3D Printing for Drones](guides/21-3d-printing.md) | Materials, print settings, designing mounts and frames |
| 35 | [Motor & Propeller Selection](guides/35-motor-propeller-selection.md) | KV ratings, stator sizes, prop pitch, efficiency |
| 36 | [Frame Design & Aerodynamics](guides/36-frame-design.md) | Layouts, materials, carbon fiber, drag reduction |

### Software & Control (08-10, 17, 22-23, 32-34)

| # | Guide | What You'll Learn |
|---|-------|-------------------|
| 08 | [Companion Computers](guides/08-raspberry-pi-integration.md) | Raspberry Pi setup, MAVLink, Python control |
| 09 | [Autonomous Flight](guides/09-autonomous-missions.md) | Waypoints, mission planning, geofencing |
| 10 | [AI & Computer Vision](guides/10-ai-integration.md) | LLMs for drones, object detection, voice control |
| 17 | [PID Tuning](guides/17-pid-tuning.md) | P, I, D explained, Betaflight + ArduPilot tuning, blackbox analysis |
| 22 | [Ground Control Stations](guides/22-ground-control-stations.md) | Mission Planner, QGC, MAVProxy, custom GCS |
| 23 | [Telemetry Systems](guides/23-telemetry-systems.md) | SiK radios, ELRS telemetry, OSD, 4G/MQTT |
| 32 | [Simulation & SITL Testing](guides/32-simulation-sitl.md) | ArduPilot SITL, PX4 SITL, Gazebo, automated testing |
| 33 | [ROS2 for Drones](guides/33-ros2-drones.md) | MAVROS, ROS2 nodes, camera pipelines, launch files |
| 34 | [Edge Computing & Embedded AI](guides/34-edge-ai.md) | Hailo, Jetson, Coral, YOLO on edge, deployment pipeline |

### Navigation & Sensors (15, 31)

| # | Guide | What You'll Learn |
|---|-------|-------------------|
| 15 | [Advanced Navigation](guides/15-advanced-navigation.md) | Optical flow, VIO, SLAM, obstacle avoidance, sensor fusion |
| 31 | [Antenna Design & RF Systems](guides/31-antenna-rf.md) | Antenna types, link budget, placement, interference |

### Flying Disciplines (13, 28-29)

| # | Guide | What You'll Learn |
|---|-------|-------------------|
| 13 | [FPV Systems & Video](guides/13-fpv-systems.md) | Analog vs digital, cameras, VTx, goggles, antennas, OSD |
| 28 | [Drone Racing](guides/28-drone-racing.md) | Racing builds, Betaflight config, simulators, skills progression |
| 29 | [Long Range & BVLOS](guides/29-long-range-bvlos.md) | Li-Ion builds, ELRS 900MHz, video links, BVLOS regulations |

### Applications (18-19, 25-27, 42-44)

| # | Guide | What You'll Learn |
|---|-------|-------------------|
| 18 | [Swarm Drones](guides/18-swarm-drones.md) | Communication, flocking algorithms, ESP-NOW, multi-vehicle SITL |
| 19 | [Drone Delivery Systems](guides/19-drone-delivery.md) | Delivery architecture, precision landing, payload release |
| 24 | [Payload Integration](guides/24-payload-integration.md) | Mounting, gimbals, CG management, camera triggers |
| 25 | [Mapping & Photogrammetry](guides/25-mapping-photogrammetry.md) | GSD, overlap, WebODM, GCPs, orthomosaics |
| 26 | [Precision Agriculture](guides/26-precision-agriculture.md) | NDVI, multispectral, spray drones, subsidies |
| 27 | [Search & Rescue](guides/27-search-rescue.md) | Thermal detection, search patterns, AI-assisted SAR |
| 42 | [Drone Photography & Videography](guides/42-photography-videography.md) | Camera settings, ND filters, Gyroflow, composition |
| 43 | [Night Operations](guides/43-night-operations.md) | Navigation lights, low-light cameras, safety |
| 44 | [Thermal Imaging](guides/44-thermal-imaging.md) | FLIR cameras, integration, building inspection, SAR |

### Operations & Safety (11-12, 20, 30, 37, 41)

| # | Guide | What You'll Learn |
|---|-------|-------------------|
| 11 | [Maintenance & Troubleshooting](guides/11-maintenance-troubleshooting.md) | Common problems, maintenance schedules, glossary |
| 12 | [Regulations & Legal](guides/12-regulations-legal.md) | DGCA rules, nano exemptions, airspace zones, international laws |
| 20 | [Weather & Environmental Factors](guides/20-weather-environment.md) | Wind, temperature, rain, EMI, seasonal flying in India |
| 30 | [Power Systems & Energy Harvesting](guides/30-power-systems.md) | PDBs, BECs, power budgets, solar charging |
| 37 | [Drone Security & Counter-Drones](guides/37-drone-security.md) | GPS spoofing, MAVLink signing, counter-drone systems |
| 41 | [Data Logging & Flight Analysis](guides/41-data-logging.md) | Blackbox, log analysis, crash investigation, vibration |

### Advanced Platforms (39-40)

| # | Guide | What You'll Learn |
|---|-------|-------------------|
| 39 | [Fixed-Wing & VTOL Design](guides/39-fixed-wing-vtol.md) | Wing types, QuadPlane, tiltrotor, ArduPlane setup |
| 40 | [Underwater & Hybrid Drones](guides/40-underwater-hybrid.md) | ROV components, ArduSub, waterproofing, hybrid designs |

### Business & Career (38, 45)

| # | Guide | What You'll Learn |
|---|-------|-------------------|
| 38 | [Commercial Drone Operations](guides/38-commercial-operations.md) | RPL, UAOP, insurance, pricing, client acquisition |
| 45 | [Building a Drone Business](guides/45-drone-business.md) | Startup costs, revenue models, scaling, govt schemes |

---

## 🎯 Who Is This For?

| You Are... | Start With... |
|------------|---------------|
| **Complete beginner** | Guide 01 → Read sequentially |
| **Know basics, want to build** | Guide 03 → Components, then Guide 07 → Assembly |
| **Have a drone, want autonomous** | Guide 08 → Raspberry Pi, Guide 09 → Missions |
| **Developer exploring drones** | Guide 05 → Software, Guide 06 → Protocols |
| **Looking to add AI** | Guide 10 → AI Integration, Guide 34 → Edge AI |
| **Want to fly FPV** | Guide 13 → FPV Systems, Guide 28 → Racing |
| **Designing your own hardware** | Guide 14 → Custom PCB, Guide 35 → Motors |
| **Indoor/GPS-denied flight** | Guide 15 → Advanced Navigation |
| **Need to know the rules** | Guide 12 → Regulations & Legal |
| **Building for delivery** | Guide 19 → Delivery Systems |
| **Starting a drone business** | Guide 38 → Commercial Ops, Guide 45 → Business |
| **Want to fly long range** | Guide 29 → Long Range & BVLOS |
| **Learning ROS2/simulation** | Guide 32 → SITL, Guide 33 → ROS2 |
| **Troubleshooting issues** | Guide 11 → Maintenance, or our [Troubleshooting Repo](https://github.com/thedronecommunity/drone-troubleshooting-guide) |

---

## 🛠️ What You Can Build

From simple to advanced:

```
Level 1: Manual Quadcopter
├── Stabilized flight
├── Altitude hold
└── Basic FPV

Level 2: GPS-Enabled Drone  
├── Position hold (Loiter)
├── Return to launch
├── Waypoint missions
└── Geofencing

Level 3: Autonomous System
├── Companion computer (Raspberry Pi)
├── Computer vision
├── AI-powered decisions
└── Custom applications

Level 4: Custom Hardware + Full Autonomy
├── Your own flight controller PCB
├── Indoor navigation without GPS
├── SLAM and obstacle avoidance
└── AI vision pipeline on edge hardware

Level 5: Commercial Operations
├── Mapping and survey missions
├── Agricultural spraying
├── Delivery systems
├── Swarm coordination
└── Fixed-wing / VTOL platforms
```

---

## 📁 Repository Structure

```
complete-drone-guide/
│
├── README.md                 # You are here
├── LICENSE                   # MIT License
├── CONTRIBUTING.md           # How to contribute
│
└── guides/
    ├── 01-introduction.md
    ├── 02-physics-of-flight.md
    ├── 03-components.md
    ├── 04-drone-types.md
    ├── 05-software-architecture.md
    ├── 06-communication-protocols.md
    ├── 07-assembly-guide.md
    ├── 08-raspberry-pi-integration.md
    ├── 09-autonomous-missions.md
    ├── 10-ai-integration.md
    ├── 11-maintenance-troubleshooting.md
    ├── 12-regulations-legal.md
    ├── 13-fpv-systems.md
    ├── 14-custom-pcb-design.md
    ├── 15-advanced-navigation.md
    ├── 16-battery-technology.md
    ├── 17-pid-tuning.md
    ├── 18-swarm-drones.md
    ├── 19-drone-delivery.md
    ├── 20-weather-environment.md
    ├── 21-3d-printing.md
    ├── 22-ground-control-stations.md
    ├── 23-telemetry-systems.md
    ├── 24-payload-integration.md
    ├── 25-mapping-photogrammetry.md
    ├── 26-precision-agriculture.md
    ├── 27-search-rescue.md
    ├── 28-drone-racing.md
    ├── 29-long-range-bvlos.md
    ├── 30-power-systems.md
    ├── 31-antenna-rf.md
    ├── 32-simulation-sitl.md
    ├── 33-ros2-drones.md
    ├── 34-edge-ai.md
    ├── 35-motor-propeller-selection.md
    ├── 36-frame-design.md
    ├── 37-drone-security.md
    ├── 38-commercial-operations.md
    ├── 39-fixed-wing-vtol.md
    ├── 40-underwater-hybrid.md
    ├── 41-data-logging.md
    ├── 42-photography-videography.md
    ├── 43-night-operations.md
    ├── 44-thermal-imaging.md
    └── 45-drone-business.md
```

---

## ⚡ Quick Reference

### LiPo Battery Voltages

| Cells | Nominal | Full | Empty | Storage |
|-------|---------|------|-------|---------|
| 1S | 3.7V | 4.2V | 3.0V | 3.8V |
| 4S | 14.8V | 16.8V | 12.0V | 15.2V |
| 6S | 22.2V | 25.2V | 18.0V | 22.8V |

### Motor Layout (Quad X - Betaflight)

```
       FRONT
   4 (CCW)   2 (CW)
       \     /
        \   /
         \ /
          X
         / \
        /   \
       /     \
   3 (CW)    1 (CCW)
       BACK
```

### Common Commands

```
# MAVProxy connection
mavproxy.py --master=/dev/ttyACM0 --console

# Flight modes
mode STABILIZE    # Manual with auto-level
mode LOITER       # GPS position hold
mode RTL          # Return to launch
mode GUIDED       # Computer control

# Arming
arm throttle      # Arm motors
disarm            # Disarm motors
```

---

## 🔒 Safety Essentials

| Rule | Why It Matters |
|------|---------------|
| **Props OFF during bench testing** | Motors spin unexpectedly |
| **Never fly over people** | Drones fail, batteries explode |
| **Always configure failsafe** | RC loss should trigger RTL |
| **Check battery before every flight** | Low voltage = uncontrolled descent |
| **Know your local regulations** | Fines, legal issues, accidents |
| **Fly in open areas first** | Learn before flying near obstacles |

---

## 🌟 Features of This Guide

* **Beginner-friendly** - No prior drone knowledge assumed
* **Practical focus** - Real commands, real configurations
* **Copy-paste ready** - Commands you can use directly
* **Hardware design included** - From schematic to fabrication
* **Open source** - Free forever, community-improved
* **India-focused examples** - But applicable worldwide

---

## 🔗 Related Resources

### Our Other Repos
* [Drone Troubleshooting Guide](https://github.com/thedronecommunity/drone-troubleshooting-guide) - 70 common problems with step-by-step solutions

### Official Documentation
* [ArduPilot Docs](https://ardupilot.org/copter/)
* [PX4 User Guide](https://docs.px4.io/)
* [Betaflight Wiki](https://betaflight.com/docs/wiki)
* [QGroundControl](https://docs.qgroundcontrol.com/)

### Community
* [ArduPilot Forum](https://discuss.ardupilot.org/)
* [Oscar Liang Blog](https://oscarliang.com/)
* [Joshua Bardwell YouTube](https://youtube.com/@JoshuaBardwell)

---

## 🤝 Contributing

This guide improves with community input. See [CONTRIBUTING.md](CONTRIBUTING.md).

**Ways to help:**
* Fix errors and typos
* Add diagrams and images
* Improve explanations
* Translate to other languages
* Share your experiences

---

## 📞 Contact

**The Drone Community**
* GitHub: [@thedronecommunity](https://github.com/thedronecommunity)
* Instagram: [@thedrone.community](https://instagram.com/thedrone.community)
* Phone: +91 7972632187
* Location: Pune, India

---

## 📄 License

MIT License - Use freely, modify, share, even commercially. Just give credit.

See [LICENSE](LICENSE) for details.

---

*The best way to learn is to build.*

**Happy Flying! 🚁**
