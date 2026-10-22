# Guide 14: Custom PCB Design for Drones

[← Previous: FPV Systems](13-fpv-systems.md) | [Back to Index](../README.md) | [Next: Advanced Navigation →](15-advanced-navigation.md)

---

## Why Design Your Own PCB?

Off-the-shelf flight controllers are great — until they're not. You hit limits when you need:
- A specific sensor combination
- Custom form factor for your frame
- Integration of companion processors
- Power systems tuned to your battery/motor combo
- Features no existing board offers

Custom PCBs take you from "drone builder" to "drone engineer."

---

## PCB Design Workflow

```
┌──────────────────────────────────────────────────┐
│              PCB DESIGN PIPELINE                  │
│                                                  │
│  Requirements ──→ Schematic ──→ Component        │
│                                  Selection       │
│       │                             │            │
│       ↓                             ↓            │
│  Block Diagram      Schematic Capture            │
│                         │                        │
│                         ↓                        │
│                    PCB Layout                     │
│                    (placement + routing)          │
│                         │                        │
│                         ↓                        │
│               Design Rule Check (DRC)            │
│                         │                        │
│                         ↓                        │
│              Generate Gerbers + BOM              │
│                         │                        │
│                         ↓                        │
│              Fabrication (JLCPCB, PCBWay)        │
│                         │                        │
│                         ↓                        │
│              Assembly + Testing                  │
│                         │                        │
│                         ↓                        │
│              Firmware + Validation               │
└──────────────────────────────────────────────────┘
```

---

## Tools

| Tool | Cost | Best For |
|------|------|----------|
| **EasyEDA (Pro)** | Free | Beginners, direct JLCPCB integration |
| **KiCad** | Free (open source) | Professional, full control |
| **Altium Designer** | Expensive | Industry standard, team collaboration |
| **Fusion 360 Electronics** | Free (hobby) | 3D integration with mechanical design |

### EasyEDA + JLCPCB (Recommended Starter Path)

Why this combo works for drone PCBs:
- Free schematic + layout editor
- Integrated LCSC component library (100K+ parts)
- One-click BOM export to JLCPCB
- SMT assembly service (they solder for you)
- 5 PCBs from ~$2 + shipping
- 2-layer boards ship in 3-5 days

---

## Flight Controller Architecture

### Minimum Viable FC

Every flight controller needs these core blocks:

```
┌──────────────────────────────────────────────────┐
│              FLIGHT CONTROLLER BLOCKS             │
│                                                  │
│  ┌─────────┐   ┌─────────┐   ┌──────────┐      │
│  │  MCU    │   │  IMU    │   │  Power   │      │
│  │(STM32)  │←──│(Gyro+   │   │ Regulator│      │
│  │         │   │ Accel)  │   │ (5V/3.3V)│      │
│  └────┬────┘   └─────────┘   └──────────┘      │
│       │                                          │
│  ┌────┴────────────────────────────┐             │
│  │          UARTs / Interfaces     │             │
│  ├──────┬──────┬──────┬───────────┤             │
│  │ ESC  │ GPS  │ RC   │ USB      │             │
│  │(DSHOT)│(UART)│(SBUS/│(Config)  │             │
│  │      │      │CRSF) │          │             │
│  └──────┴──────┴──────┴───────────┘             │
└──────────────────────────────────────────────────┘
```

### MCU Selection

| MCU | Clock | Flash | RAM | UARTs | Timers | Best For |
|-----|-------|-------|-----|-------|--------|----------|
| **STM32F405RGT6** | 168MHz | 1MB | 192KB | 6 | 14 | Betaflight standard, proven |
| **STM32F722RET6** | 216MHz | 512KB | 256KB | 4-5 | 12 | Fast, good value |
| **STM32H743VIT6** | 480MHz | 2MB | 1MB | 8 | 15 | ArduPilot, maximum features |
| **STM32H750VBT6** | 480MHz | 128KB* | 1MB | 8 | 15 | Betaflight (external flash) |
| **ESP32-S3** | 240MHz | 8-16MB | 512KB | 3 | 4 | WiFi/BLE, companion processor |

*H750 uses external flash for firmware — common in modern Betaflight boards.

**Recommendation:** STM32F405 for your first FC. It's the most widely supported in Betaflight with extensive community resources.

### IMU Selection

