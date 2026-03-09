# Guide 13: FPV Systems & Video

[← Previous: Regulations & Legal](12-regulations-legal.md) | [Back to Index](../README.md) | [Next: Custom PCB Design →](14-custom-pcb-design.md)

---

## What Is FPV?

First Person View — you see what the drone sees, in real time, through goggles or a screen. It's the difference between watching a drone from below and being in the cockpit.

```
┌──────────────────────────────────────────────────┐
│                   FPV SYSTEM                      │
│                                                  │
│  Camera ──→ VTx ──→ ))) Radio ))) ──→ VRx ──→ Goggles │
│  (on drone)  (on drone)             (on ground)  (your eyes) │
│                                                  │
│  Camera captures → VTx transmits → VRx receives → You see │
│  ~20ms total latency (analog) / 30-50ms (digital)│
└──────────────────────────────────────────────────┘
```

---

## Analog vs Digital FPV

This is the biggest decision in FPV. Both have strong use cases.

### Quick Comparison

| Aspect | Analog | Digital |
|--------|--------|---------|
| **Latency** | 10-20ms | 25-50ms |
| **Image quality** | 480p-720p (grainy) | 720p-1080p+ (crisp) |
| **Range** | 1-5km typical | 5-15km+ |
| **Cost (full system)** | ₹5,000–₹10,000 | ₹20,000–₹60,000 |
| **Weight** | Very light (10-20g VTx) | Heavier (30-60g) |
| **Breakup behavior** | Gradual static → snow | Freezes → black screen |
| **Best for** | Racing, tiny builds, learning | Cinematic, long range, freestyle |
| **Compatibility** | Universal (any analog VTx + VRx) | Locked to ecosystem |

### Analog FPV

The original. A simple FM video transmitter sends signal to any compatible receiver.

**How it works:**
```
Camera (NTSC/PAL) → VTx (5.8GHz) → Antenna → ... → Antenna → VRx → Goggles/Monitor
```

**Pros:**
- Dead simple, plug and play
- Ultra-low latency (10-20ms)
- Cheap to get started
- Lightweight — perfect for sub-250g builds
- Universal compatibility between brands

**Cons:**
- Lower image quality (think old TV)
- Multipath interference indoors
- Signal degrades gradually with range

**Typical analog components:**

| Component | Example | Weight | Price (India) |
|-----------|---------|--------|---------------|
| Camera | Foxeer Razer Mini | 4g | ₹1,500 |
| VTx | Rush Tiny Tank | 3g | ₹1,800 |
| VTx Antenna | Foxeer Lollipop | 5g | ₹600 |
| Goggles | Eachine EV800D | 230g | ₹5,000 |
| Goggle Antenna | Foxeer Patch + Omni | 15g | ₹1,200 |

### Digital FPV Ecosystems

| System | Resolution | Latency | Range | Entry Cost |
|--------|-----------|---------|-------|------------|
| **DJI O3** | 1080p/100fps | 28ms | 13km | ₹25,000+ |
| **DJI O4** | 1080p/120fps | 22ms | 20km | ₹30,000+ |
| **HDZero** | 1080p/90fps | 25ms | 5km+ | ₹20,000+ |
| **Walksnail Avatar** | 1080p/100fps | 28ms | 10km+ | ₹18,000+ |

**DJI** — Best image, best range, but locked ecosystem. You buy DJI goggles, DJI VTx, DJI camera.

**HDZero** — Open ecosystem, lowest digital latency, great for racing. Works with any HDZero-compatible goggles.

**Walksnail** — Good middle ground. Lower cost than DJI, decent image, decent latency.

---

## FPV Camera Deep Dive

### Key Specifications

| Spec | What It Means | Good Values |
|------|---------------|-------------|
| **Sensor size** | Larger = better low light | 1/1.8", 1/2" |
| **TVL** | Horizontal resolution (analog) | 1200+ TVL |
| **FOV** | Field of view | 150°-170° (wide) |
| **Minimum illumination** | Low-light performance | 0.001 lux |
| **WDR / D-WDR** | Handles bright + dark areas | Yes (essential) |
| **Aspect ratio** | 4:3 (more vertical) or 16:9 | 4:3 for racing |
| **Voltage input** | Operating range | 3.3V-5.5V typical |

### Camera Mounting

