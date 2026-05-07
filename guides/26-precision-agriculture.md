# Guide 26: Precision Agriculture

[← Previous: Mapping & Photogrammetry](25-mapping-photogrammetry.md) | [Back to Index](../README.md) | [Next: Search & Rescue →](27-search-rescue.md)

---

## Drones in Agriculture

Drones are transforming Indian agriculture by enabling farmers to monitor crops, detect disease early, and spray precisely -- reducing chemical use by 30-50% compared to traditional methods.

    ┌──────────────────────────────────────────────────┐
    │         AGRICULTURAL DRONE APPLICATIONS          │
    │                                                  │
    │  Survey & Mapping                                │
    │  ├── Field boundary mapping                      │
    │  ├── Topography / drainage planning              │
    │  └── Land record documentation                   │
    │                                                  │
    │  Crop Monitoring                                 │
    │  ├── NDVI vegetation health maps                 │
    │  ├── Disease / pest detection (AI)               │
    │  ├── Crop counting and yield estimation          │
    │  └── Irrigation analysis                         │
    │                                                  │
    │  Spraying                                        │
    │  ├── Pesticide / herbicide application           │
    │  ├── Fertilizer spraying                         │
    │  └── Seed sowing (experimental)                  │
    └──────────────────────────────────────────────────┘

---

## Multispectral Imaging

### NDVI (Normalized Difference Vegetation Index)

    NDVI = (NIR - Red) / (NIR + Red)

    Values:
    -1.0 to 0.0  → Water, bare soil, dead vegetation
     0.0 to 0.3  → Sparse vegetation, stressed crops
     0.3 to 0.6  → Moderate vegetation, growing crops
     0.6 to 1.0  → Dense, healthy vegetation

    Healthy crops reflect more NIR and absorb more Red light.
    A multispectral camera captures these bands separately.

### Multispectral Sensors

| Sensor | Bands | Weight | Resolution | Price |
|--------|-------|--------|-----------|-------|
| **MicaSense RedEdge-P** | 5 (+ panchromatic) | 275g | 1.2MP per band | ₹4,00,000+ |
| **Sentera 6X** | 6 | 170g | 1.2MP per band | ₹3,00,000+ |
| **Parrot Sequoia** | 4 + RGB | 135g | 1.2MP per band | ₹1,50,000 |
| **MAPIR Survey3** | 3 (modified) | 50g | 12MP | ₹30,000 |
| **Modified GoPro (NIR)** | 1 (NIR) | 150g | 12MP | ₹5,000 (filter swap) |

---

## Agricultural Spray Drones

### Spray System Components

    ┌──────────────────────────────────────────┐
    │  SPRAY DRONE SYSTEM                      │
    │                                          │
    │  Tank (10-30L) ──→ Pump (12V/24V)       │
    │                      │                   │
    │                      ↓                   │
    │                  Flow sensor              │
    │                      │                   │
    │                      ↓                   │
    │              Spray bar / boom             │
    │              │  │  │  │  │               │
    │              ▼  ▼  ▼  ▼  ▼               │
    │           Nozzles (fan/cone pattern)      │
    │                                          │
    │  Flow rate controlled by:                │
    │  ├── Pump speed (PWM)                    │
    │  ├── Ground speed (auto-adjust)          │
    │  └── Preset application rate (L/ha)      │
    └──────────────────────────────────────────┘

### Spray Drone Specifications

| Spec | 10L Drone | 16L Drone | 30L Drone |
|------|----------|----------|----------|
| Tank capacity | 10L | 16L | 30L |
| Spray width | 3-5m | 4-6m | 5-8m |
| Coverage/flight | 1-2 acres | 2-4 acres | 4-8 acres |
| Flight time | 8-12 min | 10-15 min | 12-18 min |
| AUW | 15-25 kg | 25-40 kg | 40-70 kg |
| Price | ₹3-5 lakh | ₹5-8 lakh | ₹8-15 lakh |

---

## DGCA Rules for Agricultural Drones (India)

    Key regulations:
    ├── Agricultural drones classified as Small/Medium category
    ├── Type Certificate required
    ├── Remote Pilot License required
    ├── NPNT compliance mandatory
    ├── Fly below 400 feet AGL
    ├── Fly only in Green/Yellow zones
    ├── Third-party insurance required
    ├── CIN (Certificate of Incorporation) for operators
    └── Maintain spray logs and GPS records

    Subsidies:
    ├── 100% subsidy for SC/ST farmers (select states)
    ├── 50% subsidy for small farmers
    ├── 40% subsidy for others
    ├── State-level schemes vary (check state agriculture dept)
    └── Kisan Drone initiative by Govt. of India

---

## Spray Mission Planning

    Mission parameters:
    ├── Swath width: Set based on nozzle coverage (3-6m)
    ├── Flight height: 2-3m above crop canopy
    ├── Speed: 3-5 m/s (for even coverage)
    ├── Application rate: 20-30 L/hectare (typical)
    ├── Nozzle type: Flat fan (80° or 110°)
    ├── Droplet size: 100-300 microns
    └── Buffer zone: 100m from water bodies

    Time of spraying:
    ├── Early morning (6-9 AM) - best
    ├── Late evening (4-6 PM) - good
    ├── Wind < 10 km/h
    ├── No rain expected for 4+ hours
    └── Temperature < 35°C (reduces evaporation)

---

## Business Opportunity

    Agricultural drone services in India:
    ├── Charge: ₹400-₹800 per acre for spraying
    ├── Coverage: 15-25 acres per day (single drone)
    ├── Monthly revenue: ₹1.5-3 lakh (peak season)
    ├── Season: Kharif (Jun-Oct) + Rabi (Oct-Mar)
    ├── Startup cost: ₹5-10 lakh (drone + training + license)
    └── Break-even: 6-12 months

---

[← Previous: Mapping & Photogrammetry](25-mapping-photogrammetry.md) | [Back to Index](../README.md) | [Next: Search & Rescue →](27-search-rescue.md)
