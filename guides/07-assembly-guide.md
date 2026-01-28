# Guide 07: Assembly Guide

[← Previous: Communication](06-communication-protocols.md) | [Back to Index](../README.md) | [Next: Raspberry Pi →](08-raspberry-pi-integration.md)

---

## Pre-Assembly Checklist

### Essential Tools

| Tool | Purpose |
|------|---------|
| Phillips screwdrivers | Frame assembly |
| Flathead screwdrivers | Adjustments |
| Hex keys (1.5-3mm) | Motor bolts |
| Wire strippers | Preparing wires |
| Soldering iron (25-40W) | Connections |
| Solder (60/40 or lead-free) | Joining wires |
| Multimeter | Testing connections |
| Zip ties | Wire management |
| 3M VHB tape | Mounting components |
| Heat shrink tubing | Insulation |
| Flux | Better solder joints |

### Safety Equipment

| Item | Purpose |
|------|---------|
| Safety glasses | Protect eyes when soldering |
| Anti-static wrist strap | Protect electronics |
| Fire extinguisher | LiPo safety |
| First aid kit | Minor injuries |
| Ventilation/fan | Solder fumes |

### Workspace

- Clean, well-lit area
- Non-conductive surface
- Organized parts storage
- No loose objects that could fall on drone

---

## Frame Assembly

### Frame Configurations

| Configuration | Description | Best For |
|---------------|-------------|----------|
| **X-config** | Arms at 45° angles | Most common, symmetrical |
| **H-config** | Rectangular layout | Better camera space |
| **Dead Cat** | Wide front, narrow rear | Camera visibility |
| **Plus (+)** | Arms at 0°/90° | Rarely used |

### Frame Materials

| Material | Weight | Strength | Cost | Notes |
|----------|--------|----------|------|-------|
| Carbon fiber | Light | Very high | High | Can block GPS signals |
| Aluminum | Moderate | High | Moderate | Good heat dissipation |
| Plastic/Nylon | Heavy | Low | Low | Beginner friendly |
| Fiberglass | Moderate | Good | Moderate | Good compromise |

### Assembly Steps

```
1. Identify all frame components
   └── Bottom plate, top plate, arms, standoffs, hardware
   
2. Inspect for damage
   └── Cracks, chips, bent parts
   
3. Assemble center section
   └── Bottom plate + standoffs
   
4. Attach arms
   └── Even torque on all bolts
   
5. Add top plate
   └── Don't fully tighten yet
   
6. Verify geometry
   └── Equal diagonals (measure corner to corner)
   └── Frame should be flat on surface
   
7. Final tightening
   └── Star pattern for even pressure
```

---

## Motor Installation

### Motor Pattern (Critical!)

```
          FRONT
    
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
    Motor 3         Motor 1
    (CW)            (CCW)
    
          BACK
```

**Pattern:** 1 & 3 = CW, 2 & 4 = CCW (diagonal pairs same direction)

### Installation Process

1. **Match motor specs to frame**
   - Verify KV rating
   - Check mounting hole pattern

2. **Apply thread locker (blue Loctite)**
   - Small drop on each screw
   - Prevents vibration loosening

3. **Mount motors**
   - Use correct length screws (too long damages windings!)
   - Tighten in star pattern
   - Don't overtighten

4. **Verify flush mounting**
   - Motor base flat against arm
   - No gaps or wobble

### Wire Routing

- Route motor wires along arms
- Secure with zip ties every 3-4 inches
- Leave slack at motor end for vibration
- Avoid sharp bends

---

## ESC Installation

### Options

| Type | Mounting | Pros | Cons |
|------|----------|------|------|
| **Individual ESCs** | On each arm | Easy repair | More wiring |
| **4-in-1 ESC** | Center stack | Clean wiring | Replace whole unit |

### Connection Diagram

```
Battery (+) ───────────────→ ESC Power (+)
Battery (-) ───────────────→ ESC Power (-)

ESC Motor A ─────→ Motor Wire 1
ESC Motor B ─────→ Motor Wire 2  (Any two can swap to reverse)
ESC Motor C ─────→ Motor Wire 3

ESC Signal ──────→ FC Motor Output (M1-M4)
ESC Ground ──────→ FC Ground
```

### Motor Direction

**To reverse motor direction:** Swap ANY two of the three motor wires.

