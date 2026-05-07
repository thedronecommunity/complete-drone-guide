# Guide 35: Motor & Propeller Selection

[← Previous: Edge Computing & Embedded AI](34-edge-ai.md) | [Back to Index](../README.md) | [Next: Frame Design & Aerodynamics →](36-frame-design.md)

---

## Motor Fundamentals

### Key Specifications

    KV Rating: RPM per volt (unloaded)
    ├── Higher KV = faster spin, smaller props
    └── Lower KV = slower spin, larger props, more efficient

    Stator Size: XXYY format (XX = diameter, YY = height in mm)
    ├── Bigger stator = more torque, more weight, more power
    └── 2207 = 22mm diameter, 7mm tall

    Weight, max thrust, max current, recommended battery

### Motor Size Guide

| Prop Size | Motor Size | KV (4S) | KV (6S) | Use Case |
|-----------|-----------|---------|---------|----------|
| 3" (76mm) | 1404-1507 | 3800-4500 | 2500-3000 | Cinewhoop, indoor |
| 5" (127mm) | 2205-2207 | 2300-2600 | 1700-1950 | Freestyle, racing |
| 5" (127mm) | 2306-2207 | 2450-2750 | 1750-2050 | Racing (high power) |
| 7" (178mm) | 2806-2807 | 1300-1500 | 1100-1300 | Long range, cinema |
| 10" (254mm) | 3110-3508 | 600-900 | 400-600 | Photography, heavy lift |
| 13-15" | 4010-5010 | 300-500 | 200-400 | Heavy lift, delivery |

---

## Thrust Testing

    How to choose motor + prop combo:

    1. Determine All-Up Weight (AUW)
    2. Minimum total thrust = AUW × 2 (2:1 ratio for stable hover)
    3. Recommended total thrust = AUW × 3-4 (for agility/wind margin)
    4. Per motor thrust = Total thrust / number of motors

    Example:
    AUW = 800g (5" FPV quad)
    Total thrust needed: 800 × 3 = 2400g minimum
    Per motor: 2400 / 4 = 600g per motor
    Choose motor+prop combo that gives 600g+ at full throttle

### Efficiency Sweet Spot

    Motors are most efficient at 50-70% throttle.

    Hover throttle should be 40-60% for best efficiency:
    ├── Below 40%: Over-powered (wasting weight on motor/prop)
    ├── 40-60%: Good balance of performance and efficiency
    └── Above 60%: Under-powered (poor performance, battery drain)

---

## Propeller Selection

### Propeller Nomenclature

    Example: 5043 or 5×4.3×3
    ├── 5 = diameter in inches (127mm)
    ├── 4.3 = pitch in inches
    └── 3 = number of blades (triblade)

    Pitch: How far the prop moves forward per revolution
    ├── Higher pitch = more speed, more current draw
    └── Lower pitch = more efficiency, better hover

### Blade Count Comparison

| Blades | Efficiency | Thrust | Noise | Use Case |
|--------|-----------|--------|-------|----------|
| **Bi-blade** | Best | Lower | Quietest | Long range, efficiency |
| **Tri-blade** | Good | Good balance | Moderate | Freestyle, general |
| **Quad-blade** | Fair | Highest | Loudest | Racing, grip |

### Popular Propellers

| Prop | Size | Blades | Style | Price (set) |
|------|------|--------|-------|------------|
| **HQProp 5×4.3×3 V2S** | 5" | 3 | All-round | ₹200-₹350 |
| **Gemfan 51466 V2** | 5" | 3 | Freestyle | ₹200-₹300 |
| **Ethix S5** | 5" | 3 | Freestyle | ₹300-₹400 |
| **HQProp 7×3.5×2** | 7" | 2 | Long range | ₹250-₹400 |
| **APC 13×4.5** | 13" | 2 | Heavy lift | ₹500-₹800 |

---

## Motor Brands (India Availability)

| Brand | Quality | Price Range | Notes |
|-------|---------|-------------|-------|
| **T-Motor** | Premium | ₹1,500-₹3,000/motor | Industry standard |
| **BrotherHobby** | Excellent | ₹1,200-₹2,500 | Great performance |
| **iFlight XING** | Good | ₹1,000-₹2,000 | Value king |
| **Emax** | Good | ₹800-₹1,500 | Budget friendly |
| **Flashhobby** | Budget | ₹500-₹900 | Amazon India |
| **Readytosky** | Budget | ₹400-₹800 | Amazon India |

---

## Motor Maintenance

    Check regularly:
    ├── Bearing smoothness (spin by hand, listen for grinding)
    ├── Bell tightness (prop nut should be secure)
    ├── Magnet condition (no chips or cracks)
    ├── Winding insulation (no burn marks)
    ├── Shaft straightness (wobble = bent shaft)
    └── Motor screws (threadlock recommended)

    After crash:
    ├── Spin each motor by hand
    ├── Check for bent shaft (visible wobble)
    ├── Check prop adapter for cracks
    └── Replace props (always, even if they look fine)

---

[← Previous: Edge Computing & Embedded AI](34-edge-ai.md) | [Back to Index](../README.md) | [Next: Frame Design & Aerodynamics →](36-frame-design.md)
