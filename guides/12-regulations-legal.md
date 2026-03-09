# Guide 12: Regulations & Legal Compliance

[← Previous: Maintenance & Troubleshooting](11-maintenance-troubleshooting.md) | [Back to Index](../README.md) | [Next: FPV Systems →](13-fpv-systems.md)

---

## Why Regulations Matter

Flying a drone isn't like flying a kite. Drones share airspace with manned aircraft, fly over people and property, and carry cameras. Every country regulates them — and ignorance isn't a defense.

**Bottom line:** Know the rules before you fly. The penalties range from fines to jail time.

---

## India — DGCA Drone Rules 2021

India classifies drones by weight and regulates them through the Directorate General of Civil Aviation (DGCA).

### Drone Categories

| Category | Weight (All-Up) | Registration | License | Examples |
|----------|-----------------|--------------|---------|----------|
| **Nano** | ≤ 250g | Not required | Not required | Tiny Whoop, toy drones |
| **Micro** | 250g – 2kg | Required | Remote Pilot Certificate | Custom quads, DJI Mini |
| **Small** | 2kg – 25kg | Required | Remote Pilot Certificate | Photography drones, delivery |
| **Medium** | 25kg – 150kg | Required | Full license | Commercial, industrial |
| **Large** | > 150kg | Required | Full license | Military, heavy cargo |

### What You Need to Fly Legally (Micro and Above)

| Requirement | Where to Get It |
|-------------|-----------------|
| **UAS Registration** (UIN) | DigitalSky portal |
| **Remote Pilot Certificate** | DGCA-authorized training school |
| **UAOP** (Unmanned Aircraft Operator Permit) | DigitalSky portal |
| **Insurance** | Third-party liability (recommended) |
| **Flight Plan Approval** | DigitalSky for controlled airspace |

### Nano Drone Exemptions (< 250g)

This is the sweet spot for hobbyists and educational builds:

- No registration required
- No pilot license required
- No permission needed for flights below 50 feet AGL
- Cannot fly in restricted zones
- Must maintain visual line of sight (VLOS)
- Cannot carry payload for commercial purposes without approval

**This is why most workshop drones and educational kits target sub-250g.** You avoid almost all regulatory overhead while still learning everything about drone engineering.

### Airspace Zones (Color Coding)

```
┌──────────────────────────────────────────────────┐
│              INDIAN AIRSPACE ZONES                │
├──────────────────────────────────────────────────┤
│                                                  │
│  🟢 GREEN ZONE                                   │
│  ├── Up to 400 feet AGL                         │
│  ├── No permission needed (nano)                │
│  └── Self-authorization for micro+ on DigitalSky│
│                                                  │
│  🟡 YELLOW ZONE                                  │
│  ├── Controlled airspace                        │
│  ├── ATC permission required                    │
│  └── Advance flight plan filing                 │
│                                                  │
│  🔴 RED ZONE (No-Fly)                            │
│  ├── Airports (within 5km)                      │
│  ├── Military installations                     │
│  ├── International borders (25km)               │
│  ├── Government buildings (Secretariat, etc.)   │
│  └── Nuclear facilities                         │
│                                                  │
└──────────────────────────────────────────────────┘
```

### DigitalSky Platform

DigitalSky is India's unified drone management system:

| Feature | Purpose |
|---------|---------|
| **Registration** | Get your UIN (Unique Identification Number) |
| **Flight permissions** | Request airspace access |
| **NPNT** (No Permission No Takeoff) | Hardware-level enforcement on registered drones |
| **Real-time tracking** | Mandatory for medium/large drones |

**Note:** NPNT is mandatory for commercial drones but not enforced on sub-250g nano drones.

---

## International Regulations Overview

### United States (FAA)

| Rule | Details |
|------|---------|
| **Registration** | Required for drones > 250g ($5 fee) |
| **Part 107** | Commercial pilot license (knowledge test) |
| **Recreational** | TRUST test (free, online) |
| **Altitude limit** | 400 feet AGL |
| **VLOS** | Required at all times |
| **Remote ID** | Required for most drones (broadcast ID while flying) |
| **Airspace** | Use LAANC for controlled airspace authorization |

### European Union (EASA)

| Category | Risk Level | Requirements |
|----------|------------|--------------|
| **Open** | Low | < 25kg, VLOS, < 120m, basic training |
| **Specific** | Medium | Risk assessment (SORA), operational authorization |
| **Certified** | High | Full aircraft-level certification |

