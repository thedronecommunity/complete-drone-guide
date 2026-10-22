# Guide 17: PID Tuning

[← Previous: Battery Technology](16-battery-technology.md) | [Back to Index](../README.md) | [Next: Swarm Drones →](18-swarm-drones.md)

---

## What is PID?

PID (Proportional-Integral-Derivative) is the control algorithm that makes your drone fly smoothly. It continuously adjusts motor speeds to match your desired attitude and position.

    ┌──────────────────────────────────────────────────┐
    │               PID CONTROL LOOP                   │
    │                                                  │
    │  Desired      Error        PID         Motor     │
    │  Angle  ──→ (+) ──→ [P + I + D] ──→ Output     │
    │              ↑ (-)                               │
    │              │                                   │
    │              └── Actual Angle (from IMU)          │
    │                                                  │
    │  Loop runs 1000-8000 times per second            │
    └──────────────────────────────────────────────────┘

---

## P, I, D Explained

### Proportional (P)

Reacts to the **current error**. Bigger error = bigger correction.

    P too low:  Drone feels sluggish, slow to respond
    P too high: Drone oscillates rapidly (high-frequency vibrations)
    P right:    Crisp response, snaps to commanded angle

### Integral (I)

Accumulates **past error** over time. Fixes steady-state drift.

    I too low:  Drone slowly drifts in wind, doesn't hold angle
    I too high: Slow oscillation, "wobble", bounce-back
    I right:    Holds position against wind, no drift

### Derivative (D)

Reacts to the **rate of change** of error. Dampens oscillations.

    D too low:  Oscillations from P term aren't dampened
    D too high: Motors get hot, vibrations amplify, "propwash"
    D right:    Smooth stops, no overshoot, clean flips

### Visual Representation

    Response to a step input (e.g., full stick deflection):

    P only:        P+D:           P+I+D (tuned):
    ──╮ ╭─╮       ──╮              ──╮
      │╱  ╲──       │╲──────────     │╲─────────── Target
      ╰    ╰──      ╰               ╰
    Oscillates     Quick settle    Perfect response

---

## PID Axes

Drones have separate PID loops for each axis:

    ┌──────────────────────────────────────────────────┐
    │                                                  │
    │  Roll  (side to side)  ──→ PID_ROLL_P/I/D       │
    │  Pitch (forward/back)  ──→ PID_PITCH_P/I/D      │
    │  Yaw   (rotation)      ──→ PID_YAW_P/I/D        │
    │                                                  │
    │  For most quads: Roll = Pitch PIDs (symmetric)   │
    │  Yaw has separate (lower) values                 │
    └──────────────────────────────────────────────────┘

---

## Betaflight PID Tuning

### Default Starting Points

| Axis | P | I | D | F (Feedforward) |
|------|---|---|---|---|
| **Roll** | 45 | 80 | 30 | 120 |
| **Pitch** | 47 | 84 | 32 | 125 |
| **Yaw** | 45 | 90 | 0 | 75 |

### Tuning Order

    Step 1: Get a flyable baseline
    ├── Start with Betaflight defaults
    ├── Set filter profile to match motor size
    └── Do a short hover test

    Step 2: Tune P term
    ├── Increase P until you see fast oscillation
    ├── Back off 20%
    └── Test with sharp stick movements

    Step 3: Tune D term
    ├── Increase D until motors get warm
    ├── Back off 20%
    └── D dampens the P oscillations

    Step 4: Tune I term
    ├── Test hover in wind
    ├── Increase I if drone drifts
    ├── Decrease if slow wobble appears
    └── Punch throttle - if it tilts, increase I

    Step 5: Tune Feedforward (F)
    ├── Affects stick response speed
    ├── Higher = more responsive to stick input
    └── Too high = overshoot on fast moves

### Betaflight CLI Commands

    # View current PIDs
    get pid

    # Set roll P
    set p_roll = 45

    # Set pitch D
    set d_pitch = 32

    # Save and reboot
    save

    # Use profiles for different flying styles
    profile 0    # Freestyle
    profile 1    # Cinematic
    profile 2    # Racing

### Betaflight RPM Filter

One of the biggest improvements for PID tuning - uses motor RPM data to filter out motor noise:

    # Enable bidirectional DShot
    set dshot_bidir = ON

    # Enable RPM filter
    set rpm_filter_harmonics = 3
    set rpm_filter_min_hz = 100

    # This dramatically reduces noise, allowing:
    # - Higher P and D gains
    # - Less filtering delay
    # - Better propwash handling

---

## ArduPilot PID Tuning

### Autotune

