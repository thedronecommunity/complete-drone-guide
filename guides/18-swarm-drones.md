# Guide 18: Swarm Drones

[← Previous: PID Tuning](17-pid-tuning.md) | [Back to Index](../README.md) | [Next: Drone Delivery Systems →](19-drone-delivery.md)

---

## What Is a Drone Swarm?

A drone swarm is a group of drones that coordinate autonomously to accomplish tasks. Unlike individually controlled drones, swarm members communicate, share information, and make collective decisions.

    ┌──────────────────────────────────────────────────┐
    │            SWARM ARCHITECTURE TYPES              │
    │                                                  │
    │  Centralized:         Decentralized:             │
    │                                                  │
    │       GCS                 D ── D                 │
    │      / | \               / \  / \                │
    │     D  D  D             D   D    D               │
    │                          \ /  \ /                │
    │  One controller           D ── D                 │
    │  commands all           Each drone decides        │
    │  Single point of        locally, shares info      │
    │  failure                No single failure point   │
    └──────────────────────────────────────────────────┘

---

## Swarm Communication

### Communication Protocols

| Protocol | Range | Bandwidth | Latency | Nodes | Use Case |
|----------|-------|-----------|---------|-------|----------|
| **Wi-Fi Mesh** | 50-100m | High | 10-50ms | 10-50 | Indoor/close range |
| **ESP-NOW** | 200m | Low | <5ms | 20 | Lightweight swarms |
| **XBee (Zigbee)** | 1km+ | Low | 10ms | 100+ | Outdoor mesh |
| **LoRa** | 5-15km | Very low | 100ms+ | 100+ | Long range, low data |
| **4G/5G** | Unlimited | High | 20-50ms | Unlimited | Cloud-connected |
| **UWB** | 100m | Medium | <1ms | 50+ | Precise positioning |

### ESP-NOW Swarm (Budget Option)

ESP-NOW is perfect for small swarms using ESP32:

    # ESP-NOW broadcast example (MicroPython)
    import espnow
    import network

    sta = network.WLAN(network.STA_IF)
    sta.active(True)

    e = espnow.ESPNow()
    e.active(True)

    # Broadcast to all peers
    BROADCAST = b'\xff' * 6
    e.add_peer(BROADCAST)

    # Send position update
    import json
    msg = json.dumps({
        "id": 1,
        "lat": 18.5204,
        "lon": 73.8567,
        "alt": 10.0,
        "heading": 45
    })
    e.send(BROADCAST, msg)

---

## Swarm Behaviors

### Basic Formation Flying

    Line:           V-Formation:      Circle:
    D  D  D  D     D                    D
                     D D              D     D
                      D D           D       D
                       D              D   D
                                        D

### Behavior Algorithms

| Behavior | Description | Algorithm |
|----------|-------------|-----------|
| **Flocking** | Move together like birds | Reynolds' Boids (separation, alignment, cohesion) |
| **Formation** | Maintain fixed relative positions | Leader-follower, virtual structure |
| **Coverage** | Spread out to cover area | Voronoi partitioning |
| **Search** | Cooperatively search an area | Lawnmower, spiral, probabilistic |
| **Consensus** | Agree on shared value | Average consensus protocol |

### Reynolds' Boids Rules

Three simple rules create realistic flocking:

    ┌──────────────────────────────────────────┐
    │  Rule 1: SEPARATION                      │
    │  Avoid crowding nearby drones             │
    │  ← D → ← D → ← D →                     │
    │                                          │
    │  Rule 2: ALIGNMENT                       │
    │  Steer toward average heading of group    │
    │  D→ D→ D→ (all go same direction)        │
    │                                          │
    │  Rule 3: COHESION                        │
    │  Move toward average position of group    │
    │    D                                     │
    │  D   D  → all pull toward center         │
    │    D                                     │
    └──────────────────────────────────────────┘

---

## SITL Multi-Vehicle Simulation

