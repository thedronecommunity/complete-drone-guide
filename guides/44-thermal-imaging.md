# Guide 44: Thermal Imaging

[← Previous: Night Operations](43-night-operations.md) | [Back to Index](../README.md) | [Next: Building a Drone Business →](45-drone-business.md)

---

## How Thermal Cameras Work

Thermal cameras detect infrared radiation (heat) emitted by all objects above absolute zero. They display temperature differences as a visual image, enabling detection of heat sources invisible to the human eye.

    ┌──────────────────────────────────────────────────┐
    │         THERMAL vs VISIBLE SPECTRUM              │
    │                                                  │
    │  Visible light: 0.4-0.7 μm (what our eyes see) │
    │  Near IR:       0.7-1.4 μm (night vision)      │
    │  SWIR:          1.4-3 μm                        │
    │  MWIR:          3-8 μm (cooled detectors)       │
    │  LWIR:          8-14 μm (most drone cameras) ←  │
    │                                                  │
    │  LWIR is where most objects emit peak radiation  │
    │  at everyday temperatures (0-100°C)              │
    └──────────────────────────────────────────────────┘

---

## Thermal Camera Options

| Camera | Resolution | Temp Range | Weight | Interface | Price |
|--------|-----------|-----------|--------|-----------|-------|
| **FLIR Lepton 2.5** | 80×60 | -10 to 140°C | 1g | SPI | ₹8,000 |
| **FLIR Lepton 3.5** | 160×120 | -10 to 140°C | 1g | SPI | ₹15,000 |
| **FLIR Boson 320** | 320×256 | -40 to 400°C | 30g | USB/analog | ₹50,000 |
| **FLIR Boson 640** | 640×512 | -40 to 400°C | 30g | USB/analog | ₹1,00,000 |
| **Seek Thermal CompactPRO** | 320×240 | -40 to 330°C | 15g | USB-C | ₹25,000 |
| **FLIR Vue Pro R** | 640×512 | -20 to 400°C | 113g | MAVLink | ₹3,00,000 |
| **DJI Zenmuse H20T** | 640×512 | -20 to 150°C | 828g | DJI SDK | ₹5,00,000+ |

---

## Integrating Thermal on Custom Drones

### FLIR Lepton + Raspberry Pi

    # Hardware:
    # FLIR Lepton 3.5 breakout board (PureThermal or GroupGets)
    # → USB to Raspberry Pi

    # Software:
    pip install opencv-python numpy

    import cv2
    import numpy as np

    # Open thermal camera (PureThermal USB)
    cap = cv2.VideoCapture('/dev/video0')
    cap.set(cv2.CAP_PROP_FOURCC, cv2.VideoWriter_fourcc(*'Y16 '))
    cap.set(cv2.CAP_PROP_CONVERT_RGB, 0)

    while True:
        ret, frame = cap.read()
        if ret:
            # Normalize for display
            frame_norm = cv2.normalize(frame, None, 0, 255, cv2.NORM_MINMAX)
            frame_color = cv2.applyColorMap(frame_norm.astype(np.uint8),
                                            cv2.COLORMAP_INFERNO)
            cv2.imshow('Thermal', frame_color)
            if cv2.waitKey(1) == 27:
                break

### Dual Camera Overlay (RGB + Thermal)

    Fuse RGB and thermal for context:

    ┌──────────────────────────────────────┐
    │  RGB Camera                          │
    │  ┌──────────┐                        │
    │  │ Building │  ← Visual context      │
    │  │  ┌────┐  │                        │
    │  │  │HEAT│  │  ← Thermal overlay     │
    │  │  │████│  │     (heat leak)         │
    │  │  └────┘  │                        │
    │  └──────────┘                        │
    │                                      │
    │  Methods:                            │
    │  ├── Picture-in-picture              │
    │  ├── Alpha blend overlay             │
    │  ├── Edge detection overlay          │
    │  └── Split screen                    │
    └──────────────────────────────────────┘

---

## Applications

### Building Inspection

    What to look for:
    ├── Heat leaks (insulation gaps glow in thermal)
    ├── Moisture damage (wet areas are cooler)
    ├── Electrical hotspots (overloaded circuits)
    ├── Roof membrane damage
    ├── HVAC system leaks
    └── Solar panel defects (hot cells = damaged)

### Solar Panel Inspection

    Thermal anomalies indicate defects:
    ├── Hot spot: Single hot cell = bypass diode failure
    ├── Hot string: Row of hot cells = connection issue
    ├── Uniform hot panel: Junction box failure
    └── Temperature difference > 15°C = investigate

### Agriculture

    Crop stress detection:
    ├── Water-stressed crops are warmer (closed stomata)
    ├── Well-watered crops are cooler (transpiration)
    ├── Irrigation leaks show as cool wet areas
    └── Best timing: Pre-dawn or late afternoon

### Search and Rescue

    Detection capabilities:
    ├── Person in open field: Detectable up to 500m+ (640×512)
    ├── Person under tree cover: Detectable with gaps in canopy
    ├── Person in water: Very clear contrast (body heat vs cold water)
    ├── Vehicle (warm engine): Detectable 1km+
    └── Campfire: Detectable 2km+

---

## Thermal Image Interpretation

    Color palettes:
    ├── White-Hot: Hot = white, cold = black (classic)
    ├── Black-Hot: Hot = black, cold = white
    ├── Inferno: Purple → red → yellow → white
    ├── Rainbow: Blue → green → yellow → red
    └── Iron: Black → purple → red → orange → yellow

    Key factors:
    ├── Emissivity: Different materials emit differently
    │   Metal: Low emissivity (looks cooler than actual)
    │   Organic: High emissivity (accurate temperature)
    ├── Reflections: Shiny surfaces reflect other heat sources
    ├── Wind: Convection cools surfaces, affects readings
    └── Sun: Solar loading heats surfaces unevenly

---

[← Previous: Night Operations](43-night-operations.md) | [Back to Index](../README.md) | [Next: Building a Drone Business →](45-drone-business.md)
