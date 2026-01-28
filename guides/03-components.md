# Guide 03: Drone Components

[← Previous: Physics of Flight](02-physics-of-flight.md) | [Back to Index](../README.md) | [Next: Drone Types →](04-drone-types.md)

---

## Component Overview

```
┌─────────────────────────────────────────────────────────────┐
│                        DRONE SYSTEM                         │
├─────────────────────────────────────────────────────────────┤
│  CONTROL          PROPULSION         POWER        SENSORS   │
│  ───────          ──────────         ─────        ───────   │
│  Flight Controller   Motors          Battery      GPS       │
│  Receiver            ESCs            PDB          IMU       │
│  Transmitter         Propellers      Wiring       Compass   │
│  Telemetry                                        Barometer │
└─────────────────────────────────────────────────────────────┘
```

---

## Flight Controller (The Brain)

The flight controller is a 32-bit ARM processor running 1000+ times per second, constantly:
- Reading sensors
- Making stability adjustments
- Translating stick movements
- Handling emergencies
- Storing flight plans

### Popular Flight Controllers

| Controller | Processor | Features | Best For |
|------------|-----------|----------|----------|
| **Pixhawk** | ARM Cortex | 15+ ports, redundant sensors | Professional, ArduPilot |
| **CUAV V6X** | STM32H7 | Ethernet, DroneCAN | Advanced autonomous |
| **Matek F405** | STM32F4 | Compact, integrated OSD | FPV racing |
| **SpeedyBee F7** | STM32F7 | Bluetooth config, WiFi | Beginner FPV |

### Firmware Options

| Feature | ArduPilot | PX4 | Betaflight |
|---------|-----------|-----|------------|
| **Philosophy** | Stability first | Innovation first | Racing performance |
| **Best for** | Beginners, commercial | Research, custom | FPV racing/freestyle |
| **Community** | Very large | Large | Very large |
| **Documentation** | Comprehensive | Technical | Excellent |
| **Vehicle types** | 100+ (planes, boats, subs) | Focused set | Multirotors only |
| **Autonomous** | Excellent | Excellent | None |
| **Learning curve** | Moderate | Steep | Moderate |

---

## Motors

### Brushless DC (BLDC) Motors

All modern drones use brushless motors because:
- No physical brushes to wear out
- 85-90% efficiency (vs 75-80% brushed)
- Thousands of hours of operation
- Higher power-to-weight ratio
- Better heat dissipation

**Requires ESC to operate** - brushless motors need electronic commutation.

### KV Rating

**KV = RPM per volt applied**

Example: 1000KV motor on 4S (16.8V) = 16,800 RPM (unloaded)

| KV Range | Prop Size | Application |
|----------|-----------|-------------|
| 400-800 | 10-18" | Heavy lift, large drones |
| 900-1200 | 7-10" | Long range, efficiency |
| 1400-1800 | 6-7" | Cinematic, smooth |
| 1800-2400 | 5-6" | Freestyle |
| 2300-2800 | 5" | Racing |
| 3000-8000 | 2-3" | Micro/tiny whoops |

### Motor Specifications

```
Example: 2306 2450KV

  23  = Stator diameter (23mm)
  06  = Stator height (6mm)
2450  = KV rating

Larger stator = more torque
Taller stator = more power
```

---

## Electronic Speed Controllers (ESCs)

ESCs convert DC battery power to 3-phase AC for brushless motors.

### Functions

- Variable speed control (1000+ steps)
- Direction control (normal/reversed)
- Overcurrent protection
- Temperature monitoring
- Telemetry (some models)

### Specifications

| Spec | Meaning | Example |
|------|---------|---------|
| **Current rating** | Max continuous amps | 40A continuous |
| **Burst rating** | Short-term max | 50A for 10s |
| **Voltage range** | Compatible batteries | 3-6S (11.1-25.2V) |
| **Protocol** | Communication method | DShot600 |
| **BEC** | Built-in voltage regulator | 5V/2A |

### Sizing Rule

```
ESC rating = Motor max current × 1.2 (20% safety margin)

Example: 30A max motor → 36A ESC minimum (use 40A)
```

### ESC Protocols

| Protocol | Speed | Features | Age |
|----------|-------|----------|-----|
| PWM | 50Hz | Universal | Old |
| OneShot125 | 4kHz | Faster response | 2015 |
| OneShot42 | 12kHz | Even faster | 2016 |
| DShot150 | 150kbps | Digital, error checking | 2016 |
| DShot300 | 300kbps | Standard digital | 2017 |
| DShot600 | 600kbps | Fast digital | Current |
| DShot1200 | 1200kbps | Fastest | Cutting edge |

