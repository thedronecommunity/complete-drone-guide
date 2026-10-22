# Guide 06: Communication Protocols

[← Previous: Software](05-software-architecture.md) | [Back to Index](../README.md) | [Next: Assembly →](07-assembly-guide.md)

---

## Protocol Overview

Drones use multiple communication protocols at different levels:

```
┌─────────────────────────────────────────────────────────────┐
│                    GROUND STATION                           │
│                         │                                   │
│                    [Telemetry Radio]                        │
│                         │                                   │
│                      MAVLink                                │
│                         ↓                                   │
├─────────────────────────────────────────────────────────────┤
│                   FLIGHT CONTROLLER                         │
│     ┌──────────┬──────────┬──────────┬──────────┐          │
│     │   UART   │   I2C    │   SPI    │   CAN    │          │
│     └────┬─────┴────┬─────┴────┬─────┴────┬─────┘          │
│          │          │          │          │                 │
│        [GPS]    [Compass]   [IMU]     [ESCs]               │
│                  [Baro]    [Flash]   [Sensors]             │
├─────────────────────────────────────────────────────────────┤
│                      RC RECEIVER                            │
│                         │                                   │
│               [S.BUS/CRSF/ELRS/PWM]                         │
│                         ↓                                   │
│                    RC TRANSMITTER                           │
└─────────────────────────────────────────────────────────────┘
```

---

## MAVLink Protocol

The standard protocol for drone communication.

### What MAVLink Connects

- Flight controller ↔ Ground station
- Flight controller ↔ Companion computer
- Flight controller ↔ Other systems (gimbals, cameras)

### Message Types

| Type | Direction | Contains |
|------|-----------|----------|
| **Telemetry** | Drone → Ground | GPS, altitude, speed, battery, status |
| **Commands** | Ground → Drone | Mode changes, arm/disarm, takeoff/land |
| **Mission** | Bidirectional | Waypoints, start/pause, progress |
| **Parameters** | Bidirectional | Configuration read/write |

### Packet Structure

```
┌───────┬────────┬──────────┬───────────┬──────────────┬──────────┬─────────┬──────────┐
│ Start │ Length │ Sequence │ System ID │ Component ID │ Msg ID   │ Payload │ Checksum │
│ 0xFD  │ 1 byte │ 1 byte   │ 1 byte    │ 1 byte       │ 3 bytes  │ 0-255   │ 2 bytes  │
└───────┴────────┴──────────┴───────────┴──────────────┴──────────┴─────────┴──────────┘
```

### MAVLink Versions

| Version | Features | Use |
|---------|----------|-----|
| MAVLink 1 | Original, 8-byte header | Legacy systems |
| MAVLink 2 | Message signing, packet trimming, 14-byte header | Current standard |

### Common MAVLink Messages

**Telemetry (Drone sends):**

| Message | Data |
|---------|------|
| HEARTBEAT | System status, flight mode |
| GPS_RAW_INT | Position, satellites, fix type |
| ATTITUDE | Roll, pitch, yaw |
| VFR_HUD | Airspeed, ground speed, altitude |
| BATTERY_STATUS | Voltage, current, remaining |
| SYS_STATUS | CPU load, sensor health, errors |

**Commands (Ground sends):**

| Message | Function |
|---------|----------|
| COMMAND_LONG | Arm/disarm, takeoff, land, mode change |
| SET_MODE | Change flight mode |
| MISSION_ITEM | Send waypoint |
| PARAM_SET | Change parameter |

---

## Onboard Bus Protocols

### UART (Serial)

Universal Asynchronous Receiver/Transmitter - the most common protocol.

```
Device A                    Device B
   TX ─────────────────────── RX
   RX ─────────────────────── TX
  GND ─────────────────────── GND
```

| Characteristic | Value |
|----------------|-------|
| Wires | 2 (TX, RX) + ground |
| Connection | Point-to-point |
| Speed | 9600 to 921600 baud |
| Distance | Short (< 1m typically) |
| Common uses | GPS, telemetry, ESC config |

**Common Baud Rates:**
- 9600 - Slow, reliable
- 57600 - Telemetry radios
- 115200 - GPS, common default
- 921600 - High-speed companion computer

**Important:** TX connects to RX (crossover).

### I2C (Inter-Integrated Circuit)

Multi-device bus using addresses.

