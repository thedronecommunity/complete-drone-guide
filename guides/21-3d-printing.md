# Guide 21: 3D Printing for Drones

[← Previous: Weather & Environment](20-weather-environment.md) | [Back to Index](../README.md) | [Next: Ground Control Stations →](22-ground-control-stations.md)

---

## Why 3D Print Drone Parts?

3D printing lets you design and manufacture custom drone parts in hours instead of weeks. Perfect for prototyping, replacement parts, and custom mounts.

    ┌──────────────────────────────────────────────────┐
    │         WHAT TO 3D PRINT FOR DRONES             │
    │                                                  │
    │  ✓ Camera mounts and gimbals                    │
    │  ✓ Landing gear                                 │
    │  ✓ GPS mast / antenna mounts                    │
    │  ✓ Battery trays and straps                     │
    │  ✓ Payload release mechanisms                   │
    │  ✓ Sensor mounts (LiDAR, ToF, flow)            │
    │  ✓ Canopy / top plates                          │
    │  ✓ Prop guards                                  │
    │  ✓ Micro/nano drone frames (< 100g thrust)     │
    │                                                  │
    │  ✗ Main frame arms (use carbon fiber instead)   │
    │  ✗ Motor mounts (vibration + heat issues)       │
    │  ✗ Props (balance is impossible)                │
    └──────────────────────────────────────────────────┘

---

## Material Selection

| Material | Strength | Weight | Flexibility | Heat Resist | Price/kg | Best For |
|----------|----------|--------|-------------|-------------|----------|----------|
| **PLA** | Medium | Light | Brittle | 60°C | ₹800-₹1,200 | Prototyping, indoor |
| **PETG** | Good | Medium | Some flex | 80°C | ₹1,000-₹1,500 | General drone parts |
| **ABS** | Good | Medium | Some flex | 100°C | ₹900-₹1,300 | Heat-exposed parts |
| **TPU** | Low | Medium | Very flexible | 80°C | ₹1,500-₹2,500 | Prop guards, bumpers |
| **Nylon (PA)** | Excellent | Medium | Flexible | 120°C | ₹2,000-₹3,000 | Structural parts |
| **CF-PETG** | Very good | Light | Low flex | 85°C | ₹2,500-₹4,000 | Lightweight structural |
| **CF-Nylon** | Excellent | Light | Medium | 130°C | ₹4,000-₹6,000 | High-performance |

### Material Decision Tree

    Does the part need to survive a crash?
    ├── Yes → PETG or Nylon
    │         Is it near motors/ESCs?
    │         ├── Yes → ABS or Nylon
    │         └── No → PETG
    └── No → PLA is fine

    Does it need to flex (prop guard, bumper)?
    └── Yes → TPU (95A Shore)

    Is weight absolutely critical?
    └── Yes → CF-PETG or CF-Nylon

---

## Print Settings for Drone Parts

### Recommended Settings

| Setting | Structural Parts | Light Parts | Flexible Parts |
|---------|-----------------|-------------|---------------|
| **Layer height** | 0.2mm | 0.2mm | 0.2mm |
| **Wall count** | 4-5 | 2-3 | 3-4 |
| **Top/bottom layers** | 4-5 | 3-4 | 4-5 |
| **Infill** | 30-50% | 15-20% | 20-30% |
| **Infill pattern** | Gyroid | Gyroid/Grid | Gyroid |
| **Speed** | 40-60mm/s | 60-80mm/s | 25-35mm/s |
| **Supports** | Only if needed | Minimize | Avoid |

### Orientation Matters

    WRONG:                 RIGHT:
    ┌──────────┐          ┌──┐
    │  mount   │ ← layers ││ │
    │  arm     │   break  ││m│ ← layers
    │          │   here   ││o│   along
    └──────────┘          ││u│   stress
    Layers ⊥ to stress   ││n│   direction
    (weak)                ││t│
                          └──┘
                          (strong)

    Rule: Print so that layer lines run PARALLEL to the
    direction of stress, not perpendicular.

---

## Drone Parts Design Tips

### CAD Software

| Software | Price | Difficulty | Best For |
|----------|-------|-----------|----------|
| **Fusion 360** | Free (hobby) | Medium | Full parametric design |
| **OnShape** | Free (public) | Medium | Browser-based, collaboration |
| **TinkerCAD** | Free | Easy | Beginners, simple mounts |
| **FreeCAD** | Free | Hard | Open source parametric |
| **SolidWorks** | Expensive | Medium | Professional/student |
| **OpenSCAD** | Free | Hard | Code-based, parametric |

### Design Guidelines

    General:
    ├── Minimum wall thickness: 1.2mm (2 perimeters)
    ├── Minimum feature size: 0.8mm
    ├── Hole tolerance: Print 0.2mm larger than needed
    ├── Snap-fit clearance: 0.3mm gap
    └── Screw holes: Design for M3 heat inserts

    Weight optimization:
    ├── Use hollow sections with 2-3 walls
    ├── Add lightening holes where stress is low
    ├── Fillet all corners (distributes stress)
    ├── Use gyroid infill at 15-20% for non-structural
    └── Design ribs instead of thick walls

    Assembly:
    ├── M3 heat-set inserts for reusable screw holes
    ├── Zip tie slots for securing wires
    ├── Cable routing channels in the design
    └── Captive nut slots for easy assembly

---

## Printer Recommendations (India)

| Printer | Type | Build Volume | Price | Notes |
|---------|------|-------------|-------|-------|
| **Creality Ender 3 V3** | FDM | 220×220×250 | ₹15,000 | Best budget starter |
| **Creality K1** | FDM (fast) | 220×220×250 | ₹25,000 | Speed + quality |
| **Bambu Lab A1 Mini** | FDM | 180×180×180 | ₹20,000 | Reliable, easy |
| **Bambu Lab P1S** | FDM (enclosed) | 256×256×256 | ₹55,000 | Best all-round |
| **Artillery Sidewinder** | FDM | 300×300×400 | ₹30,000 | Large format |

---

## Complete Frame: 65mm Micro Drone

    Frame specifications:
    ├── Wheelbase: 65mm
    ├── Material: PLA or PETG
    ├── Weight: 8-12g
    ├── Motors: 0802 brushless
    ├── FC mount: 20×20mm M2
    ├── Battery: 1S 300-450mAh
    └── Print time: ~45 minutes

    Slicer settings:
    ├── Material: PETG
    ├── Layer height: 0.15mm
    ├── Walls: 3
    ├── Infill: 25% gyroid
    ├── Supports: None (design to avoid)
    └── Orientation: Flat, top plate up

    Files: Available on Thingiverse/Printables
    Search: "65mm brushless whoop frame"

---

## Post-Processing

    Weight reduction:
    ├── Sand off support marks
    ├── Drill out unnecessary material
    └── Remove brims/rafts cleanly

    Strength improvement:
    ├── Annealing PLA/PETG (oven at 70°C for 1hr)
    │   Increases heat resistance + strength 20-30%
    │   WARNING: Parts may warp, use a mold
    ├── Epoxy coating on high-stress areas
    └── Carbon fiber tape wrap on arms

    Finishing:
    ├── Light sanding (220 grit)
    ├── Spray paint (adds ~2-5g)
    └── Clear coat for UV protection (outdoor parts)

---

[← Previous: Weather & Environment](20-weather-environment.md) | [Back to Index](../README.md) | [Next: Ground Control Stations →](22-ground-control-stations.md)