```
Normal:                    Reversed:
ESC A → Motor 1           ESC A → Motor 2  (swapped)
ESC B → Motor 2           ESC B → Motor 1  (swapped)
ESC C → Motor 3           ESC C → Motor 3
```

Or use BLHeli configurator to reverse in software.

---

## Power Distribution

### Power Flow Diagram

```
                    ┌──────────────────────────────┐
                    │          BATTERY             │
                    │         (4S-6S)              │
                    └─────────────┬────────────────┘
                                  │
                                  ▼
                    ┌──────────────────────────────┐
                    │     PDB / 4-in-1 ESC         │
                    │                              │
                    │  Full Voltage    5V BEC      │
                    │      │              │        │
                    └──────┼──────────────┼────────┘
                           │              │
              ┌────────────┼────────────┐ │
              │            │            │ │
              ▼            ▼            ▼ ▼
           [ESC1]       [ESC2]       [ESC3]   ┌────────────┐
           [ESC4]                             │     FC     │
                                              │  Receiver  │
                                              │    GPS     │
                                              │     Pi     │
                                              └────────────┘
```

### Battery Sizing

```
Total current = Motor max × 4 × 1.2 (safety margin)

Example:
- 30A motors
- 30 × 4 = 120A
- 120 × 1.2 = 144A minimum battery capability

Choose battery:
- 5000mAh at 35C = 175A capability ✓
```

### Mounting Battery