EASA uses class labels (C0-C6) on drones to indicate capability and where they can fly.

### Key Differences

| Aspect | India | USA | EU |
|--------|-------|-----|-----|
| No-reg weight | < 250g | < 250g | < 250g (C0) |
| Commercial license | Remote Pilot Certificate | Part 107 | A2 Certificate |
| Remote ID | NPNT (planned) | Required | Required (C1+) |
| Max altitude | 400 ft | 400 ft | 120m (~394 ft) |
| Night flying | Restricted | Allowed (Part 107 update) | Allowed with conditions |

---

## Building and Selling Drones in India

### Manufacturing Requirements

| Weight Class | Requirements |
|-------------|--------------|
| **Nano (< 250g)** | No type certification. No BIS. Sell as educational kit. |
| **Micro (250g–2kg)** | Type certification from QCI-approved body |
| **Small+ (> 2kg)** | Full type certification + DGCA approval |

### Import/Export

| Task | What You Need |
|------|---------------|
| **Importing components** | Standard import license, AD Code from bank |
| **Importing complete drones** | DGCA import permission + customs clearance |
| **Exporting** | AD Code, IEC (Import-Export Code), standard export docs |

**Pro tip for builders:** Import components (motors, ESCs, flight controllers) individually — they're electronic components, not drones. No special drone import permission needed for parts.

### Educational Kit Exemption

If you're building sub-250g educational drone kits:

- No DGCA type certification
- No BIS compliance
- Can sell directly to consumers, schools, ATL labs
- Must not market as "ready-to-fly" commercial drone
- Include safety documentation

---

## Practical Guidelines for Hobbyists

### Pre-Flight Checklist (Legal)

| Check | Status |
|-------|--------|
| Drone weight < 250g? | If yes, minimal rules apply |
| Flying in green zone? | Check AirMap or DigitalSky |
| Below 50 feet AGL (nano)? | Stay within limits |
| Daylight hours? | Required for most categories |
| Visual line of sight? | Always required |
| Away from airports (5km)? | Non-negotiable |
| Not over crowds? | Safety essential |
| Not near borders (25km)? | Security restriction |

### If Stopped by Authorities

| Situation | Response |
|-----------|----------|
| Police ask about your drone | Stay calm, show your drone's weight class |
| Asked for registration | Nano (< 250g) doesn't need one — explain politely |
| Asked to stop flying | Comply immediately, discuss after |
| Near restricted area unknowingly | Land immediately, apologize, leave |

### Documentation to Carry

Even for nano drones, it helps to carry:
- Proof that your drone is < 250g (weigh it, photograph the scale)
- A printout or screenshot of DGCA nano exemption rules
- Your ID proof
- Contact details of your organization (if applicable)

---

## Common Misconceptions

| Myth | Reality |
|------|---------|
| "All drones need registration" | Only > 250g |
| "You need a license to fly" | Not for nano (< 250g) under 50 ft |
| "FPV is illegal in India" | Not explicitly prohibited for nano, but VLOS required |
| "You can't sell custom drones" | You can sell sub-250g kits without certification |
| "NPNT applies to all drones" | Currently only mandated for commercial > 250g |
| "Flying in my backyard is always legal" | Check zone — even your backyard could be in red zone |

---

## Staying Updated

Drone regulations evolve rapidly. Bookmark these:

| Resource | URL |
|----------|-----|
| **DGCA Drone Rules** | digitalsky.dgca.gov.in |
| **FAA DroneZone** | faadronezone.faa.gov |
| **EASA Drones** | easa.europa.eu/domains/drones |
| **AirMap** | airmap.com (airspace maps) |
| **India NOTAM** | aim-india.aai.aero |

---

## Key Takeaways

```
For Workshop/Educational Builds:
├── Keep it under 250g → Almost zero regulatory overhead
├── Fly in green zones → Check DigitalSky map
├── Stay below 50 feet → No permission needed
├── Maintain VLOS → Always see your drone
└── Carry weight proof → Avoids unnecessary hassle

For Commercial Operations:
├── Register on DigitalSky → UIN mandatory
├── Get Remote Pilot Certificate → Training school
├── File flight plans → For controlled airspace
├── Get insurance → Protect yourself
└── Stay current on rules → They change often
```

---

[← Previous: Maintenance & Troubleshooting](11-maintenance-troubleshooting.md) | [Back to Index](../README.md) | [Next: FPV Systems →](13-fpv-systems.md)
