# Guide 27: Search & Rescue Drones

[← Previous: Precision Agriculture](26-precision-agriculture.md) | [Back to Index](../README.md) | [Next: Drone Racing →](28-drone-racing.md)

---

## SAR Drone Operations

Drones dramatically accelerate search and rescue operations by covering large areas quickly, accessing dangerous terrain, and providing thermal and visual feeds to responders.

    ┌──────────────────────────────────────────────────┐
    │           SAR DRONE CAPABILITIES                 │
    │                                                  │
    │  Visual Search                                   │
    │  ├── High-res zoom cameras (30x optical)        │
    │  ├── Wide-area surveillance                     │
    │  └── Live video feed to command center          │
    │                                                  │
    │  Thermal Detection                               │
    │  ├── Body heat detection in darkness/fog         │
    │  ├── Warm vehicle/shelter identification         │
    │  └── Fire hotspot detection                     │
    │                                                  │
    │  Delivery                                        │
    │  ├── First aid kits                              │
    │  ├── Communication devices                       │
    │  ├── Water/food supplies                         │
    │  ├── Life jackets / floatation devices           │
    │  └── Rope / emergency beacons                   │
    │                                                  │
    │  Communication Relay                             │
    │  ├── Cell signal repeater                        │
    │  └── Radio relay at altitude                    │
    └──────────────────────────────────────────────────┘

---

## SAR Drone Hardware

### Recommended Configuration

    Platform: Heavy-lift quadcopter or hexacopter
    ├── Frame: 550-700mm, folding arms for transport
    ├── Motors: High-efficiency (2814-3115 range)
    ├── Battery: 6S 10000-16000mAh (30+ min flight)
    ├── Redundancy: Hexacopter can fly on 5 motors
    └── Weather: IP54 rating minimum

    Sensors:
    ├── RGB camera: 30x optical zoom (₹15,000-₹40,000)
    ├── Thermal camera: FLIR Lepton/Boson (₹20,000-₹1,00,000)
    ├── Spotlight: 5000+ lumens (₹2,000-₹5,000)
    ├── Speaker: 120dB for voice commands (₹3,000-₹8,000)
    └── GPS RTK: For precise location marking

    Communication:
    ├── Dual telemetry (redundant link)
    ├── 4G backup link
    ├── Video downlink (1080p minimum)
    └── ADS-B receiver (for manned aircraft awareness)

### Thermal Camera Options

| Camera | Resolution | Range | Weight | Price |
|--------|-----------|-------|--------|-------|
| **FLIR Lepton 3.5** | 160×120 | 300m (person) | 10g | ₹15,000 |
| **FLIR Boson 320** | 320×256 | 500m (person) | 30g | ₹50,000 |
| **FLIR Boson 640** | 640×512 | 1km (person) | 30g | ₹1,00,000 |
| **DJI Zenmuse H20T** | 640×512 | 1km+ | 828g | ₹5,00,000+ |
| **Workswell WIRIS** | 640×512 | 1km+ | 400g | ₹3,00,000 |

---

## Search Patterns

    Grid Search (most common):
    ┌──→──→──→──→──→──→──┐
    │                    │
    └──←──←──←──←──←──←──┘
    ┌──→──→──→──→──→──→──┐
    │                    │
    └──←──←──←──←──←──←──┘

    Expanding Square:
    ┌──→──┐
    │     │
    │  S──┘
    │     ┌──→──→──┐
    └──←──┘        │
    ┌──→──→──→──→──┘
    │

    Sector Search (from known last position):
         ╱ ╲
        ╱   ╲
       ╱  K  ╲     K = Known last position
      ╱   │   ╲    Fly outward in sectors
     ╱    │    ╲
    ╱─────┼─────╲

---

## AI-Assisted Detection

    Object detection pipeline:
    1. Thermal + RGB dual feed
    2. YOLO / SSD model trained on:
       ├── Human body shapes (thermal signature)
       ├── Clothing colors (RGB)
       ├── Vehicle shapes
       └── Campfire / shelter shapes
    3. Alert operator with bounding box + GPS coords
    4. Auto-zoom and track detected target

    Training data:
    ├── Public SAR datasets (HERIDAL, SARD)
    ├── Synthetic data (rendered humans in terrain)
    └── Augmented real-world thermal images

---

## Emergency Procedures

    Lost link during SAR:
    ├── Drone continues pattern for 30 seconds
    ├── Climbs to safe altitude (120m)
    ├── Attempts to reestablish link for 60 seconds
    └── Returns to launch if link not restored

    Low battery:
    ├── Auto-RTL at 30% battery
    ├── Emergency land at 15%
    └── Always have second battery ready for hot-swap

    Manned aircraft conflict:
    ├── ADS-B IN receiver to detect aircraft
    ├── Immediate descend to < 50m
    ├── Yield right of way ALWAYS
    └── Notify ATC if in controlled airspace

---

## NDRF / SDRF Coordination (India)

    Working with disaster response teams:
    ├── Coordinate with NDRF/SDRF incident commander
    ├── Establish dedicated radio channel for drone ops
    ├── Define no-fly zones (helicopter corridors)
    ├── Share live video feed via portable ground station
    ├── Provide real-time mapping updates
    └── Log all flight data for official records

    Legal considerations:
    ├── Emergency exemptions may apply for SAR
    ├── Coordinate with local police and DGCA
    ├── Document all flights for post-incident review
    └── Insurance coverage must be active

---

[← Previous: Precision Agriculture](26-precision-agriculture.md) | [Back to Index](../README.md) | [Next: Drone Racing →](28-drone-racing.md)
