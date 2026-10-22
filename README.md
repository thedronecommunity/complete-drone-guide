<div align="center">

# 🚁 The Complete Drone Building Guide

### From Zero to Autonomous Flight

**56 in-depth guides** • **Practical, not just theory** • **Copy-paste commands** • **Community-driven**

Built and maintained by [The Drone Community](https://github.com/thedronecommunity)

</div>

---

## 👋 Welcome

Whether you are soldering your first frame or wiring a companion computer for autonomous flight, this repository is a complete, hands-on path to building drones. Every guide is written to teach the concepts, not just list steps, so you understand why things work, not only how.

Start at Guide 01 and work through, or jump to whatever you need.

---

## 📚 Guide Index

### Foundations (01-11)

| #  | Guide                                                                     |
| --- | ------------------------------------------------------------------------ |
| 01 | [Introduction](guides/01-introduction.md)                                |
| 02 | [Physics of Flight](guides/02-physics-of-flight.md)                      |
| 03 | [Components](guides/03-components.md)                                     |
| 04 | [Drone Types](guides/04-drone-types.md)                                  |
| 05 | [Software Architecture](guides/05-software-architecture.md)              |
| 06 | [Communication Protocols](guides/06-communication-protocols.md)          |
| 07 | [Assembly Guide](guides/07-assembly-guide.md)                            |
| 08 | [Raspberry Pi Integration](guides/08-raspberry-pi-integration.md)        |
| 09 | [Autonomous Missions](guides/09-autonomous-missions.md)                  |
| 10 | [AI Integration](guides/10-ai-integration.md)                            |
| 11 | [Maintenance & Troubleshooting](guides/11-maintenance-troubleshooting.md) |

### Systems & Hardware (12-31)

| #  | Guide                                                                     |
| --- | ------------------------------------------------------------------------ |
| 12 | [Regulations & Legal](guides/12-regulations-legal.md)                    |
| 13 | [FPV Systems](guides/13-fpv-systems.md)                                  |
| 14 | [Custom PCB Design](guides/14-custom-pcb-design.md)                      |
| 15 | [Advanced Navigation](guides/15-advanced-navigation.md)                  |
| 17 | [PID Tuning](guides/17-pid-tuning.md)                                    |
| 18 | [Swarm Drones](guides/18-swarm-drones.md)                                |
| 19 | [Drone Delivery](guides/19-drone-delivery.md)                            |
| 20 | [Weather & Environment](guides/20-weather-environment.md)                |
| 21 | [3D Printing](guides/21-3d-printing.md)                                  |
| 22 | [Ground Control Stations](guides/22-ground-control-stations.md)          |
| 23 | [Telemetry Systems](guides/23-telemetry-systems.md)                      |
| 24 | [Payload Integration](guides/24-payload-integration.md)                  |
| 25 | [Mapping & Photogrammetry](guides/25-mapping-photogrammetry.md)          |
| 26 | [Precision Agriculture](guides/26-precision-agriculture.md)              |
| 27 | [Search & Rescue](guides/27-search-rescue.md)                            |
| 28 | [Drone Racing](guides/28-drone-racing.md)                                |
| 29 | [Long Range & BVLOS](guides/29-long-range-bvlos.md)                      |
| 30 | [Power Systems](guides/30-power-systems.md)                              |
| 31 | [Antenna & RF](guides/31-antenna-rf.md)                                  |

### Advanced & Specialized (32-45)

| #  | Guide                                                                     |
| --- | ------------------------------------------------------------------------ |
| 32 | [Simulation & SITL](guides/32-simulation-sitl.md)                        |
| 33 | [ROS2 Drones](guides/33-ros2-drones.md)                                  |
| 34 | [Edge AI](guides/34-edge-ai.md)                                          |
| 35 | [Motor & Propeller Selection](guides/35-motor-propeller-selection.md)    |
| 36 | [Frame Design](guides/36-frame-design.md)                                |
| 37 | [Drone Security](guides/37-drone-security.md)                            |
| 38 | [Commercial Operations](guides/38-commercial-operations.md)              |
| 39 | [Fixed-Wing & VTOL](guides/39-fixed-wing-vtol.md)                        |
| 40 | [Underwater & Hybrid](guides/40-underwater-hybrid.md)                    |
| 41 | [Data Logging](guides/41-data-logging.md)                                |
| 42 | [Photography & Videography](guides/42-photography-videography.md)        |
| 43 | [Night Operations](guides/43-night-operations.md)                        |
| 44 | [Thermal Imaging](guides/44-thermal-imaging.md)                          |
| 45 | [Drone Business](guides/45-drone-business.md)                            |

### AI Autonomous Drone Series (46-56)

| #  | Guide                                                                                     | What You'll Learn                                                   |
| --- | ----------------------------------------------------------------------------------------- | ------------------------------------------------------------------- |
| 46 | [AI Autonomous Drone: Overview & Architecture](guides/46-ai-autonomous-drone-overview.md) | The two-brain model, the full stack, where the intelligence lives   |
| 47 | [Linking the Two Brains](guides/47-companion-mavlink-link.md)                              | MAVLink over ethernet, the heartbeat, the one-reader principle      |
| 48 | [Giving the Drone Eyes](guides/48-edge-vision-hailo.md)                                    | Camera vs perception, YOLO on the Hailo, what a detection means     |
| 49 | [The Language Brain](guides/49-language-brain-llm.md)                                      | Natural-language control, constrained autonomy, local vs cloud LLM  |
| 50 | [The Safety Layer](guides/50-safety-layer.md)                                              | Propose and dispose, why the AI cannot bypass safety, warn vs block |
| 51 | [Feeding the AI Good Context](guides/51-context-telemetry-perception.md)                   | Telemetry and perception as text, why context drives decisions      |
| 52 | [Making It Visible](guides/52-live-dashboard.md)                                           | The live dashboard, visibility for trust and debugging              |
| 53 | [Flying Without GPS](guides/53-optical-flow-gps-denied.md)                                 | Optical flow, position without GPS, the LOITER hold gate            |
| 54 | [The Closed Loop](guides/54-autonomous-missions.md)                                        | Perceive, reason, act; open-ended autonomy inside safe limits       |
| 55 | [From Simulation to Real Flight](guides/55-simulation-to-real.md)                          | The safety ladder from SITL to real flight, thinking about risk     |
| 56 | [The Complete Picture (Summary + Diagrams)](guides/56-ai-autonomous-drone-summary.md)     | The whole series tied together, with architecture and flow diagrams |

---

## 🎯 Who Is This For?

| If you are...                              | Start here                                        |
| ------------------------------------------ | ------------------------------------------------- |
| **Completely new to drones**               | Guide 01                                          |
| **Ready to build**                         | Guide 07 (Assembly)                               |
| **Adding a companion computer**            | Guide 08                                          |
| **Adding AI and computer vision**          | Guide 10 and Guide 34                             |
| **Building a full AI autonomous drone**    | AI Autonomous Drone Series (46-56)                |

---

## 🤝 Contributing

Found a mistake? Have a better way to explain something? Contributions are welcome. Open an issue or a pull request.

## ⭐ Support

If these guides helped you, star the repository. It helps more builders find it.

---

<div align="center">

**Happy Flying**

The Drone Community

</div>