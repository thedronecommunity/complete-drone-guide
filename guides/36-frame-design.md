# Guide 36: Frame Design & Aerodynamics

[← Previous: Motor & Propeller Selection](35-motor-propeller-selection.md) | [Back to Index](../README.md) | [Next: Drone Security & Counter-Drones →](37-drone-security.md)

---

## Frame Design Principles

The frame is the structural backbone of your drone. It determines durability, vibration characteristics, aerodynamics, and component accessibility.

    ┌──────────────────────────────────────────────────┐
    │         FRAME DESIGN TRADEOFFS                  │
    │                                                  │
    │  Strength ←──────→ Weight                       │
    │  Thick carbon = strong but heavy                │
    │                                                  │
    │  Stiffness ←─────→ Vibration Damping            │
    │  Stiff = clean PID, but transmits vibrations    │
    │                                                  │
    │  Compact ←────────→ Prop Protection              │
    │  Tight build = aero, but props exposed          │
    │                                                  │
    │  Accessible ←────→ Aerodynamic                  │
    │  Easy to work on vs clean airflow               │
    └──────────────────────────────────────────────────┘

---

## Frame Configurations

### Quadcopter Layouts

    True-X:               Stretched-X:          Dead Cat:
    M ─── M               M ───── M              M ─── M
     \   /                  \   /                  \   /
      \ /                    \ /                    \ /
       X                      X                      X
      / \                    / \                    / \
     /   \                  /   \                  /   \
    M ─── M               M ── M              M ─────── M

    Equal arm length     Front wide,         Front narrow,
    Balanced flight      back narrow          back wide
    Most common          Racing               Camera unobstructed

### Multi-Rotor Configurations

| Config | Motors | Redundancy | Payload | Use Case |
|--------|--------|-----------|---------|----------|
| **Tricopter** | 3 | None | Low | Fun, unique |
| **Quadcopter** | 4 | None | Medium | Most common |
| **Hexacopter** | 6 | Can lose 1 motor | High | Photography, safety |
| **Octocopter** | 8 | Can lose 2 motors | Very high | Heavy lift, cinema |

---

## Frame Materials

| Material | Strength | Weight | Cost | Vibration | Use Case |
|----------|----------|--------|------|-----------|----------|
| **Carbon fiber** | Excellent | Very light | High | Stiff (good) | Performance builds |
| **3K carbon** | Excellent | Light | Very high | Very stiff | Premium frames |
| **Fiberglass (G10)** | Good | Medium | Low | Medium | Budget frames |
| **3D printed (PETG)** | Fair | Heavy | Very low | Flexible | Prototypes, micro |
| **Aluminum** | Good | Heavy | Medium | Resonant | Motor mounts, spacers |
| **Plywood** | Fair | Medium | Very low | Dampens well | Educational, prototype |

### Carbon Fiber Thickness Guide

| Thickness | Use Case | Notes |
|-----------|----------|-------|
| 1.5mm | Arms (micro/3") | Light but fragile |
| 2.0mm | Arms (5" light) | Freestyle builds |
| 2.5mm | Arms (5" standard) | Good crash resistance |
| 3.0mm | Arms (heavy/7"+) | Very durable |
| 1.5-2.0mm | Top/bottom plates | Adequate for plates |

---

## Aerodynamic Considerations

    Sources of drag on a quadcopter:
    ├── Frame arms (flat plates = bad, aerofoil = better)
    ├── Standoffs and spacers (round > hex > square)
    ├── Exposed wires and cables (tuck everything inside)
    ├── Battery mount (streamline or shield from airflow)
    ├── Camera and gimbal (frontal area)
    └── GPS mast (minimal cross-section)

    Speed vs Drag:
    Drag increases with the SQUARE of speed.
    At 100 km/h, drag is 4× what it is at 50 km/h.

    For photography drones: Drag doesn't matter much (slow flight)
    For racing/long-range: Every bit of drag reduction counts

---

## Designing Your Own Frame

### Software

| Tool | Type | Price | Best For |
|------|------|-------|----------|
| **Fusion 360** | CAD | Free (hobby) | Full 3D frame design |
| **OnShape** | CAD | Free (public) | Browser-based design |
| **OpenSCAD** | Parametric | Free | Code-based designs |
| **Inkscape** | 2D vector | Free | Flat plate cutting profiles |

### Design Guidelines

    Arm design:
    ├── Motor mount: 4× M3 holes at 16×16mm or 19×19mm
    ├── Arm width: 12-18mm for 5" (wider = stiffer)
    ├── Taper toward tips (less weight where it matters less)
    └── Round leading edge if possible

    Stack mounting:
    ├── 20×20mm M3/M2 pattern (mini stacks)
    ├── 30.5×30.5mm M3 pattern (standard)
    ├── Use soft-mount grommets between FC and frame
    └── Allow airflow over ESC for cooling

    Battery mounting:
    ├── Non-slip pad (battery grip tape)
    ├── Battery strap with buckle (not just Velcro)
    ├── Low CG position (under frame preferred)
    └── Easy swap access

---

## Manufacturing Your Frame

### CNC Carbon Fiber

    Services in India:
    ├── CarbonQuad (carbonquad.com)
    ├── Custom cut from local CNC shops
    └── Price: ₹500-₹2,000 per plate (depending on complexity)

    File format: DXF or SVG (2D profile)
    Material: 3K twill carbon fiber plate
    Tolerances: ±0.1mm typical

### 3D Printed Frame

    Material: PETG or CF-PETG
    Settings: 4 walls, 30% gyroid infill
    Suitable for: < 250g AUW drones
    Not suitable for: Crash-heavy FPV (will break)

---

[← Previous: Motor & Propeller Selection](35-motor-propeller-selection.md) | [Back to Index](../README.md) | [Next: Drone Security & Counter-Drones →](37-drone-security.md)
