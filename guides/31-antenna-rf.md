# Guide 31: Antenna Design & RF Systems

[← Previous: Power Systems](30-power-systems.md) | [Back to Index](../README.md) | [Next: Simulation & SITL →](32-simulation-sitl.md)

---

## RF Fundamentals for Drones

Every wireless system on your drone (RC link, video, telemetry, GPS) uses radio frequency signals. Understanding RF basics helps you avoid interference, maximize range, and troubleshoot link issues.

    ┌──────────────────────────────────────────────────┐
    │           DRONE RF SYSTEMS                      │
    │                                                  │
    │  RC Control:     900MHz (ELRS/Crossfire)        │
    │                  2.4GHz (ELRS/FrSky/Spektrum)   │
    │                                                  │
    │  Video (FPV):    5.8GHz (analog/digital)        │
    │                  2.4GHz (DJI O4)                │
    │                                                  │
    │  Telemetry:      433MHz (SiK radio)             │
    │                  915MHz (SiK radio)              │
    │                  2.4GHz (Wi-Fi)                  │
    │                                                  │
    │  GPS:            1.575GHz (L1)                   │
    │                  1.227GHz (L2)                   │
    └──────────────────────────────────────────────────┘

---

## Antenna Types

| Antenna | Gain | Pattern | Polarization | Best For |
|---------|------|---------|-------------|----------|
| **Dipole (stock)** | 2 dBi | Omnidirectional | Linear | General, close range |
| **Whip/monopole** | 2-3 dBi | Omnidirectional | Linear | RC receivers |
| **Patch** | 8-12 dBi | Directional (60-90°) | Linear/Circular | Ground station, long range |
| **Helical** | 10-14 dBi | Directional (40°) | Circular | Long-range video/control |
| **Cloverleaf/Pagoda** | 2-3 dBi | Omnidirectional | Circular (RHCP/LHCP) | FPV video |
| **Yagi** | 12-16 dBi | Narrow beam (30°) | Linear | Telemetry, very long range |
| **Crosshair** | 10-12 dBi | Directional | Circular | Ground FPV |

### Polarization Matching

    Transmitter and receiver antennas should match polarization:

    Linear TX + Linear RX = Good ✓ (but orientation matters)
    RHCP TX + RHCP RX = Best ✓ (no orientation dependency)
    LHCP TX + RHCP RX = Very bad ✗ (-20dB loss)

    For FPV:
    ├── Drone: RHCP omnidirectional (pagoda, lollipop)
    ├── Goggles: RHCP patch (directional) + RHCP omni (backup)
    └── Never mix RHCP and LHCP

---

## Link Budget Calculation

    Link budget tells you if your signal will reach:

    Received Power = Tx Power + Tx Antenna Gain - Path Loss + Rx Antenna Gain

    Free Space Path Loss (dB) = 20×log10(distance) + 20×log10(frequency) - 147.55

    Example: ELRS 900MHz at 10km
    Tx Power: 30 dBm (1W)
    Tx Antenna: 2 dBi (dipole)
    Rx Antenna: 2 dBi (dipole)
    Path Loss at 10km, 900MHz: 20×log10(10000) + 20×log10(900e6) - 147.55 = 111.5 dB
    Received Power: 30 + 2 - 111.5 + 2 = -77.5 dBm
    ELRS sensitivity: -130 dBm

    Margin: -77.5 - (-130) = 52.5 dB → Very comfortable ✓

---

## Antenna Placement Rules

    On the drone:
    ├── GPS antenna: TOP of drone, clear sky view, away from electronics
    ├── RC antenna: Extend away from carbon (blocks signal), 90° to each other if diversity
    ├── VTx antenna: Vertical, unobstructed, away from camera
    ├── Telemetry: Bottom or side, oriented toward ground station
    └── NEVER fold or coil antennas — they lose effectiveness

    Common mistakes:
    ├── Antenna touching carbon fiber frame (blocks signal)
    ├── Antenna inside carbon tube (shielded)
    ├── Zip-tied antenna next to motor wires (interference)
    ├── GPS antenna near VTx (noise from VTx overwhelms GPS)
    └── Antenna coiled up inside frame (terrible SWR)

---

## Interference Troubleshooting

| Problem | Symptom | Fix |
|---------|---------|-----|
| Video noise | Static, lines in FPV feed | Move VTx antenna away from power wires |
| RC failsafe | Random failsafes close range | Antenna placement, check SWR |
| GPS low sats | < 8 satellites, slow fix | Move GPS away from VTx, use GPS mast |
| Compass errors | Erratic heading, toilet bowl | Mount compass on mast, away from current |
| Range reduced | Poor RSSI at normal distance | Check antenna damage, connector, SWR |

---

## DIY Antenna Building

### 900MHz Dipole (for ELRS/SiK)

    Materials:
    ├── RG316 coaxial cable
    ├── IPEX/U.FL connector
    └── Heat shrink tubing

    Dimensions (900 MHz quarter-wave):
    ├── Element length: λ/4 = 300/900/4 = 83mm
    ├── Strip outer conductor for 83mm
    ├── Inner conductor extends 83mm beyond
    └── Total antenna length: ~83mm active element

    Cost: ₹100-₹200

---

[← Previous: Power Systems](30-power-systems.md) | [Back to Index](../README.md) | [Next: Simulation & SITL →](32-simulation-sitl.md)