```
        ┌─────────────────────────────────────┐
        │               I2C Bus               │
  SDA ──┼──────┬──────┬──────┬──────┬────────│
  SCL ──┼──────┼──────┼──────┼──────┼────────│
        │      │      │      │      │        │
     [Master] [0x68] [0x1E] [0x77] [0x50]    │
       (FC)   (IMU) (Comp) (Baro) (EEPROM)   │
        └─────────────────────────────────────┘
```

| Characteristic | Value |
|----------------|-------|
| Wires | 2 (SDA, SCL) + ground |
| Connection | Multi-device, addressed |
| Speed | 100-400 kbps typical |
| Devices | Up to 127 on one bus |
| Common uses | IMU, compass, barometer, displays |

**I2C Addresses (Examples):**
- 0x68 - MPU6050 IMU
- 0x1E - HMC5883L compass
- 0x77 - BMP280 barometer
- 0x76 - MS5611 barometer

### SPI (Serial Peripheral Interface)

High-speed, full-duplex communication.

```
Master (FC)              Slave (IMU)
  MOSI ──────────────────── MOSI
  MISO ──────────────────── MISO
   SCK ──────────────────── SCK
   CS1 ──────────────────── CS
   
               Slave (Flash)
   CS2 ──────────────────── CS
```

| Characteristic | Value |
|----------------|-------|
| Wires | 4+ (MOSI, MISO, SCK, CS) |
| Connection | Master + multiple slaves |
| Speed | 10-100 Mbps |
| Common uses | High-speed IMU, SD cards, flash |

**Advantages:** Very fast, full-duplex
**Disadvantages:** More wires, one CS per device

### CAN Bus

Robust, long-distance protocol for professional systems.

```
┌──────┐     ┌──────┐     ┌──────┐     ┌──────┐
│ FC   │     │ ESC1 │     │ ESC2 │     │ GPS  │
│      │     │      │     │      │     │      │
└──┬───┘     └──┬───┘     └──┬───┘     └──┬───┘
   │            │            │            │
───┴────────────┴────────────┴────────────┴─── CAN-H
───┴────────────┴────────────┴────────────┴─── CAN-L
```

| Characteristic | Value |
|----------------|-------|
| Wires | 2 (CAN-H, CAN-L) differential |
| Connection | Multi-device bus |
| Speed | Up to 1 Mbps |
| Distance | Up to 40 meters |
| Common uses | Professional/commercial drones |

**DroneCAN (UAVCAN):**
- Built on CAN bus
- Plug-and-play device discovery
- Firmware updates over bus
- Redundant bus support
- Aerospace-grade reliability

---

## RC Control Protocols

### PWM (Pulse Width Modulation)

The oldest and simplest RC protocol.

```
│←── 20ms (50Hz) ──→│

┌───┐               ┌───┐
│   │               │   │
│   │               │   │
┘   └───────────────┘   └────

│←1ms→│              │←2ms→│
 (Min)                (Max)
```

| Characteristic | Value |
|----------------|-------|
| Signal | 1-2ms pulse, 50Hz |
| Channels | 1 wire per channel |
| Latency | 20ms |
| Use | Learning, old equipment |

### PPM (Pulse Position Modulation)

Multiple channels on one wire.

| Characteristic | Value |
|----------------|-------|
| Signal | Sequential pulses |
| Channels | 8-12 on 1 wire |
| Latency | 20-27ms |
| Use | Cleaner wiring than PWM |

### S.BUS (Serial Bus)

Digital protocol from Futaba.

| Characteristic | Value |
|----------------|-------|
| Signal | Digital packets, inverted UART |
| Channels | 16 + 2 digital |
| Speed | 100 kbps |
| Latency | 7-14ms |
| Use | Modern FPV standard |

**Note:** S.BUS is inverted UART - may need hardware inverter on some FCs.

### CRSF (Crossfire)

TBS Crossfire protocol, also used by ExpressLRS.

| Characteristic | Value |
|----------------|-------|
| Signal | Digital, bidirectional |
| Channels | 16 |
| Speed | 416 kbps |
| Latency | 4-10ms |
| Range | 2-50+ km |
| Telemetry | Full bidirectional |

### ExpressLRS (ELRS)

Open-source long-range protocol.

| Characteristic | Value |
|----------------|-------|
| Signal | Digital, bidirectional |
| Channels | 16 |
| Latency | 2-5ms |
| Range | Up to 100+ km |
| Telemetry | Full bidirectional |
| Cost | Very affordable |