| IMU | Gyro + Accel | Interface | Gyro Rate | Status |
|-----|-------------|-----------|-----------|--------|
| **ICM-42688-P** | Yes | SPI | 32kHz | Current best, recommended |
| **BMI270** | Yes | SPI | 6.4kHz | Good budget option |
| **MPU-6000** | Yes | SPI | 8kHz | Legacy, still works |
| **MPU-6050** | Yes | I2C only | 1kHz | End-of-life, avoid in new designs |
| **ICM-42605** | Yes | SPI/I2C | 32kHz | Good alternative |

**Recommendation:** ICM-42688-P. Best noise performance, highest sample rate, well-supported in Betaflight and ArduPilot. Use SPI interface, not I2C.

---

## Schematic Design Essentials

### Power Supply Design

Every FC needs regulated power from the battery:

```
Battery (VBAT)         5V Rail               3.3V Rail
14.8-25.2V ──→ [Buck Converter] ──→ [LDO] ──→ 3.3V
  (4S-6S)         5V @ 2A+           3.3V @ 500mA+
                    │                    │
                    ├── USB VBUS         ├── MCU
                    ├── GPS              ├── IMU
                    ├── RC Receiver      ├── Barometer
                    └── Peripherals      └── Flash
```

| Component | Recommended | Why |
|-----------|------------|-----|
| 5V Buck | MP2359 / MP1584 | Efficient, handles wide input |
| 3.3V LDO | AMS1117-3.3 | Simple, cheap, sufficient |
| Input capacitors | 100µF + 10µF electrolytic | Absorb battery voltage spikes |
| Decoupling caps | 100nF on every power pin | Noise filtering |

**Critical mistakes to avoid:**
- Using an LDO for 5V directly from battery — massive heat dissipation
- Skipping bulk capacitors on VBAT input — voltage spikes kill ICs
- Forgetting decoupling caps on MCU power pins — random resets

### USB-C Interface

Modern FCs use USB-C for configuration:

```
USB-C Connector
    │
    ├── CC1, CC2: 5.1kΩ pulldowns (identifies as UFP/device)
    │
    ├── D+, D- ──→ [ESD Protection IC] ──→ MCU USB pins
    │               (USBLC6-2SC6)
    │
    ├── VBUS ──→ Schottky diode ──→ 5V rail
    │            (prevents backfeed from battery)
    │
    └── GND
```

**Do not skip ESD protection on D+/D- lines.** USB ports see ESD events constantly — a single zap can kill your MCU's USB peripheral. USBLC6-2SC6 is cheap and effective.

### Motor Outputs (DSHOT)

```
MCU Timer Pin ──→ [Pad] ──→ ESC Signal Input

DSHOT Protocol:
├── DSHOT150  — 150 kbit/s (slower, more noise tolerant)
├── DSHOT300  — 300 kbit/s (common default)
├── DSHOT600  — 600 kbit/s (recommended for most builds)
└── DSHOT1200 — 1200 kbit/s (needs clean signal path)
```

Each motor output needs its own timer channel. The STM32F405 has plenty — just make sure you assign motor outputs to different timer peripherals or channels on the same timer.

### UART Allocation

Plan your UARTs before laying out the schematic:

| UART | Assignment | Baud Rate |
|------|-----------|-----------|
| UART1 | GPS (RX + TX) | 115200 |
| UART2 | RC Receiver (SBUS/CRSF) | 420000 (CRSF) |
| UART3 | Telemetry / Smart Audio | 9600-57600 |
| UART4 | Spare / Companion Computer | 115200-921600 |
| UART5 | ESC Telemetry (half-duplex) | 115200 |
| UART6 | Spare / Bluetooth | 115200 |

**Tip:** Always expose at least one spare UART on pads. You'll thank yourself later.

### I2C Bus

Shared bus for slower sensors:

```
3.3V ──[4.7kΩ]──┬── SDA ──┬── Barometer (BMP388)
                 │         ├── Compass (QMC5883L)
3.3V ──[4.7kΩ]──┬── SCL ──┤── OLED Display (optional)
                           └── External sensor
```

| Parameter | Value |
|-----------|-------|
| Pull-up resistors | 2.2kΩ – 4.7kΩ (depends on bus capacitance) |
| Speed | 400kHz (Fast Mode) |
| Max devices | ~8 practical (address limit) |

**Common I2C addresses:**

| Device | Address |
|--------|---------|
| BMP388 (baro) | 0x76 or 0x77 |
| BMP280 (baro) | 0x76 or 0x77 |
| QMC5883L (compass) | 0x0D |
| HMC5883L (compass) | 0x1E |
| INA226 (power monitor) | 0x40-0x4F |