### 4-in-1 vs Individual ESCs

| Aspect | 4-in-1 ESC | Individual ESCs |
|--------|------------|-----------------|
| Wiring | Clean, simple | More complex |
| Weight | Lighter | Heavier |
| Repair | Replace whole unit | Replace one |
| Heat | Concentrated | Distributed |
| Cost | Often cheaper | More flexible |

---

## Propellers

### Naming Convention

```
Example: 10×4.5

10   = Diameter (10 inches)
4.5  = Pitch (4.5 inches per revolution)
```

Pitch = theoretical distance traveled in one rotation (like a screw).

### Propeller Factors

| Factor | Effect | Trade-off |
|--------|--------|-----------|
| **Larger diameter** | More lift at lower RPM | Higher current, slower response |
| **Higher pitch** | More forward thrust | Less efficiency at hover |
| **Carbon fiber** | Light, stiff | Expensive, shatters on impact |
| **Plastic** | Cheap, flexible | Heavier, can bend |

### Blade Count

| Blades | Characteristics |
|--------|-----------------|
| 2-blade | Most efficient, quieter |
| 3-blade | Balance of efficiency and thrust |
| 4+ blade | More thrust, less efficient, smaller diameter needed |

### Rotation Direction

**Critical:** Propellers must match motor rotation direction.

| Motor Direction | Prop Type | Markings |
|-----------------|-----------|----------|
| CW (clockwise) | CW prop | Often unmarked or "R" |
| CCW (counter-clockwise) | CCW prop | Often "L" or different color |

---

## Battery Systems

### LiPo (Lithium Polymer)

The standard for drones due to:
- High energy density
- High discharge rates
- Lightweight
- Available in many sizes

### Voltage Levels

| State | Voltage per Cell | Notes |
|-------|------------------|-------|
| Full charge | 4.2V | Maximum safe |
| Nominal | 3.7V | Rated capacity |
| Storage | 3.8V | For long-term storage |
| Low warning | 3.5V | Land soon |
| Minimum | 3.0V | Never go below |
| Damaged | <3.0V | May not recover |

### Cell Count

| Cells | Nominal | Full Charge | Typical Use |
|-------|---------|-------------|-------------|
| 1S | 3.7V | 4.2V | Tiny whoops, micro |
| 2S | 7.4V | 8.4V | Small indoor |
| 3S | 11.1V | 12.6V | Beginner outdoor |
| 4S | 14.8V | 16.8V | Standard 5" FPV |
| 5S | 18.5V | 21.0V | High-performance |
| 6S | 22.2V | 25.2V | Racing, cinema |
| 8S+ | 29.6V+ | 33.6V+ | Heavy lift |

### Capacity (mAh)

```
mAh = milliamp-hours

5200mAh = 5.2 amps for 1 hour
       = 10.4 amps for 30 minutes
       = 52 amps for 6 minutes
```

### C-Rating

Maximum safe discharge rate.

```
Max current = Capacity × C-rating

Example: 1500mAh × 65C = 97.5A max discharge
```

| C-Rating | Application |
|----------|-------------|
| 20-35C | Gentle flying, photography |
| 40-60C | General use |
| 65-90C | Freestyle |
| 95-120C | Racing |

### Flight Time Examples

| Use Case | Battery | Typical Flight Time |
|----------|---------|---------------------|
| Racing 5" | 1300-1500mAh 4-6S | 3-8 min |
| Freestyle 5" | 1300-1800mAh 4-6S | 4-10 min |
| Photography | 5000-5200mAh | 15-25 min |
| Long range | 10,000+ mAh | 45+ min |

### Battery Safety

**LiPo batteries can catch fire if mishandled.**

| Do | Don't |
|----|-------|
| Use balance charger | Overcharge |
| Store at 3.8V/cell | Leave fully charged |
| Use fireproof bag | Charge unattended |
| Inspect for damage | Use puffed batteries |
| Dispose properly | Puncture or crush |

---

## Power Distribution

### Power Distribution Board (PDB)

Safely distributes battery power to all components.

**Functions:**
- Voltage regulation (5V, 12V outputs)
- Current distribution
- Electrical noise filtering
- Overcurrent protection

