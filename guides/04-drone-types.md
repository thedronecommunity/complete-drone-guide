# Guide 04: Drone Types & Selection

[← Previous: Components](03-components.md) | [Back to Index](../README.md) | [Next: Software →](05-software-architecture.md)

---

## Overview

Different missions require different aircraft. This guide covers the three main types and helps you choose the right one.

```
┌─────────────────────────────────────────────────────────────┐
│                      DRONE TYPES                            │
├──────────────┬──────────────────┬───────────────────────────┤
│  QUADCOPTER  │    FIXED-WING    │      HYBRID VTOL          │
│   (Rotary)   │    (Airplane)    │    (Best of Both)         │
├──────────────┼──────────────────┼───────────────────────────┤
│   ▪ Hover    │   ▪ Efficiency   │   ▪ Hover + Efficiency    │
│   ▪ Precise  │   ▪ Long range   │   ▪ Complex               │
│   ▪ Simple   │   ▪ Fast         │   ▪ Expensive             │
└──────────────┴──────────────────┴───────────────────────────┘
```

---

## Quadcopters (Multirotors)

### How They Work

Four (or more) propellers provide lift and control through differential thrust.

```
    Motor 4         Motor 2
    (CCW)           (CW)
         \         /
          \       /
           \     /
            \   /
             \ /
              X
             / \
            /   \
           /     \
          /       \
         /         \
    Motor 3         Motor 1
    (CW)            (CCW)
```

### Strengths

| Advantage | Why It Matters |
|-----------|----------------|
| **VTOL** | No runway needed - fly anywhere |
| **Precision hover** | Hold exact position for photography |
| **Omnidirectional** | Move any direction instantly |
| **Camera stability** | Gimbals work best with hover capability |
| **Intuitive controls** | Easy to learn |
| **Simple mechanics** | Just motors - no servos, linkages |

### Limitations

| Limitation | Impact |
|------------|--------|
| 10-30 min flight time | Multiple batteries needed |
| Inefficient forward flight | Not for long distances |
| Wind sensitive | Below 20mph winds |
| Noisy | Four props = lots of sound |
| Limited speed | 40-100 km/h typical |

### Best Applications

- Aerial photography and videography
- Building/infrastructure inspection
- Search patterns (small areas)
- FPV racing
- Learning/hobby flying
- Indoor flight

### Quadcopter Variations

| Type | Motors | Characteristics |
|------|--------|-----------------|
| Tricopter | 3 | Tilting rear motor for yaw, unique look |
| Quadcopter | 4 | Standard, most common |
| Hexacopter | 6 | Redundancy, can land with 1 motor out |
| Octocopter | 8 | Maximum redundancy, heavy lift |
| X8 | 8 (coaxial) | Compact, very heavy lift |

---

## Fixed-Wing (Airplanes)

### How They Work

Wings generate lift from forward motion. Thrust from pusher or puller prop. Control surfaces (ailerons, elevator, rudder) control attitude.

```
           ┌─────────┐
           │  Tail   │
           └────┬────┘
                │
    ╔═══════════╧═══════════╗
    ║                       ║
    ║         WING          ║
    ║                       ║
    ╚═══════════════════════╝
                │
           ┌────┴────┐
           │  Nose   │
           │  Prop   │
           └─────────┘
```

### Strengths

| Advantage | Comparison |
|-----------|------------|
| **Flight time** | 1-3 hours (vs 15-30min quad) |
| **Speed** | 50-200+ mph |
| **Efficiency** | Wings generate "free" lift |
| **Range** | Hundreds of km possible |
| **Wind resistance** | Can fly in 30+ mph winds |
| **Payload efficiency** | Better lift-to-weight ratio |

### Limitations

| Limitation | Impact |
|------------|--------|
| **Needs runway** | 50-200+ feet for takeoff/landing |
| **No hover** | Can't hold position |
| **Complex aerodynamics** | Stall, spin recovery needed |
| **Landing skill** | Harder to master |
| **Turning radius** | Can't turn on a dime |

### Takeoff/Landing Methods

| Method | Pros | Cons |
|--------|------|------|
| Hand launch | Simple, no equipment | Requires practice |
| Catapult | Repeatable, automated | Equipment needed |
| Runway roll | Natural, gentle | Needs flat surface |
| Belly landing | Simple | Wears airframe |
| Parachute | Precise, gentle | Adds weight |

### Best Applications

- Large area mapping/survey
- Search and rescue (large areas)
- Agricultural monitoring
- Long-distance delivery
- Surveillance
- Scientific research

---

## Hybrid VTOL

### How They Work

Combine quadcopter hover capability with fixed-wing efficiency. Two main types:

**Tail-sitter:**
- Takes off vertically like a rocket
- Transitions to horizontal flight
- Example: Google Wing (original)

**Tiltrotor:**
- Rotors tilt from vertical to horizontal
- Like V-22 Osprey
- More complex mechanically

```
VTOL Mode:                    Cruise Mode:
    ●   ●                         ╔═══════════╗
     \ /                          ║    ●──●   ║
      │                           ╚═══════════╝
      │                                 ↓
      │                              Direction
     / \
    ●   ●
```

### Strengths

| Advantage | Why It Matters |
|-----------|----------------|
| **VTOL + range** | Best of both worlds |
| **Land anywhere** | No runway needed |
| **Fast cruise** | 40-150 mph |
| **Long endurance** | 45-90 minutes typical |
| **Versatile** | Hover when needed, cruise when not |

### Limitations

| Limitation | Impact |
|------------|--------|
| **Most complex** | More failure points |
| **Transition risk** | Accident-prone phase |
| **Weight penalty** | Extra motors/systems |
| **High cost** | 2-5x equivalent quad |
| **Maintenance** | More systems to service |

