# The Complete Drone Guide

[![Made with Love](https://img.shields.io/badge/Made%20with-❤️-red.svg)](https://github.com/thedronecommunity)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg)](CONTRIBUTING.md)

> **Everything you need to know about building, programming, and flying custom drones** — from physics fundamentals to autonomous missions with AI integration.

Created by [The Drone Community](https://github.com/thedronecommunity) | Pune, India

---

## What Is This?

A comprehensive, open-source guide covering everything about custom drones. Whether you're a complete beginner or looking to add autonomous capabilities, this guide has you covered.

**11 in-depth guides** • **Practical, not just theory** • **Copy-paste commands** • **Community-driven**

---

## 📚 Guide Index

| # | Guide | What You'll Learn |
|---|-------|-------------------|
| 01 | [Introduction to Drones](guides/01-introduction.md) | What drones are, consumer vs custom, why build your own |
| 02 | [Physics of Flight](guides/02-physics-of-flight.md) | Four forces, lift, drag, how quadcopters move |
| 03 | [Drone Components](guides/03-components.md) | Flight controllers, motors, ESCs, batteries, sensors |
| 04 | [Drone Types](guides/04-drone-types.md) | Quadcopters, fixed-wing, VTOL, how to choose |
| 05 | [Software & Firmware](guides/05-software-architecture.md) | ArduPilot, PX4, Betaflight, flight modes, tuning |
| 06 | [Communication Protocols](guides/06-communication-protocols.md) | MAVLink, UART, I2C, SPI, ELRS, CRSF |
| 07 | [Building Your Drone](guides/07-assembly-guide.md) | Frame assembly, wiring, calibration, first flight |
| 08 | [Companion Computers](guides/08-raspberry-pi-integration.md) | Raspberry Pi setup, MAVLink, Python control |
| 09 | [Autonomous Flight](guides/09-autonomous-missions.md) | Waypoints, mission planning, geofencing |
| 10 | [AI & Computer Vision](guides/10-ai-integration.md) | LLMs for drones, object detection, voice control |
| 11 | [Maintenance & Troubleshooting](guides/11-maintenance-troubleshooting.md) | Common problems, maintenance schedules, glossary |

---

## 🎯 Who Is This For?

| You Are... | Start With... |
|------------|---------------|
| **Complete beginner** | Guide 01 → Read sequentially |
| **Know basics, want to build** | Guide 03 → Components, then Guide 07 → Assembly |
| **Have a drone, want autonomous** | Guide 08 → Raspberry Pi, Guide 09 → Missions |
| **Developer exploring drones** | Guide 05 → Software, Guide 06 → Protocols |
| **Looking to add AI** | Guide 10 → AI Integration |
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
    └── 11-maintenance-troubleshooting.md
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

```bash
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
|------|----------------|
| **Props OFF during bench testing** | Motors spin unexpectedly |
| **Never fly over people** | Drones fail, batteries explode |
| **Always configure failsafe** | RC loss should trigger RTL |
| **Check battery before every flight** | Low voltage = uncontrolled descent |
| **Know your local regulations** | Fines, legal issues, accidents |
| **Fly in open areas first** | Learn before flying near obstacles |

---

## 🌟 Features of This Guide

- **Beginner-friendly** — No prior drone knowledge assumed
- **Practical focus** — Real commands, real configurations
- **Copy-paste ready** — Commands you can use directly
- **Comprehensive** — From physics to AI in one place
- **Open source** — Free forever, community-improved
- **India-focused examples** — But applicable worldwide

---

## 🔗 Related Resources

### Our Other Repos
- [Drone Troubleshooting Guide](https://github.com/thedronecommunity/drone-troubleshooting-guide) — 50 common problems with step-by-step solutions

### Official Documentation
- [ArduPilot Docs](https://ardupilot.org/copter/)
- [PX4 User Guide](https://docs.px4.io/)
- [Betaflight Wiki](https://betaflight.com/docs/wiki)
- [QGroundControl](https://docs.qgroundcontrol.com/)

### Community
- [ArduPilot Forum](https://discuss.ardupilot.org/)
- [Oscar Liang Blog](https://oscarliang.com/)
- [Joshua Bardwell YouTube](https://youtube.com/@JoshuaBardwell)

---

## 🤝 Contributing

This guide improves with community input. See [CONTRIBUTING.md](CONTRIBUTING.md).

**Ways to help:**
- Fix errors and typos
- Add diagrams and images  
- Improve explanations
- Translate to other languages
- Share your experiences

---

## 📞 Contact

**The Drone Community**
- GitHub: [@thedronecommunity](https://github.com/thedronecommunity)
- Phone: +91 7972632187
- Location: Pune, India

---

## 📄 License

MIT License — Use freely, modify, share, even commercially. Just give credit.

See [LICENSE](LICENSE) for details.

---

<p align="center">
  <i>The best way to learn is to build.</i>
  <br><br>
  <b>Happy Flying! 🚁</b>
</p>