### SPI Bus (IMU Connection)

The IMU should always be on SPI, not I2C:

```
MCU                     IMU (ICM-42688-P)
├── SPI_CLK  ──────────→ SCLK
├── SPI_MOSI ──────────→ SDI
├── SPI_MISO ←────────── SDO
├── CS_IMU   ──────────→ CS (active low)
└── EXTI_PIN ←────────── INT1 (data ready interrupt)
```

| Parameter | Value |
|-----------|-------|
| SPI Speed | Up to 24MHz for ICM-42688-P |
| Mode | SPI Mode 0 or Mode 3 |
| CS idle | HIGH (active low) |
| INT pin | Configure with pull-up, active LOW |

**Layout critical:** Keep SPI traces to IMU short (< 20mm) and away from motor output traces and switching power supply.

---

## PCB Layout Guidelines

### Layer Stackup

| Layers | Use Case |
|--------|----------|
| **2-layer** | Simple FCs, cost-effective, JLCPCB cheapest tier |
| **4-layer** | Better EMI, dedicated ground plane, recommended for H7 MCUs |

For a 2-layer board, use top layer primarily for signals and bottom layer as ground plane with short power traces.

### Form Factors

| Standard | Hole Spacing | Common On |
|----------|-------------|-----------|
| **20×20mm** | 16×16mm M2 | Micro quads, Tiny Whoop AIO |
| **25.5×25.5mm** | 25.5mm M2 | Nano/micro quads (your Whoop-to-3" builds) |
| **30.5×30.5mm** | 30.5mm M3 | Standard 5" quads |
| **Custom** | Varies | Frames with unique mounting |

### IMU Placement Rules

The IMU is the most layout-sensitive component:

```
DO:
✅ Place IMU at center of board (center of rotation)
✅ Keep solid ground plane under IMU
✅ Use short SPI traces (< 20mm)
✅ Add 100nF + 10nF decoupling caps close to IMU power pins
✅ Consider soft-mounting the FC (rubber grommets)

DON'T:
❌ Route high-current traces under IMU
❌ Place IMU near switching regulator
❌ Put vias under the IMU pad
❌ Share IMU SPI bus with other devices
```

### Power Trace Width

Calculate trace width for current-carrying paths:

| Current | Trace Width (1oz copper, 10°C rise) |
|---------|-------------------------------------|
| 1A | 0.25mm (10mil) |
| 3A | 0.75mm (30mil) |
| 5A | 1.25mm (50mil) |
| 10A | 2.5mm (100mil) |
| 20A+ | Use copper pour / polygon fill |

**For battery input and ESC power pads:** Use copper fills, not traces. These carry full motor current.

### Decoupling Capacitor Placement

```
Rule: Every IC power pin gets a 100nF cap within 3mm

MCU:
├── VDDA: 100nF + 1µF (analog power, keep clean)
├── VDD (each pin): 100nF
└── VCAP: 2.2µF (internal regulator, exact value per datasheet)

IMU:
├── VDD: 100nF + 10nF
└── VDDIO: 100nF

Buck converter:
├── Input: 10µF ceramic + 100µF electrolytic
└── Output: 22µF ceramic (X5R/X7R)
```

---

## Common Drone PCB Mistakes

| Mistake | Consequence | Fix |
|---------|-------------|-----|
| No ESD on USB D+/D- | USB peripheral dies from static | Add USBLC6-2SC6 |
| WS2812B LED on 3.3V | Unreliable or dim (spec is 5V) | Power from 5V rail |
| Missing flyback diodes on motor pads | Voltage spikes damage FC | Add TVS or flyback diode |
| MPU-6050 in new design | End-of-life, I2C only, noisy | Use ICM-42688-P |
| No reverse polarity protection | Plugging battery backwards kills everything | Add P-MOSFET or ideal diode IC |
| Wrong pull resistor direction | INT pin floats, I2C doesn't work | Check datasheet for pull-up vs pull-down |
| Barometer exposed to light/airflow | Altitude readings jump around | Cover with foam, shield from props |
| SPI traces too long | Signal integrity issues at high speed | Keep under 20mm |
| No test points | Can't debug after assembly | Add test pads for VBAT, 5V, 3.3V, SWD |

---

## Fabrication & Assembly

### JLCPCB Order Checklist

| Parameter | Recommended Value |
|-----------|-------------------|
| **Layers** | 2 or 4 |
| **PCB thickness** | 1.6mm (standard) or 1.0mm (lightweight) |
| **Copper weight** | 1oz (signal) or 2oz (high current) |
| **Surface finish** | HASL (cheap) or ENIG (flat pads, better for fine-pitch) |
| **Solder mask** | Any color (black is popular for FCs) |
| **Min trace/space** | 0.15mm/0.15mm (6mil) |
| **Min via** | 0.3mm drill, 0.6mm diameter |

### BOM Optimization for JLCPCB Assembly

| Tip | Why |
|-----|-----|
| Use LCSC "Basic" parts when possible | No setup fee ($3 per extended part) |
| Check stock before designing | Out-of-stock parts delay assembly |
| Use standard package sizes (0402, 0603) | Better machine placement accuracy |
| Minimize unique part count | Fewer parts = lower assembly cost |
| Group resistor values where possible | Fewer BOM lines |

### Typical FC Cost Breakdown (JLCPCB, qty 5)

| Item | Cost |
|------|------|
| PCB fabrication (5 pcs, 2-layer) | $2-5 |
| SMT assembly (per board) | $8-15 |
| Components (BOM) | $10-20 per board |
| Shipping (to India) | $15-25 |
| **Total per board** | **$15-30** |

Compare this to buying a commercial FC at $40-80 — custom boards are surprisingly affordable, especially at slightly higher quantities.

---

## Testing Your Board

### Power-On Sequence

```
Step 1: Visual inspection (solder bridges, missing parts)
        ↓
Step 2: Continuity test (no shorts on VBAT, 5V, 3.3V, GND)
        ↓
Step 3: Power via USB only (check 3.3V rail)
        ↓
Step 4: Flash bootloader via SWD (ST-Link)
        ↓
Step 5: Flash Betaflight/ArduPilot firmware
        ↓
Step 6: Connect via USB, verify sensors in configurator
        ↓
Step 7: Test each UART, motor output, peripheral
        ↓
Step 8: Power from battery, verify buck converter output
        ↓
Step 9: Full integration test on drone frame
```

### Essential Test Equipment

| Tool | Purpose | Cost |
|------|---------|------|
| **Multimeter** | Voltage, continuity | ₹500-₹2,000 |
| **ST-Link V2** | SWD debugging + flashing | ₹300-₹800 |
| **Logic analyzer** | UART/SPI/I2C debugging | ₹1,500-₹5,000 |
| **USB microscope** | Solder joint inspection | ₹2,000-₹5,000 |
| **Bench power supply** | Controlled voltage testing | ₹3,000-₹8,000 |
| **Hot air station** | Rework SMD components | ₹3,000-₹6,000 |

---

## Dual-MCU Architecture

For advanced builds, consider separating flight control and communication:

```
┌──────────────────────────────────────────────────┐
│               DUAL-MCU ARCHITECTURE              │
│                                                  │
│  ┌─────────────┐         ┌─────────────┐        │
│  │  Flight MCU │  UART   │  Comms MCU  │        │
│  │ (STM32F405) │←───────→│  (ESP32-S3) │        │
│  │             │         │             │        │
│  │ • Betaflight│         │ • WiFi/BLE  │        │
│  │ • IMU       │         │ • MQTT      │        │
│  │ • Motor ctrl│         │ • Telemetry │        │
│  │ • PID loop  │         │ • OTA update│        │
│  │ • Baro      │         │ • Vision AI │        │
│  └─────────────┘         └─────────────┘        │
│                                                  │
│  Why: Flight MCU runs deterministic control loop │
│       Comms MCU handles non-realtime networking  │
│       Crash in comms MCU doesn't affect flight   │
└──────────────────────────────────────────────────┘
```

This architecture separates safety-critical flight control from networking and AI — a crash or hang in the ESP32 won't affect the PID loop running on the STM32.

---

## Design Resources

| Resource | What It Offers |
|----------|---------------|
| **Betaflight Unified Targets** | Pin mappings for existing FCs (use as reference) |
| **STM32CubeMX** | MCU pin configuration, clock tree |
| **JLCPCB Parts Library** | Search LCSC stock with EasyEDA integration |
| **Betaflight GitHub** | Target configs for your custom board |
| **Oscar Liang Blog** | FC design teardowns and reviews |

---

[← Previous: FPV Systems](13-fpv-systems.md) | [Back to Index](../README.md) | [Next: Advanced Navigation →](15-advanced-navigation.md)