ArduPilot has a built-in autotune that does the work for you:

    Setup:
    1. Assign AUTOTUNE to a flight mode switch
       FLTMODE4 = 15 (AutoTune)

    2. Fly to altitude in stable conditions (low wind)
    3. Switch to AutoTune mode
    4. Drone will twitch on each axis automatically
    5. Takes 5-15 minutes per axis
    6. Land and disarm to save new PIDs

    Parameters:
    AUTOTUNE_AXES = 7 (all axes: roll + pitch + yaw)
    AUTOTUNE_AGGR = 0.1 (0.05 = gentle, 0.1 = default, 0.2 = aggressive)

### Manual Tuning (ArduPilot)

| Parameter | Default | Description |
|-----------|---------|-------------|
| ATC_RAT_RLL_P | 0.135 | Roll rate P |
| ATC_RAT_RLL_I | 0.135 | Roll rate I |
| ATC_RAT_RLL_D | 0.0036 | Roll rate D |
| ATC_RAT_PIT_P | 0.135 | Pitch rate P |
| ATC_RAT_PIT_I | 0.135 | Pitch rate I |
| ATC_RAT_PIT_D | 0.0036 | Pitch rate D |
| ATC_RAT_YAW_P | 0.18 | Yaw rate P |
| ATC_RAT_YAW_I | 0.018 | Yaw rate I |
| ATC_RAT_YAW_D | 0.0 | Yaw rate D |

---

## Diagnosing PID Problems

### Blackbox / Log Analysis

    Betaflight Blackbox:
    1. Enable blackbox logging
       set blackbox_device = SPIFLASH
    2. Fly your normal patterns
    3. Download log via Betaflight Configurator
    4. Open in Blackbox Explorer or PIDtoolbox

    ArduPilot:
    1. Logs saved automatically to SD card
    2. Download via Mission Planner or MAVExplorer
    3. Analyze with UAV Log Viewer (https://plot.ardupilot.org)

### Common Symptoms

| Symptom | Likely Cause | Fix |
|---------|-------------|-----|
| Fast oscillation (jello in video) | P too high | Lower P |
| Slow oscillation/wobble | I too high, or D too low | Lower I, raise D |
| Sluggish response | P too low | Raise P |
| Drift in hover | I too low | Raise I |
| Hot motors after flight | D too high, or noise | Lower D, check filters |
| Bounce-back after flip | P too high or D too low | Lower P, raise D |
| Propwash oscillation | D too low, filters too aggressive | Raise D, adjust filters |
| Toilet bowl in GPS modes | Compass issues, not PID | Calibrate compass |

### Temperature Check

    After a 30-second hover:
    ├── Motors cool to touch → Room for more D
    ├── Motors warm → Good balance
    ├── Motors hot → D too high or mechanical issue
    └── Motors burning → Land immediately, check build

---

## Advanced: Filter Tuning

Filters remove sensor noise before it reaches the PID controller:

    IMU Data (noisy) ──→ Lowpass Filter ──→ Notch Filter ──→ PID Controller
                              │                    │
                              ↓                    ↓
                         Removes high           Removes specific
                         frequency noise        frequency spikes
                                               (motor harmonics)

### Betaflight Filter Settings

    # Gyro lowpass filter
    set gyro_lowpass_hz = 200
    set gyro_lowpass_type = PT1

    # Dynamic notch filter (auto-detects motor noise)
    set dyn_notch_count = 3
    set dyn_notch_q = 300
    set dyn_notch_min_hz = 100
    set dyn_notch_max_hz = 600

    # D-term lowpass (critical for D noise)
    set dterm_lowpass_hz = 150

### Filter Tradeoff

    More filtering → Less noise → More latency → Worse propwash
    Less filtering → More noise → Less latency → Hot motors

    Goal: Minimum filtering that keeps motors cool

---

## PID Tuning Tools

| Tool | Platform | Use Case |
|------|----------|----------|
| **PIDtoolbox** | Desktop (MATLAB) | Betaflight log analysis, filter tuning |
| **Blackbox Explorer** | Desktop | Betaflight log viewer |
| **Betaflight Configurator** | Desktop/Web | Direct PID adjustment |
| **Mission Planner** | Desktop | ArduPilot PID adjustment + logs |
| **UAV Log Viewer** | Web | ArduPilot log analysis |
| **Plasmatree PID Simulator** | Web | Practice PID concepts |

---

## Quick Reference

    5" freestyle quad (typical good tune):
    Roll/Pitch P: 40-55
    Roll/Pitch I: 70-100
    Roll/Pitch D: 25-40
    Yaw P: 35-50
    Yaw I: 80-100

    If in doubt:
    ├── Lower P by 10% if oscillating
    ├── Raise D by 10% if bouncing
    ├── Raise I by 10% if drifting
    └── Always check motor temps

---

[← Previous: Battery Technology](16-battery-technology.md) | [Back to Index](../README.md) | [Next: Swarm Drones →](18-swarm-drones.md)
