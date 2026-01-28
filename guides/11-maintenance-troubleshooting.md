# Guide 11: Maintenance & Troubleshooting

[← Previous: AI Integration](10-ai-integration.md) | [Back to Index](../README.md)

---

## Maintenance Schedule

### Before Each Flight

| Check | What to Look For |
|-------|------------------|
| Frame | Cracks, loose screws |
| Props | Chips, bends, cracks |
| Motors | Smooth spin, no grinding |
| Battery | Voltage, no puffing |
| Wires | Loose connections |
| RC | Binding, all channels work |
| GPS | Satellite count (8+) |

### After Each Flight

| Task | Purpose |
|------|---------|
| Visual inspection | Catch crash damage |
| Battery storage charge | 3.8V/cell for storage |
| Clean if dirty | Prevent debris damage |
| Note any issues | Track recurring problems |

### Weekly (Active Use)

| Task | Details |
|------|---------|
| Tighten screws | Vibration loosens fasteners |
| Clean dust/debris | Compressed air on electronics |
| Check motor temps | Hot motors = problem |
| Inspect wire insulation | Rubbing wears insulation |
| Update log book | Track flight hours |

### Monthly

| Task | Details |
|------|---------|
| Deep clean | Remove grime buildup |
| Check prop wear | Replace if edges worn |
| Test battery capacity | Compare to rated capacity |
| Firmware update | Check for updates |
| Backup parameters | Save configuration |
| Inspect frame joints | Carbon fiber can crack |

### Quarterly

| Task | Details |
|------|---------|
| Replace props | Even if they look fine |
| Test ESC response | Check for desync |
| Full sensor recalibration | All sensors |
| Review flight logs | Look for trends |
| Motor bearing check | Spin by hand, feel for grit |

---

## Common Problems & Solutions

### Connection Problems

**Symptom:** Cannot connect to flight controller

| Check | Solution |
|-------|----------|
| USB cable | Try different cable |
| Drivers | Install/update (ImpulseRC Driver Fixer) |
| Port | Try different USB port |
| Firmware | May need DFU mode reflash |
| Baud rate | Match GCS and FC settings |

**Symptom:** No heartbeat from FC (companion computer)

| Check | Solution |
|-------|----------|
| TX/RX wiring | TX→RX, RX→TX (crossover) |
| Baud rate | Match FC parameter (921600) |
| Serial port enabled | Check FC SERIAL_PROTOCOL |
| Permissions | Add user to dialout group |

### Motor Issues

**Symptom:** Motors not spinning

| Check | Solution |
|-------|----------|
| Battery connected? | ESCs need battery power |
| Armed? | Check arming status |
| ESC signal wire | Verify connection to FC |
| Motor direction set? | Check in BLHeli |
| Pre-arm errors? | Clear all errors first |

**Symptom:** Motor spinning wrong direction

| Solution | Method |
|----------|--------|
| Hardware | Swap any 2 motor wires |
| Software | Reverse in BLHeli Configurator |

**Symptom:** Motor grinding/rough

| Cause | Solution |
|-------|----------|
| Debris | Clean motor, remove debris |
| Bent shaft | Replace motor |
| Bad bearing | Replace motor |
| Screw too long | Use shorter motor screws |

### GPS Problems

**Symptom:** No GPS lock

| Check | Solution |
|-------|----------|
| Outdoors? | GPS needs clear sky view |
| Antenna up? | GPS antenna faces sky |
| Wait time | First fix can take 2-3 minutes |
| Interference | Move away from metal/electronics |

**Symptom:** GPS position jumps around

| Cause | Solution |
|-------|----------|
| Multipath | Move away from buildings |
| Low satellites | Wait for more satellites |
| Interference | Relocate GPS module |
| Damaged antenna | Replace GPS module |

**Symptom:** Toilet bowling (circular drift)

| Cause | Solution |
|-------|----------|
| Compass interference | Recalibrate away from metal |
| Compass/GPS offset | Verify COMPASS_ORIENT parameter |
| Motor interference | Enable compass motor calibration |

### Flight Instability

**Symptom:** Oscillations/vibrations

| Cause | Solution |
|-------|----------|
| Loose components | Tighten everything |
| P gains too high | Lower PID P values |
| Damaged props | Replace propellers |
| Unbalanced props | Balance or replace |
| FC vibration | Improve vibration isolation |

**Symptom:** Drone flips on takeoff

| Cause | Solution |
|-------|----------|
| Wrong motor order | Verify with motor test |
| Wrong motor direction | Fix CW/CCW pattern |
| Wrong prop direction | Match prop to motor |
| FC orientation wrong | Check arrow direction |

**Symptom:** Drift in Loiter mode

| Cause | Solution |
|-------|----------|
| Compass needs cal | Recalibrate compass |
| GPS quality poor | Wait for better lock |
| Accelerometer drift | Recalibrate accelerometer |
| Wind | Normal if windy |

### Battery/Power Issues

**Symptom:** Short flight time

| Cause | Solution |
|-------|----------|
| Old battery | Test capacity, replace if low |
| Overweight drone | Reduce weight |
| Aggressive flying | Fly more efficiently |
| Windy conditions | Wind uses more power |
| Motors working hard | Check for mechanical drag |

**Symptom:** Voltage sag under load

| Cause | Solution |
|-------|----------|
| Weak battery | Replace battery |
| High C-rating needed | Use higher C battery |
| Undersized wiring | Use thicker power wires |
| Poor connections | Resolder joints |

**Symptom:** FC brownouts/reboots

| Cause | Solution |
|-------|----------|
| Voltage sag | Add capacitor (470-1000μF) |
| Undersized BEC | Use higher current BEC |
| ESC noise | Add LC filter |
| Loose power wires | Resolder connections |

### Radio Issues

