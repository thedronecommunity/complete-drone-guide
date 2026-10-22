# Guide 28: Drone Racing

[← Previous: Search & Rescue](27-search-rescue.md) | [Back to Index](../README.md) | [Next: Long Range & BVLOS →](29-long-range-bvlos.md)

---

## FPV Drone Racing

Drone racing is flying small, agile quadcopters through obstacle courses at speeds of 100-180 km/h while wearing FPV goggles. It demands fast reflexes, mechanical skill, and well-tuned hardware.

    ┌──────────────────────────────────────────────────┐
    │           RACING DRONE SPECS                    │
    │                                                  │
    │  Class: 5" (most popular)                       │
    │  Weight: 350-550g (without battery)             │
    │  Top speed: 140-180 km/h                        │
    │  0-100 km/h: ~1 second                          │
    │  Battery: 4S or 6S 1300-1500mAh                 │
    │  Flight time: 2-4 minutes (full throttle)       │
    │  Thrust-to-weight: 8:1 to 12:1                  │
    └──────────────────────────────────────────────────┘

---

## Racing Build Guide

### Components

| Part | Recommendation | Price (India) |
|------|---------------|--------------|
| **Frame** | TBS Source One V5 / ImpulseRC Apex | ₹2,000-₹4,500 |
| **Motors** | T-Motor Velox V3 2207 1950KV (6S) | ₹5,000-₹8,000 (set) |
| **ESC** | 4-in-1 55A BLHeli_32 | ₹3,000-₹5,000 |
| **FC** | F7 with Betaflight (STM32F722) | ₹3,000-₹5,000 |
| **FPV Camera** | Foxeer Razer / Caddx Ratel | ₹2,000-₹3,500 |
| **VTx** | TBS Unify Pro32 / Rush Tank | ₹2,500-₹4,000 |
| **Receiver** | ELRS EP1/EP2 | ₹800-₹1,500 |
| **Props** | HQProp 5x4.3x3 V2S | ₹200-₹400 (set) |
| **Battery** | 6S 1300mAh 100C+ | ₹3,000-₹5,000 |
| **Total** | | **₹22,000-₹36,000** |

### Betaflight Racing Configuration

    # Rate profile for racing
    set rates_type = ACTUAL
    set roll_rc_rate = 200
    set pitch_rc_rate = 200
    set yaw_rc_rate = 200
    set roll_expo = 56
    set pitch_expo = 56
    set roll_srate = 53
    set pitch_srate = 53
    set yaw_srate = 53

    # Turtle mode (flip after crash)
    set small_angle = 180
    # Assign to switch: FLIP OVER AFTER CRASH

    # Air mode (maintains control at zero throttle)
    set airmode_start_throttle_percent = 25

---

## Simulators

Practice before crashing expensive hardware:

| Simulator | Platform | Price | Realism |
|-----------|----------|-------|---------|
| **Velocidrone** | PC | ₹1,500 | Best physics |
| **Liftoff** | PC/Console | ₹1,500 | Good visuals |
| **Tryp FPV** | PC (VR) | ₹1,200 | VR immersion |
| **Orqa FPV.SkyDive** | PC | Free | Decent beginner |
| **Uncrashed** | PC | ₹800 | Freestyle focused |

    Minimum 20-50 hours in simulator before first real flight.
    Practice: figure-8s, power loops, split-S, orbits, gates.

---

## Racing Organizations

| Organization | Region | Format |
|-------------|--------|--------|
| **MultiGP** | Global | Local chapters, league races |
| **FAI / World Drone Racing** | Global | International championships |
| **Indian FPV League** | India | Growing community |
| **TDC Community Races** | Pune/Bangalore | Community events |

---

## Race Day Checklist

    Before race:
    □ All batteries fully charged
    □ Props inspected (no nicks or cracks)
    □ VTx channel assigned (no conflicts)
    □ Failsafe tested (disarm on signal loss)
    □ Spare parts packed (props, arms, camera)
    □ Goggles charged and channel set
    □ Radio bound and tested
    □ Arm switch verified

    Between heats:
    □ Check frame for cracks
    □ Check motor screws
    □ Replace damaged props
    □ Cool battery before recharging
    □ Review DVR footage for line optimization

---

## Skills Progression

    Beginner (Month 1-3):
    ├── Hover control (all orientations)
    ├── Smooth figure-8s
    ├── Basic gates and gaps
    └── Landing without crashing

    Intermediate (Month 3-6):
    ├── Power loops and split-S
    ├── Consistent gate navigation
    ├── Speed management through corners
    └── Race pace for 3-lap runs

    Advanced (Month 6+):
    ├── Proximity flying
    ├── Inverted maneuvers
    ├── Variable speed through technical sections
    └── Competitive lap times

---

[← Previous: Search & Rescue](27-search-rescue.md) | [Back to Index](../README.md) | [Next: Long Range & BVLOS →](29-long-range-bvlos.md)