ArduPilot SITL lets you test swarms without real hardware:

    # Terminal 1: Launch first drone
    sim_vehicle.py -v ArduCopter -I 0 --console --map \
        --custom-location=18.5204,73.8567,560,0

    # Terminal 2: Launch second drone
    sim_vehicle.py -v ArduCopter -I 1 --console \
        --custom-location=18.5205,73.8568,560,0

    # Terminal 3: Launch third drone
    sim_vehicle.py -v ArduCopter -I 2 --console \
        --custom-location=18.5206,73.8569,560,0

    # Each drone gets its own SYSID and port:
    # Drone 0: SYSID=1, UDP 14550
    # Drone 1: SYSID=2, UDP 14560
    # Drone 2: SYSID=3, UDP 14570

### Python Multi-Drone Control

    from pymavlink import mavutil
    import time

    # Connect to multiple drones
    drones = []
    for i in range(3):
        port = 14550 + (i * 10)
        conn = mavutil.mavlink_connection(f'udp:127.0.0.1:{port}')
        conn.wait_heartbeat()
        drones.append(conn)
        print(f"Drone {i} connected (SYSID: {conn.target_system})")

    def arm_and_takeoff(drone, alt):
        drone.mav.command_long_send(
            drone.target_system, drone.target_component,
            mavutil.mavlink.MAV_CMD_COMPONENT_ARM_DISARM,
            0, 1, 0, 0, 0, 0, 0, 0)
        time.sleep(2)
        drone.mav.command_long_send(
            drone.target_system, drone.target_component,
            mavutil.mavlink.MAV_CMD_NAV_TAKEOFF,
            0, 0, 0, 0, 0, 0, 0, alt)

    # Takeoff all drones sequentially
    for i, drone in enumerate(drones):
        arm_and_takeoff(drone, 10 + i * 2)  # Staggered altitude
        time.sleep(3)

---

## Swarm Frameworks

| Framework | Language | Features | Best For |
|-----------|----------|----------|----------|
| **Crazyswarm2** | Python/ROS2 | Crazyflie-native, motion capture | Indoor research |
| **MAVSWARM** | Python | MAVLink-based, ArduPilot/PX4 | Outdoor multi-vehicle |
| **Swarmlib** | Python | Algorithm visualization | Learning/prototyping |
| **ROSSwarm** | C++/Python | ROS2 integrated | Full-scale systems |
| **Buzz** | Custom DSL | Swarm-specific language | Research |

---

## Hardware for Swarms

### Budget Swarm Platform (~₹8,000 per drone)

    Per drone:
    ├── ESP32-S3 flight controller (custom or Qyverra-style)
    ├── 4× 0802-1S brushed motors
    ├── 65mm frame (3D printed)
    ├── 1S 300mAh LiPo
    ├── MPU6050/BMI270 IMU
    ├── ESP-NOW for inter-drone comms
    └── PMW3901 optical flow (for indoor)

    Total for 5-drone swarm: ~₹40,000

### Research Swarm Platform

    Per drone:
    ├── Crazyflie 2.1 (~₹15,000)
    ├── Flow deck V2 (optical flow)
    ├── Loco positioning deck (UWB)
    └── AI deck (GAP8 processor)

    + Loco Positioning System (~₹25,000)
    Total for 5-drone swarm: ~₹1,00,000

---

## Safety in Swarm Operations

    Critical rules:
    1. Always test in simulation first (SITL)
    2. Start with 2 drones before scaling
    3. Use altitude separation (at least 1m between layers)
    4. Implement collision avoidance as a hard constraint
    5. Have individual kill switches for each drone
    6. Fly in netted/caged areas until proven reliable
    7. Monitor battery levels on ALL drones simultaneously

    Emergency protocol:
    ├── If one drone fails → Others avoid + land nearby
    ├── If comms fail → Each drone RTL independently
    └── If collision → Immediate disarm of affected drones

---

## Applications

| Application | Swarm Size | Key Tech |
|-------------|-----------|----------|
| Light shows | 100-1000+ | GPS RTK, choreography software |
| Agricultural survey | 3-10 | Coverage planning, multispectral |
| Search and rescue | 5-20 | Camera + thermal, area coverage |
| Warehouse inventory | 5-10 | Indoor nav, barcode scanning |
| Security patrol | 3-5 | AI detection, perimeter coverage |
| 3D mapping | 3-5 | Photogrammetry, LiDAR |

---

[← Previous: PID Tuning](17-pid-tuning.md) | [Back to Index](../README.md) | [Next: Drone Delivery Systems →](19-drone-delivery.md)