- Use velcro straps or mounting plate
- Position for proper center of gravity
- Easy removal for charging
- Secure against vibration (it's the heaviest component!)

---

## Flight Controller Installation

### Placement Requirements

| Requirement | Reason |
|-------------|--------|
| Center of drone | Balanced sensor readings |
| Vibration dampening | Accurate IMU data |
| Arrow points forward | Correct orientation |
| Protected location | Avoid debris damage |

### Vibration Isolation

```
┌─────────────────────────┐
│    Flight Controller    │
└──────────┬──────────────┘
           │
    [Rubber grommets or foam]
           │
┌──────────┴──────────────┐
│      Mounting Plate     │
└─────────────────────────┘
```

Options:
- Rubber O-rings on standoffs
- Foam tape (3M VHB)
- Dedicated anti-vibration mount
- Soft standoffs

### Essential Connections

| Connection | From | To |
|------------|------|-----|
| Power | PDB 5V | FC power input |
| Motor 1 | FC M1 | ESC 1 signal |
| Motor 2 | FC M2 | ESC 2 signal |
| Motor 3 | FC M3 | ESC 3 signal |
| Motor 4 | FC M4 | ESC 4 signal |
| Receiver | RC RX | FC SBUS/PPM input |
| GPS | GPS module | FC GPS port |
| Telemetry | Telemetry radio | FC TELEM port |
| Buzzer | FC buzzer output | Buzzer |

---

## GPS Module Installation

### Mounting Requirements

| Requirement | Reason |
|-------------|--------|
| On top of drone | Clear view of sky |
| Away from motors | Reduce EMI |
| Away from power wires | Reduce EMI |
| Arrow forward | Same orientation as FC |
| Raised if possible | Better satellite reception |

### GPS Mast

For best compass performance, mount GPS on a mast:

```
         [GPS Module]
              │
              │ (10-15cm)
              │
    ══════════╧══════════
         [Drone Frame]
```

This separates compass from:
- Motor magnetic fields
- Power wires
- Carbon fiber (which can shield signals)

---

## Sensor Calibration

### Compass Calibration

```
1. Go outdoors, away from:
   - Metal structures
   - Power lines
   - Vehicles
   - Electronic devices

2. Start calibration in GCS

3. Rotate drone in all axes:
   - Horizontal rotation (yaw)
   - Nose down rotation (pitch)
   - Side rotation (roll)
   
4. Continue until calibration completes

5. Verify heading matches actual direction
```

**Tips:**
- Do calibration at flying location
- Multiple attempts may be needed
- Check for compass interference sources

### Accelerometer Calibration

```
6-position calibration:

Position 1: Level (right-side up)
Position 2: Nose down (90°)
Position 3: Nose up (90°)
Position 4: Left side down (90°)
Position 5: Right side down (90°)
Position 6: Upside down (180°)

Hold steady 10+ seconds each position.
```

### Gyroscope Calibration

```
1. Place drone on stable surface
2. Completely still
3. Stable temperature
4. No vibration
5. Wait 30-60 seconds
```

Usually automatic at power-on.

### Level Horizon

```
1. Place on known level surface (use bubble level)
2. Run level calibration
3. Verify artificial horizon is level in GCS
```

---

## Radio Setup

### Standard Channel Mapping

| Channel | Function |
|---------|----------|
| 1 | Roll / Aileron |
| 2 | Pitch / Elevator |
| 3 | Throttle |
| 4 | Yaw / Rudder |
| 5 | Flight mode switch |
| 6-8 | Auxiliary functions |

### Transmitter Configuration

1. **Create new model** for this drone
2. **Set aircraft type** to quadcopter/multirotor
3. **Channel order** - AETR (Aileron, Elevator, Throttle, Rudder)
4. **Stick mode** - Mode 2 most common (throttle on left)
5. **Endpoints** - Usually 1000-2000μs
6. **Subtrim** - Center sticks at 1500μs
7. **Expo/rates** - Personal preference
8. **Flight mode switch** - 3-position switch recommended
9. **Failsafe** - Configure appropriate action

### Binding Process

```
1. Power off transmitter and receiver

2. Put receiver in bind mode:
   - Hold bind button while powering on
   - Or use bind plug
   - LED should flash rapidly

3. Put transmitter in bind mode:
   - Usually in menu: Setup → Bind

4. Wait for confirmation:
   - Receiver LED solid
   - Transmitter shows bound

5. Power cycle both

6. Test all channels in GCS
```

### Failsafe Configuration

| Component | Setting |
|-----------|---------|
| Transmitter | Send specific values on signal loss |
| Receiver | Output failsafe values |
| Flight Controller | RTL or Land on failsafe |

**Test failsafe before flying!** Turn off transmitter and verify drone responds correctly.

---

## Pre-Flight Verification

### Motor Test (PROPS OFF!)

```
⚠️ REMOVE ALL PROPELLERS FIRST ⚠️

1. Power drone with battery
2. Connect to GCS
3. In Motor Test tab:
   - Spin Motor 1 → Verify rear-right spins CCW
   - Spin Motor 2 → Verify front-right spins CW
   - Spin Motor 3 → Verify rear-left spins CW
   - Spin Motor 4 → Verify front-left spins CCW
4. Fix any wrong directions before installing props
```

### Control Direction Test (PROPS OFF!)

```
1. Arm drone (props off!)
2. Tilt nose DOWN → Front motors should speed up
3. Tilt nose UP → Rear motors should speed up
4. Tilt LEFT → Right motors should speed up
5. Tilt RIGHT → Left motors should speed up
6. If any direction wrong, check:
   - Motor order
   - FC orientation
   - Motor direction
```

### Propeller Installation

**Only after all tests pass:**

| Motor Direction | Propeller Type |
|-----------------|----------------|
| CW motor | CW propeller |
| CCW motor | CCW propeller |

```
         FRONT
    
    CCW Prop        CW Prop
    (Motor 4)       (Motor 2)
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
    CW Prop         CCW Prop
    (Motor 3)       (Motor 1)
    
          BACK
```

Secure nuts firmly but don't overtighten.

---

## Final Checklist

### Before First Flight

- [ ] Frame tight, no wobble
- [ ] Motors spin correct direction
- [ ] Props correct direction
- [ ] Props secure
- [ ] FC arrow points forward
- [ ] All sensors calibrated
- [ ] Radio calibrated, all channels work
- [ ] Failsafe configured and tested
- [ ] Battery secured
- [ ] Center of gravity correct
- [ ] No loose wires
- [ ] Firmware updated
- [ ] Flight modes configured
- [ ] Geofence set (optional but recommended)
- [ ] Weather appropriate
- [ ] Flying area clear

---

## Key Takeaways

1. **Motor pattern is critical** - wrong = flip on takeoff
2. **TX→RX crossover** for UART connections
3. **Props OFF for testing** - always
4. **Vibration isolation** protects sensors
5. **Calibrate at flying location** for compass
6. **Test failsafe** before every flight session

---

[← Previous: Communication](06-communication-protocols.md) | [Back to Index](../README.md) | [Next: Raspberry Pi →](08-raspberry-pi-integration.md)