### Best Applications

- Delivery (mixed terrain)
- Military surveillance
- Infrastructure inspection (linear + point)
- Emergency services
- Long-range mapping with precision targets

---

## Type Comparison Table

| Factor | Quadcopter | Fixed-Wing | Hybrid VTOL |
|--------|------------|------------|-------------|
| **Lift source** | 4+ propellers | Wings + speed | Rotors + wings |
| **Takeoff/Landing** | Vertical anywhere | Runway 50-200ft | Vertical anywhere |
| **Flight time** | 10-30 min | 1-3 hours | 45-90 min |
| **Speed** | 20-40 mph | 50-200+ mph | 40-150 mph |
| **Hovering** | Excellent | No | Yes (VTOL mode) |
| **Wind resistance** | Poor-Moderate | Excellent | Good-Excellent |
| **Efficiency** | Low | Very high | High (cruise) |
| **Payload capacity** | Moderate | Very good | Good |
| **Control ease** | Easy | Moderate | Difficult |
| **Build complexity** | Low | Moderate | Very high |
| **Maintenance** | Low | Low | High |
| **Cost** | Low-Moderate | Low-Moderate | Very high |
| **Learning curve** | Gentle | Moderate | Steep |
| **Best for** | Photography, racing | Mapping, delivery | Long-range hybrid |

---

## Flight Controller Comparison

| Feature | ArduPilot | PX4 | Betaflight | iNav |
|---------|-----------|-----|------------|------|
| **Focus** | Stability & reliability | Innovation & research | Racing & acrobatics | GPS navigation |
| **Vehicles** | Planes, copters, rovers, boats, subs | Planes, copters, VTOL, rovers | Multirotors only | Planes, copters, wings |
| **Learning curve** | Moderate | Steep | Moderate | Gentle |
| **Documentation** | Excellent | Good (technical) | Excellent | Good |
| **Community** | Very large | Large | Very large | Moderate |
| **Autonomous missions** | Excellent | Excellent | None | Good (basic) |
| **GPS features** | Full (RTK, precision) | Full (RTK, advanced) | Basic (rescue) | Good |
| **FPV racing** | Not optimized | Not optimized | Optimized | Capable |
| **Long-range** | Excellent | Excellent | Poor | Excellent |
| **Companion computer** | Excellent (MAVLink) | Excellent (ROS) | Limited | Limited |
| **Best for** | Commercial, multi-vehicle | Research, custom | Racing, freestyle | Beginner GPS |

---

## Motor & Propeller Selection

### By Application

| Application | Frame | KV Rating | Prop Size | Battery |
|-------------|-------|-----------|-----------|---------|
| **Racing** | 5" | 2300-2800 | 5" | 4S-6S |
| **Freestyle** | 5" | 1800-2400 | 5-6" | 4S-6S |
| **Cinematic** | 5-7" | 1400-1800 | 6-7" | 4S-6S |
| **Long range** | 7" | 1200-1600 | 7-9" | 6S |
| **Heavy lift** | 10"+ | 400-800 | 10-18" | 6S-12S |
| **Micro/Tiny** | <3" | 3000-8000 | 2-3" | 1S-3S |

### Selection Formula

```
Lower KV = Larger props = More efficiency = Less speed
Higher KV = Smaller props = More speed = Less efficiency
```

---

## Battery Selection Guide

### By Cell Count

| Cells | Nominal | Full | Applications |
|-------|---------|------|--------------|
| 1S | 3.7V | 4.2V | Tiny whoops, indoor micro |
| 2S | 7.4V | 8.4V | Small indoor, beginner |
| 3S | 11.1V | 12.6V | Small outdoor, learning |
| 4S | 14.8V | 16.8V | Standard 5" racing/freestyle |
| 5S | 18.5V | 21.0V | High-performance 5" |
| 6S | 22.2V | 25.2V | Racing, freestyle, cinema |
| 8S | 29.6V | 33.6V | Large, heavy lift |
| 10-12S | 37-44V | 42-50V | Industrial, heavy lift |

### By C-Rating

| C-Rating | Application | Price |
|----------|-------------|-------|
| 20-35C | Gentle photography | Budget |
| 40-60C | General learning | Moderate |
| 65-90C | Freestyle, aggressive | Higher |
| 95-120C | Racing, max performance | Premium |

---

## Choosing Your Drone Type

### Decision Tree

```
Do you need to hover?
├── YES → Do you need long range (>10km)?
│         ├── YES → Hybrid VTOL
│         └── NO → Quadcopter
└── NO → Fixed-Wing
```

### By Use Case

| Use Case | Best Type | Reasoning |
|----------|-----------|-----------|
| Real estate photos | Quadcopter | Hover for stable shots |
| Farm mapping (1000 acres) | Fixed-wing | Efficiency for large area |
| Building inspection | Quadcopter | Precision positioning |
| Package delivery (20km) | Hybrid VTOL | VTOL + range |
| Search and rescue | Fixed-wing | Cover more ground |
| FPV racing | Quadcopter | Agility |
| Pipeline inspection | Fixed-wing or VTOL | Long linear routes |
| Event photography | Quadcopter | Hover and move freely |

---

## Key Takeaways

1. **Quadcopters** = Hover + precision, but limited range/time
2. **Fixed-wing** = Efficiency + range, but needs runway
3. **Hybrid VTOL** = Best of both, but complex and expensive
4. **Choose based on mission**, not what looks cool
5. **Start with quadcopter** for learning - easiest to fly

---

[← Previous: Components](03-components.md) | [Back to Index](../README.md) | [Next: Software →](05-software-architecture.md)
