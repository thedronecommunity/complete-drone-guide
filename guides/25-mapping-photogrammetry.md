# Guide 25: Mapping & Photogrammetry

[← Previous: Payload Integration](24-payload-integration.md) | [Back to Index](../README.md) | [Next: Precision Agriculture →](26-precision-agriculture.md)

---

## What Is Drone Photogrammetry?

Photogrammetry reconstructs 3D models and maps from overlapping 2D photographs. Drones capture hundreds of photos in a systematic grid, and software stitches them into orthomosaics, elevation models, and 3D meshes.

    ┌──────────────────────────────────────────────────┐
    │           PHOTOGRAMMETRY PIPELINE                │
    │                                                  │
    │  1. Mission Planning (grid/survey pattern)       │
    │  2. Image Capture (70-80% overlap)               │
    │  3. GCP Placement (optional, for accuracy)       │
    │  4. Photo Alignment (feature matching)           │
    │  5. Dense Point Cloud Generation                 │
    │  6. Mesh / DSM / DTM Creation                   │
    │  7. Orthomosaic Generation                       │
    │  8. Export (GeoTIFF, OBJ, LAS)                  │
    └──────────────────────────────────────────────────┘

---

## Key Concepts

### Overlap

    ┌────┐ ┌────┐ ┌────┐
    │    │ │    │ │    │  ← Individual photos
    │  ╔═╪═╪════╪═╪═╗  │
    │  ║ │ │    │ │ ║  │  ← Overlap region
    └──╫─┘ └────┘ └─╫──┘
       ╚═════════════╝

    Front overlap (along flight): 70-80%
    Side overlap (between lines): 60-70%

    Higher overlap = better reconstruction but more photos
    For 3D models: Use 80-85% overlap in both directions

### GSD (Ground Sampling Distance)

    GSD = pixel size on the ground

    GSD (cm/px) = (Sensor Width × Flight Height × 100) / (Focal Length × Image Width)

    Example:
    Sony RX100 at 50m altitude:
    Sensor width = 13.2mm, Focal length = 8.8mm, Image width = 5472px
    GSD = (13.2 × 50 × 100) / (8.8 × 5472) = 1.37 cm/pixel

    GSD guidelines:
    ├── Survey/mapping: 2-5 cm/px (fly at 50-100m)
    ├── Inspection: 0.5-2 cm/px (fly at 15-40m)
    ├── 3D modeling: 1-3 cm/px (fly at 30-60m)
    └── Agriculture: 3-10 cm/px (fly at 50-120m)

---

## Mission Planning for Mapping

### Grid Mission (ArduPilot / Mission Planner)

    Mission Planner → Plan → Survey (Grid):
    1. Draw polygon around area
    2. Set altitude (determines GSD)
    3. Set camera (auto-calculates spacing)
    4. Set overlap (70/60 minimum)
    5. Set speed (5-10 m/s)
    6. Generate waypoints
    7. Upload to drone

    For oblique / 3D:
    - Add a second pass at 45° camera tilt
    - Or use crosshatch pattern (grid + perpendicular grid)

### Camera Settings for Mapping

    Mode: Manual or Shutter Priority
    ├── Shutter: 1/800s minimum (avoid motion blur)
    ├── ISO: 100-400 (low noise)
    ├── Aperture: f/4-f/5.6 (sharp across frame)
    ├── Focus: Manual, set to infinity
    ├── Format: JPEG (RAW if color accuracy critical)
    └── White balance: Fixed (not auto)

---

## Processing Software

| Software | Type | Price | Best For |
|----------|------|-------|----------|
| **WebODM** | Open source | Free | Budget, self-hosted |
| **OpenDroneMap** | Open source | Free | CLI processing |
| **Pix4Dmapper** | Commercial | ₹50,000+/yr | Professional mapping |
| **Agisoft Metashape** | Commercial | ₹20,000 (standard) | 3D models, research |
| **DroneDeploy** | Cloud SaaS | ₹25,000+/yr | Easy cloud processing |
| **COLMAP** | Open source | Free | Research, photogrammetry |

### WebODM Setup (Free)

    # Install with Docker
    git clone https://github.com/OpenDroneMap/WebODM
    cd WebODM
    ./webodm.sh start

    # Access at http://localhost:8000
    # Upload images → Create task → Process
    # Download orthomosaic, DSM, 3D model

    Processing settings:
    ├── dsm: true (digital surface model)
    ├── dtm: true (digital terrain model)
    ├── orthophoto-resolution: 2 (cm/px)
    ├── mesh-octree-depth: 12 (detail level)
    └── feature-quality: high

---

## Output Products

| Product | Format | Use Case |
|---------|--------|----------|
| **Orthomosaic** | GeoTIFF | 2D top-down map, measurement |
| **DSM** (Digital Surface Model) | GeoTIFF | Elevation including buildings/trees |
| **DTM** (Digital Terrain Model) | GeoTIFF | Bare earth elevation |
| **3D Point Cloud** | LAS/PLY | Volumetric analysis |
| **3D Mesh** | OBJ/FBX | Visualization, inspection |
| **Contour Lines** | SHP/DXF | Topographic maps |
| **NDVI Map** | GeoTIFF | Vegetation health (needs multispectral) |

---

## Ground Control Points (GCPs)

    For survey-grade accuracy:

    Without GCPs: ±1-5m absolute accuracy
    With GCPs:    ±2-5cm absolute accuracy
    With RTK GPS: ±2-5cm without GCPs

    GCP placement:
    ├── Minimum 5 GCPs across the site
    ├── Place at edges and center
    ├── Use high-contrast markers (checkerboard)
    ├── Survey with RTK GPS or total station
    └── Must be visible in multiple photos

    ┌──────────────────────────┐
    │  G                    G  │
    │                          │
    │          G               │
    │                          │
    │  G                    G  │
    └──────────────────────────┘
    G = GCP placement (5 minimum)

---

## Camera Recommendations for Mapping

| Camera | Weight | Resolution | Sensor | Price | Notes |
|--------|--------|-----------|--------|-------|-------|
| **GoPro Hero** | 150g | 27MP | 1/1.9" | ₹35,000 | Wide angle, OK for mapping |
| **Sony RX100** | 300g | 20MP | 1" | ₹45,000 | Excellent for mapping |
| **Sony A6000** | 460g | 24MP | APS-C | ₹35,000 | Pro mapping, needs gimbal |
| **Ricoh GR III** | 257g | 24MP | APS-C | ₹70,000 | Compact, sharp |
| **MicaSense RedEdge** | 231g | 5×1.2MP | Multispectral | ₹3,00,000+ | Agriculture/science |

---

## Quick Mapping Checklist

    Before flight:
    □ Plan grid mission with correct overlap
    □ Set camera to manual exposure
    □ Check GSD meets requirements
    □ Place GCPs if accuracy needed
    □ Check weather (no harsh shadows ideal)

    During flight:
    □ Verify camera is triggering
    □ Monitor battery for full coverage
    □ Keep consistent altitude (no terrain follow without DTM)

    After flight:
    □ Verify image count and coverage
    □ Check for blur or exposure issues
    □ Import to processing software
    □ Add GCP coordinates if used
    □ Process and verify output accuracy

---

[← Previous: Payload Integration](24-payload-integration.md) | [Back to Index](../README.md) | [Next: Precision Agriculture →](26-precision-agriculture.md)