| Size | Dimensions | Common On |
|------|-----------|-----------|
| **Full** | 28×28mm | 5" quads, large builds |
| **Mini** | 21×21mm | 3"-5" quads |
| **Micro** | 19×19mm | 3" quads, sub-250g |
| **Nano** | 14×14mm | Tiny Whoop, micro builds |

### Camera Tilt

```
Side View:

       0° (flat) ──── Good for slow/cinematic flying
      /
     /  15° ──── General purpose
    /
   /   25° ──── Freestyle
  /
 /    35° ──── Fast freestyle
/
      45°+ ──── Racing (full send)

Higher tilt = faster forward flight = more sky in view while cruising
```

---

## Video Transmitter (VTx)

### Power Levels

| Power | Range (Open Field) | Use Case | Legal Status (India) |
|-------|-------------------|----------|---------------------|
| **25mW** | 200-500m | Indoor, proximity | Generally acceptable |
| **100mW** | 500m-1km | Park flying | Commonly used |
| **200mW** | 1-2km | General FPV | Check local limits |
| **400mW** | 2-4km | Long range | Higher power, more heat |
| **600mW** | 3-5km | Long range analog | Max for most VTx |
| **1W+** | 5km+ | Extreme range | Likely needs authorization |

**Rule of thumb:** Use the minimum power that gives you clean signal. Higher power = more heat = shorter VTx life.

### Frequency Bands (5.8GHz)

| Band | Channels | Notes |
|------|----------|-------|
| **A** (Boscam A) | 8 | Standard |
| **B** (Boscam B) | 8 | Standard |
| **E** (Lumenier) | 8 | Less common |
| **F** (FatShark/NexWave) | 8 | Popular for racing |
| **R** (Raceband) | 8 | Designed for minimal interference between pilots |

**For multi-pilot flying:** Use Raceband channels spaced at least 2 apart (R1, R3, R5, R7) to avoid video interference.

### Smart Audio / Tramp

Most modern VTx support software-controlled settings through the flight controller:

```
FC ──UART──→ VTx (Smart Audio / Tramp protocol)
                 │
                 ├── Change channel from OSD
                 ├── Change power from OSD
                 ├── Pit mode (ultra-low power for bench)
                 └── No need to press physical buttons
```

Configure in Betaflight:
```
Ports tab → Choose UART → Peripherals → TBS SmartAudio or IRC Tramp
```

---

## FPV Goggles

### Types

| Type | Pros | Cons | Price Range |
|------|------|------|-------------|
| **Box goggles** | Cheap, easy to use with glasses | Heavy, bulky | ₹3,000–₹7,000 |
| **Compact goggles** | Lightweight, good FOV | Need diopter inserts | ₹8,000–₹50,000 |
| **Monitor** | Shareable view, any angle | Sun glare, not immersive | ₹3,000–₹10,000 |

### Key Specs

| Spec | What It Means | Good Values |
|------|---------------|-------------|
| **Resolution** | Display pixels | 1280×960+ per eye |
| **FOV** | Immersiveness | 40°+ diagonal |
| **DVR** | Built-in recording | Yes (essential) |
| **Diversity** | Two receivers, picks best signal | Yes |
| **Module bay** | Swap receiver modules | Useful for upgrades |
| **IPD adjustment** | Inter-pupillary distance | Adjustable preferred |

### Starter Recommendations

| Budget | Goggle | Type | Why |
|--------|--------|------|-----|
| ₹3,000–₹5,000 | Eachine EV800D | Box | Diversity, DVR, cheap |
| ₹8,000–₹12,000 | Skyzone Cobra X | Compact | Good analog, module bay |
| ₹25,000+ | DJI Goggles 3 | Compact | Digital, best image, OLED |

---

## Antenna Fundamentals

Antennas matter more than VTx power for clean signal. A good antenna on 200mW beats a bad antenna on 600mW.

### Types

| Antenna | Pattern | Best For |
|---------|---------|----------|
| **Linear dipole** | Omni (weak) | Comes stock, replace ASAP |
| **Circular polarized omni** (RHCP/LHCP) | Omni (good) | General flying, all directions |
| **Patch** | Directional | Long range in one direction |
| **Helical** | Directional | Maximum range, narrow beam |
| **Pagoda/Lollipop** | Omni (good) | Compact, durable |

### Polarization

```
Transmitter and receiver antennas MUST match polarization:

  RHCP VTx antenna + RHCP VRx antenna = ✅ Good signal
  LHCP VTx antenna + LHCP VRx antenna = ✅ Good signal
  RHCP VTx antenna + LHCP VRx antenna = ❌ ~30dB loss (terrible)
```