### Voltage Requirements

| Component | Voltage | Current |
|-----------|---------|---------|
| Motors/ESCs | Full battery (14.8-25.2V) | 10-40A each |
| Flight controller | 5V | 0.5-1A |
| Receiver | 5V | 0.1A |
| GPS | 5V | 0.1A |
| Raspberry Pi | 5V | 2-3A |
| Servos/gimbals | 5-6V | 1-2A |
| Sensors | 3.3V | 0.1A |

### Power Flow

```
Battery → PDB → ESCs (full voltage)
              → 5V BEC → FC
                      → Receiver
                      → GPS
                      → Companion computer
              → 12V BEC → Video TX
                       → Lights
```

---

## Sensors

### GPS Module (M9N/M10)

| Specification | Value |
|---------------|-------|
| Satellite systems | GPS, GLONASS, Galileo, BeiDou |
| Update rate | 10 Hz |
| Accuracy (standard) | 3-5 meters |
| Accuracy (RTK) | 1-2 cm |
| Time to first fix | 30-60 seconds |
| Satellites tracked | 24+ |

### IMU (Inertial Measurement Unit)

Contains three sensors:

| Sensor | Measures | Axes |
|--------|----------|------|
| **Accelerometer** | Linear acceleration | X, Y, Z |
| **Gyroscope** | Rotational velocity | Roll, Pitch, Yaw |
| **Magnetometer** | Magnetic field (compass) | X, Y, Z |

Modern flight controllers have **triple redundancy** - 3 IMU units for safety.

### Barometer

| Function | Specification |
|----------|---------------|
| Measures | Air pressure |
| Determines | Altitude |
| Accuracy | 1-2 meters |
| Note | Affected by weather, prop wash |

---

## Communication Systems

### RC Protocols

| Protocol | Type | Channels | Latency | Best For |
|----------|------|----------|---------|----------|
| PWM | Analog | 8 | 20ms | Learning |
| PPM | Analog | 8-12 | 20-27ms | Simple wiring |
| S.BUS | Digital | 16 | 7-14ms | Modern standard |
| iBUS | Digital | 14 | 7-10ms | FlySky budget |
| F.Port | Digital | 24 | 9-12ms | FrSky + telemetry |
| CRSF | Digital | 16 | 4-10ms | Long range |
| ELRS | Digital | 16 | 2-5ms | Open source, long range |

### Frequency Bands

| Band | Frequency | Range | Penetration | Use |
|------|-----------|-------|-------------|-----|
| 900MHz | 868/915MHz | 10-50km+ | Excellent | Long-range RC |
| 2.4GHz | 2400-2483MHz | 1-5km | Moderate | Standard RC |
| 5.8GHz | 5650-5950MHz | 0.5-2km | Poor | FPV video |

### Security Features

- Frequency hopping (FHSS)
- 16-128 bit encryption
- Transmitter-receiver binding
- Failsafe programming

---

## Component Selection Guide

### For a 5" Freestyle/General Purpose Build

| Component | Recommendation |
|-----------|----------------|
| Frame | 5" carbon fiber (200-250mm) |
| Motors | 2306 1800-2400KV |
| ESCs | 4-in-1 35-50A |
| FC | F4 or F7 with ArduPilot/Betaflight |
| Props | 5x4.5 triblade |
| Battery | 1300-1500mAh 4-6S |
| Receiver | ELRS or Crossfire |

### For an Autonomous/Photography Build

| Component | Recommendation |
|-----------|----------------|
| Frame | 450-550mm |
| Motors | 2212-2216 900-1000KV |
| ESCs | 30A individual |
| FC | Pixhawk with ArduPilot |
| Props | 10-12" 2-blade |
| Battery | 5000mAh 4S |
| GPS | M9N with compass |
| Receiver | S.BUS compatible |

---

## Key Takeaways

1. **Flight Controller** = Brain - reads sensors, controls motors
2. **Motors** = Muscle - KV rating determines speed/torque trade-off
3. **ESCs** = Nervous system - converts DC to 3-phase AC
4. **Props** = Diameter and pitch determine lift and thrust
5. **Battery** = Fuel - balance capacity vs weight
6. **Sensors** = Senses - GPS, IMU, compass, barometer

---

[← Previous: Physics of Flight](02-physics-of-flight.md) | [Back to Index](../README.md) | [Next: Drone Types →](04-drone-types.md)