### Protocol Comparison

| Protocol | Channels | Latency | Range | Telemetry |
|----------|----------|---------|-------|-----------|
| PWM | 8 | 20ms | 1-2km | No |
| PPM | 8-12 | 20-27ms | 1-2km | No |
| S.BUS | 16 | 7-14ms | 1-2km | No |
| iBUS | 14 | 7-10ms | 1-2km | Basic |
| F.Port | 24 | 9-12ms | 1-2km | Yes |
| CRSF | 16 | 4-10ms | 50km+ | Full |
| ELRS | 16 | 2-5ms | 100km+ | Full |

---

## Frequency Bands

### Overview

| Band | Frequency | Range | Penetration | Bandwidth | Use |
|------|-----------|-------|-------------|-----------|-----|
| 900MHz | 868/915MHz | 10-50km+ | Excellent | Low | Long-range RC |
| 2.4GHz | 2400-2483MHz | 1-5km | Moderate | High | Standard RC |
| 5.8GHz | 5650-5950MHz | 0.5-2km | Poor | Very high | FPV video |

### Why Multiple Bands?

**900MHz:** Best range and penetration, but limited bandwidth.
- Use for: RC control at extreme range

**2.4GHz:** Good balance of range and bandwidth.
- Use for: Standard RC control

**5.8GHz:** Highest bandwidth, worst range/penetration.
- Use for: FPV video (needs lots of data)

### Interference Considerations

Keep video (5.8GHz) antenna away from RC (2.4GHz) antenna to prevent interference.

```
Good:                       Bad:
                           
┌─────────────────┐        ┌─────────────────┐
│    [RC 2.4]     │        │    [RC 2.4]     │
│       │         │        │     │ │         │
│       │         │        │     │ │         │
│                 │        │     [FPV 5.8]   │
│    [FPV 5.8]    │        │                 │
└─────────────────┘        └─────────────────┘
   (Separated)               (Too close)
```

---

## Security Features

### Binding

Transmitter and receiver pair exclusively.

```
1. Put receiver in bind mode (button/power sequence)
2. Put transmitter in bind mode
3. They exchange unique ID
4. Only this pair can communicate
```

### Frequency Hopping (FHSS)

Rapidly switches frequencies to avoid interference and eavesdropping.

```
Time →
┌────┬────┬────┬────┬────┬────┐
│ F1 │ F7 │ F3 │ F9 │ F2 │ F5 │  (Pseudo-random pattern)
└────┴────┴────┴────┴────┴────┘
```

### Encryption

Some protocols offer encryption:
- 16-128 bit keys
- Prevents eavesdropping
- Prevents command injection

### Failsafe

What happens when signal is lost:

| Option | Behavior |
|--------|----------|
| **Hold** | Maintain last known position |
| **RTL** | Return to launch point |
| **Land** | Land immediately |
| **Drop** | Cut motors (dangerous) |
| **Custom** | User-defined outputs |

---

## Wiring Best Practices

### UART Connections

**Remember:** TX→RX, RX→TX (crossover)

```
Device A          Device B
  TX ──────────────→ RX
  RX ←────────────── TX
 GND ←────────────→ GND
```

### I2C Connections

**Pull-up resistors** usually built into devices, but sometimes needed.

```
VCC ───┬───────┬───────┬───
       R       R       │
       │       │       │
SDA ───┴───┬───┴───┬───┴───
           │       │
SCL ───────┴───────┴───────
```

### CAN Bus Termination

**120Ω termination** at each end of bus.

```
┌──────┐                              ┌──────┐
│ 120Ω │──────────────────────────────│ 120Ω │
└──────┘                              └──────┘
```

### Signal Integrity

- Keep wires short
- Twist signal pairs
- Separate power and signal wires
- Use shielded cables for telemetry
- Ground all devices to common ground

---

## Key Takeaways

1. **MAVLink** = Standard drone communication protocol
2. **UART** = Simple, point-to-point (TX→RX crossover!)
3. **I2C** = Multi-device bus with addresses
4. **SPI** = High-speed, more wires
5. **CAN** = Professional, reliable, long distance
6. **ELRS/CRSF** = Best modern RC protocols
7. **Always configure failsafe** before flying

---

[← Previous: Software](05-software-architecture.md) | [Back to Index](../README.md) | [Next: Assembly →](07-assembly-guide.md)