**Symptom:** Radio connection drops

| Cause | Solution |
|-------|----------|
| Antenna position | Keep antenna vertical |
| Antenna damaged | Replace antenna |
| Range exceeded | Stay within range |
| Interference | Change channels |
| Failsafe not set | Configure failsafe |

**Symptom:** Stick inputs reversed

| Cause | Solution |
|-------|----------|
| Channel reverse | Fix in transmitter settings |
| Wrong channel map | Match AETR/TAER order |

---

## Pre-Flight Checklist (1 Minute)

### Hardware Check (30 seconds)

- [ ] Frame tight, no cracks
- [ ] Props secure, undamaged
- [ ] Battery secured
- [ ] Antenna positioned correctly
- [ ] Nothing loose/rattling

### Software Check (30 seconds)

- [ ] GPS lock (8+ satellites)
- [ ] Compass healthy
- [ ] Battery voltage correct
- [ ] Flight mode correct (STABILIZE or LOITER)
- [ ] Failsafe configured
- [ ] Home point set

### Environment Check

- [ ] Weather appropriate (wind < 15 mph for beginners)
- [ ] Airspace clear
- [ ] No people in flight path
- [ ] Landing zone clear
- [ ] Know the regulations

---

## Flight Log Analysis

### What to Look For

| Log Field | Healthy | Problem |
|-----------|---------|---------|
| Vibration (VIBE) | < 30 | > 60 = isolation issue |
| GPS satellites | > 8 | < 6 = GPS problems |
| HDOP | < 2 | > 3 = GPS quality poor |
| Current | Consistent | Spikes = motor issues |
| Compass variance | Low | High = interference |
| EKF status | Green | Red/yellow = estimation issues |

### Using MAVExplorer

```bash
# Install
pip install MAVProxy

# Open log
mavexplorer.py flight.bin

# Common graphs:
graph VIBE.VibeX VIBE.VibeY VIBE.VibeZ  # Vibration
graph GPS.NSats GPS.HDop                  # GPS quality
graph CURR.Curr                           # Current draw
graph ATT.Roll ATT.DesRoll                # Attitude tracking
```

---

## Emergency Procedures

### Loss of Control

1. **Switch to STABILIZE mode** (if possible)
2. **Reduce throttle** to descend
3. **If still uncontrollable**, kill throttle
4. **Let it fall** rather than fly away

### Flyaway Prevention

| Safeguard | Purpose |
|-----------|---------|
| Geofence | Limits distance |
| RTL configured | Automatic return |
| Low battery failsafe | Forces return |
| GPS loss failsafe | Lands if GPS lost |

### Fire (LiPo)

1. **Do NOT use water**
2. Use sand or fire extinguisher (Class D)
3. Move battery outdoors if safe
4. Let it burn out if uncontrollable
5. Ventilate area (toxic fumes)

---

## Glossary

| Term | Definition |
|------|------------|
| **AETR** | Aileron, Elevator, Throttle, Rudder (channel order) |
| **APM** | ArduPilot Mega (early flight controller) |
| **BLDC** | Brushless DC Motor |
| **C-Rating** | Battery discharge rate capability |
| **CUAV** | Commercial UAV (flight controller manufacturer) |
| **DShot** | Digital motor protocol |
| **EKF** | Extended Kalman Filter (state estimation) |
| **ESC** | Electronic Speed Controller |
| **FC** | Flight Controller |
| **FPV** | First Person View (flying via camera) |
| **GNSS** | Global Navigation Satellite System |
| **HDOP** | Horizontal Dilution of Precision (GPS quality) |
| **IMU** | Inertial Measurement Unit |
| **KV** | Motor RPM per volt |
| **LiPo** | Lithium Polymer battery |
| **MAVLink** | Micro Air Vehicle Link protocol |
| **PDB** | Power Distribution Board |
| **PID** | Proportional-Integral-Derivative controller |
| **PWM** | Pulse Width Modulation |
| **RSSI** | Received Signal Strength Indicator |
| **RTK** | Real-Time Kinematic (precision GPS) |
| **RTL** | Return to Launch |
| **SBUS** | Serial Bus (RC protocol) |
| **SITL** | Software In The Loop (simulation) |
| **SLAM** | Simultaneous Localization and Mapping |
| **UART** | Universal Asynchronous Receiver-Transmitter |
| **VTOL** | Vertical Take-Off and Landing |

---

## Resources

### Documentation

- [ArduPilot Docs](https://ardupilot.org/copter/)
- [PX4 Docs](https://docs.px4.io/)
- [Betaflight Wiki](https://betaflight.com/docs/wiki)
- [QGroundControl](https://docs.qgroundcontrol.com/)

### Community

- [ArduPilot Forum](https://discuss.ardupilot.org/)
- [PX4 Slack](https://px4.io/slack)
- [RCGroups](https://www.rcgroups.com/)
- [Oscar Liang Blog](https://oscarliang.com/)

### YouTube Channels

- Joshua Bardwell (FPV)
- Painless360 (General)
- Andrew Newton (ArduPilot)
- Chris Rosser (PX4)

---

## Final Words

You now have the knowledge to:
- Understand how drones fly
- Build a complete quadcopter
- Configure and tune firmware
- Program autonomous missions
- Integrate AI capabilities
- Troubleshoot problems

**Remember:**
- Safety first, always
- Props off during testing
- Start simple, add complexity
- Join the community
- Keep learning

**The skills you've gained apply to:**
- Robotics and automation
- Computer vision and AI
- Embedded systems and IoT
- Mechatronics and control systems

*The best way to learn is to build.*

---

**The Drone Community**
Contact: +91 7972632187

Happy Flying! 🚁

---

[← Previous: AI Integration](10-ai-integration.md) | [Back to Index](../README.md)