### Goggle Antenna Setup (Diversity)

Best combo for most flying:
```
Left module:  Omni antenna (RHCP) ── covers all directions
Right module: Patch antenna (RHCP) ── boosts forward range

The diversity receiver auto-switches to whichever has better signal.
```

---

## On-Screen Display (OSD)

OSD overlays flight data on your FPV feed. Essential for situational awareness.

### Common OSD Elements

| Element | Why You Need It |
|---------|-----------------|
| **Battery voltage** | Land before voltage drops |
| **mAh consumed** | Track battery usage precisely |
| **Flight time** | Know how long you've been flying |
| **RSSI** | RC link signal strength |
| **Link quality** | ELRS LQ percentage |
| **GPS coordinates** | Find drone if crashed |
| **Satellite count** | GPS health |
| **Altitude** | Height awareness |
| **Speed** | Ground speed |
| **Warnings** | Low battery, failsafe, etc. |

### Setting Up OSD in Betaflight

```
1. OSD Tab → Enable elements by toggling
2. Drag elements to desired position on preview
3. Set warning thresholds:
   - Battery warn: 3.5V per cell
   - Battery critical: 3.3V per cell
   - RSSI warn: 40
4. Save and reboot
```

### OSD Chips

| Chip | Compatibility | Notes |
|------|--------------|-------|
| **AT7456E** | Analog | Most common, PAL/NTSC |
| **Integrated** | DJI/HDZero/Walksnail | Digital systems handle OSD differently |

For digital systems, OSD data is sent as metadata and rendered by the goggles — no physical OSD chip needed on some setups.

---

## Complete FPV Build Checklist

### Budget Analog Setup (₹8,000–₹12,000)

```
Drone Side:
├── Camera: Foxeer Razer Mini (₹1,500)
├── VTx: Rush Tiny Tank (₹1,800)
├── VTx Antenna: Foxeer Lollipop 3 RHCP (₹600)
└── OSD: Usually built into FC

Ground Side:
├── Goggles: Eachine EV800D (₹5,000)
└── Antenna: Stock (upgrade later to patch + omni)

Total: ~₹9,000
```

### Mid-Range Digital Setup (₹25,000–₹35,000)

```
Drone Side:
├── DJI O3 Air Unit (₹15,000)
└── DJI Antenna (included)

Ground Side:
├── DJI Goggles 2/3 (₹20,000+)
└── Antenna (included)

Total: ~₹35,000
```

---

## FPV Flight Tips

### Getting Started

| Phase | Tip |
|-------|-----|
| **Simulator first** | Fly Liftoff, Velocidrone, or DRL Sim before real flying |
| **Start in Angle mode** | Auto-levels the drone when sticks are centered |
| **Open field** | No obstacles, no people |
| **Low rates** | Reduce max rotation speed while learning |
| **Hover first** | Just hover at 2m for 5 battery packs |
| **Then circuits** | Figure-8s, slow turns |
| **Then Acro mode** | Full manual — the real FPV experience |

### Common Mistakes

| Mistake | Fix |
|---------|-----|
| Flying Acro on day one | Use Angle mode until comfortable |
| Wrong antenna polarization | Match RHCP/LHCP on both ends |
| VTx power too high on bench | Use pit mode or 25mW indoors |
| No DVR recording | Always record — helps find crashed drones |
| Flying without spotter | Have someone watching for obstacles/people |

---

## Video Recording

### FPV DVR vs HD Camera

| Recording | Quality | Purpose |
|-----------|---------|---------|
| **Goggle DVR** | Low (720p analog, compressed) | Flight review, finding crashes |
| **HD camera on drone** | 4K/60fps | Cinematic footage |

For cinematic FPV, mount a GoPro or similar action camera separately from the FPV camera. The FPV camera is for flying; the action camera is for the final video.

### Popular HD Cameras for Drones

| Camera | Weight | Resolution | Stabilization |
|--------|--------|-----------|---------------|
| GoPro Hero (Bones) | 54g | 5.3K | HyperSmooth |
| Insta360 Go 3S | 39g | 4K | FlowState |
| RunCam Thumb Pro | 16g | 4K | EIS |
| Naked GoPro (mod) | 25-30g | 5.3K | HyperSmooth |

---

[← Previous: Regulations & Legal](12-regulations-legal.md) | [Back to Index](../README.md) | [Next: Custom PCB Design →](14-custom-pcb-design.md)
