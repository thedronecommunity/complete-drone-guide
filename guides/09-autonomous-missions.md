# Guide 09: Autonomous Missions

[← Previous: Raspberry Pi](08-raspberry-pi-integration.md) | [Back to Index](../README.md) | [Next: AI Integration →](10-ai-integration.md)

---

## What Is an Autonomous Mission?

A pre-programmed flight plan the drone executes without manual control.

```
┌─────────────────────────────────────────────────────────────┐
│                    MISSION EXECUTION                        │
│                                                             │
│   Home ──→ Takeoff ──→ WP1 ──→ WP2 ──→ WP3 ──→ Land       │
│    ●         ↑         ●       ●       ●        ↓          │
│              │                                  │          │
│              └────────── RTL if needed ─────────┘          │
└─────────────────────────────────────────────────────────────┘
```

---

## Mission Components

### Waypoint Attributes

| Attribute | Description | Example |
|-----------|-------------|---------|
| Latitude | GPS coordinate | 18.5204° N |
| Longitude | GPS coordinate | 73.8567° E |
| Altitude | Height above home | 50 meters |
| Speed | Travel speed | 5 m/s |
| Delay | Time to wait | 10 seconds |
| Actions | Commands at waypoint | Take photo |

### Mission Commands

| Command | Function |
|---------|----------|
| NAV_WAYPOINT | Fly to coordinates |
| NAV_TAKEOFF | Autonomous takeoff |
| NAV_LAND | Autonomous landing |
| NAV_RETURN_TO_LAUNCH | Return home |
| NAV_LOITER_TIME | Hold position for duration |
| NAV_LOITER_UNLIM | Hold position indefinitely |
| DO_SET_CAM_TRIGG_DIST | Take photos at intervals |
| DO_CHANGE_SPEED | Adjust flight speed |
| DO_SET_ROI | Point camera at location |
| CONDITION_DELAY | Wait before next command |
| CONDITION_YAW | Rotate to heading |

---

## Planning in QGroundControl

### Creating a Mission

```
1. Open QGroundControl
2. Connect to drone
3. Click "Plan" view (left sidebar)
4. Click on map to add waypoints
5. Configure each waypoint:
   - Altitude
   - Speed
   - Actions
6. Click "Upload" to send to FC
```

### Survey Patterns

QGC can auto-generate patterns:

| Pattern | Use Case |
|---------|----------|
| **Survey** | Mapping rectangular areas |
| **Corridor** | Following linear features |
| **Circular** | Inspection around point |
| **Structure Scan** | 3D building capture |

### Mission Validation

Before upload, QGC checks:
- Altitude limits
- Geofence compliance
- Battery estimate
- Speed limits
- Waypoint distances

---

## Mission Planning via Python

### Simple Mission Upload

```python
from pymavlink import mavutil
import time

connection = mavutil.mavlink_connection('/dev/ttyAMA0', baud=921600)
connection.wait_heartbeat()

def upload_mission(waypoints):
    """
    Upload a mission to the flight controller
    
    waypoints: list of (lat, lon, alt) tuples
    """
    # Clear existing mission
    connection.mav.mission_clear_all_send(
        connection.target_system,
        connection.target_component
    )
    time.sleep(1)
    
    # Send mission count
    connection.mav.mission_count_send(
        connection.target_system,
        connection.target_component,
        len(waypoints) + 2  # +2 for home and takeoff
    )
    
    # Wait for mission request
    for seq in range(len(waypoints) + 2):
        msg = connection.recv_match(type='MISSION_REQUEST', blocking=True)
        
        if seq == 0:
            # Home position (auto-set, but we need placeholder)
            send_mission_item(seq, 16, 0, 0, 0)  # NAV_WAYPOINT as placeholder
        elif seq == 1:
            # Takeoff command
            send_mission_item(seq, 22, 0, 0, 10)  # NAV_TAKEOFF to 10m
        else:
            # Waypoint
            wp = waypoints[seq - 2]
            send_mission_item(seq, 16, wp[0], wp[1], wp[2])  # NAV_WAYPOINT
    
    # Wait for acknowledgment
    msg = connection.recv_match(type='MISSION_ACK', blocking=True)
    if msg.type == 0:
        print("Mission uploaded successfully!")
    else:
        print(f"Mission upload failed: {msg.type}")

def send_mission_item(seq, command, lat, lon, alt):
    """Send a single mission item"""
    connection.mav.mission_item_int_send(
        connection.target_system,
        connection.target_component,
        seq,                    # Sequence number
        mavutil.mavlink.MAV_FRAME_GLOBAL_RELATIVE_ALT,
        command,                # Command type
        0,                      # Current (0 = not current)
        1,                      # Autocontinue
        0, 0, 0, 0,            # Params 1-4
        int(lat * 1e7),        # Latitude
        int(lon * 1e7),        # Longitude
        alt                     # Altitude
    )

# Example mission
waypoints = [
    (18.5204, 73.8567, 20),   # WP1: 20m altitude
    (18.5210, 73.8570, 25),   # WP2: 25m altitude
    (18.5215, 73.8565, 20),   # WP3: 20m altitude
]

upload_mission(waypoints)
```

### Starting a Mission

```python
def start_mission():
    """Set mode to AUTO and start mission"""
    
    # Set to AUTO mode
    mode_id = connection.mode_mapping()['AUTO']
    connection.mav.set_mode_send(
        connection.target_system,
        mavutil.mavlink.MAV_MODE_FLAG_CUSTOM_MODE_ENABLED,
        mode_id
    )
    
    # Arm if not armed
    connection.mav.command_long_send(
        connection.target_system,
        connection.target_component,
        mavutil.mavlink.MAV_CMD_COMPONENT_ARM_DISARM,
        0, 1, 0, 0, 0, 0, 0, 0
    )
    
    print("Mission started!")

# Monitor mission progress
def monitor_mission():
    """Print mission progress"""
    while True:
        msg = connection.recv_match(type='MISSION_CURRENT', blocking=True, timeout=1)
        if msg:
            print(f"Current waypoint: {msg.seq}")
        
        # Check if mission complete
        msg = connection.recv_match(type='MISSION_ITEM_REACHED', blocking=False)
        if msg:
            print(f"Reached waypoint: {msg.seq}")
```

---

## Advanced Features

### Terrain Following

Maintain constant height above ground, not fixed altitude.

```
Fixed altitude:          Terrain following:
                         
───────────────────      ─────────────────
      ↑ 50m                    ↑ 10m AGL
──────┼────────────      ┌─────┼─────┐
      │                  │     │     │ Ground
~~~~~~│~~~~~~~~~~       ~~└────│─────┘~~~~~~
    Ground                     
```

**Requirements:**
- Terrain database (loaded to FC or SD card)
- Or rangefinder sensor (lidar/sonar)

**ArduPilot parameters:**
```
TERRAIN_ENABLE = 1
TERRAIN_FOLLOW = 1
```

### Rally Points

Pre-defined safe return locations.

```
                [Rally 2]
                    ●
                    
    [Home]                    [Drone]
       ●                         ✈
                    
                [Rally 1]
                    ●
```

If RTL triggered:
1. FC calculates nearest rally point
2. Returns there instead of home
3. Useful for large operating areas

### Geofencing

Virtual boundaries that trigger failsafe.

| Fence Type | Function |
|------------|----------|
| Max altitude | Prevents flying too high |
| Min altitude | Prevents flying too low |
| Circle | Maximum distance from home |
| Polygon | Custom inclusion/exclusion zones |

**ArduPilot parameters:**
```
FENCE_ENABLE = 1
FENCE_TYPE = 7        # Altitude + Circle + Polygon
FENCE_ALT_MAX = 120   # Max 120m
FENCE_RADIUS = 500    # Max 500m from home
FENCE_ACTION = 1      # RTL on breach
```

### Precision Landing

Land on a target using:
- IR beacon
- AprilTag/ArUco marker
- Companion computer vision

```
     [Drone]
        │
        │ Camera view
        ▼
    ┌───────┐
    │  [ ]  │  Landing target
    └───────┘
```

**ArduPilot parameters:**
```
PLND_ENABLED = 1
PLND_TYPE = 1         # Companion computer
```

---

## Real-Time Commands (GUIDED Mode)

Instead of pre-programmed waypoints, send commands in real-time.

### Go To Location

```python
def goto(lat, lon, alt):
    """
    Fly to GPS coordinates in GUIDED mode
    """
    connection.mav.set_position_target_global_int_send(
        0,  # Timestamp
        connection.target_system,
        connection.target_component,
        mavutil.mavlink.MAV_FRAME_GLOBAL_RELATIVE_ALT_INT,
        0b0000111111111000,  # Type mask (position only)
        int(lat * 1e7),
        int(lon * 1e7),
        alt,
        0, 0, 0,  # Velocity
        0, 0, 0,  # Acceleration
        0, 0      # Yaw, yaw rate
    )
    print(f"Going to: {lat}, {lon}, {alt}m")
```

### Velocity Control

```python
def set_velocity(vx, vy, vz):
    """
    Set velocity in body frame (m/s)
    vx: forward/backward
    vy: left/right
    vz: up/down (negative = up)
    """
    connection.mav.set_position_target_local_ned_send(
        0,
        connection.target_system,
        connection.target_component,
        mavutil.mavlink.MAV_FRAME_BODY_NED,
        0b0000111111000111,  # Type mask (velocity only)
        0, 0, 0,  # Position
        vx, vy, vz,  # Velocity
        0, 0, 0,  # Acceleration
        0, 0      # Yaw, yaw rate
    )
```

### Yaw Control

```python
def set_yaw(heading, relative=False):
    """
    Set drone heading
    heading: degrees (0 = North)
    relative: True = relative to current, False = absolute
    """
    connection.mav.command_long_send(
        connection.target_system,
        connection.target_component,
        mavutil.mavlink.MAV_CMD_CONDITION_YAW,
        0,
        heading,          # Target angle
        10,               # Angular speed (deg/s)
        1 if relative else 0,  # Direction: 1=relative
        1 if relative else 0,  # Relative offset
        0, 0, 0
    )
```

---

## Safety Considerations

### Pre-Mission Checklist

- [ ] Mission validated in GCS
- [ ] Battery sufficient (estimate + 20% reserve)
- [ ] GPS lock solid (HDOP < 2, 8+ satellites)
- [ ] Geofence configured
- [ ] RTL altitude set appropriately
- [ ] Weather conditions acceptable
- [ ] Airspace clear
- [ ] Manual override ready

### During Mission

- Always have RC transmitter ready
- Monitor telemetry continuously
- Know how to trigger RTL manually
- Watch battery levels
- Be ready to take manual control

### Failsafe Configuration

| Trigger | Recommended Action |
|---------|-------------------|
| RC loss | RTL |
| GCS loss | Continue mission or RTL |
| Low battery | RTL |
| GPS loss | Land |
| Geofence breach | RTL |

---

## Example: Survey Mission

```python
#!/usr/bin/env python3
"""
Generate and execute a survey pattern
"""

from pymavlink import mavutil
import math

def generate_survey(center_lat, center_lon, width, height, altitude, spacing):
    """
    Generate lawnmower survey pattern
    
    Returns list of (lat, lon, alt) waypoints
    """
    waypoints = []
    
    # Convert spacing from meters to degrees (approximate)
    lat_per_meter = 1 / 111320
    lon_per_meter = 1 / (111320 * math.cos(math.radians(center_lat)))
    
    # Calculate corners
    lat_start = center_lat - (height/2) * lat_per_meter
    lon_start = center_lon - (width/2) * lon_per_meter
    
    # Generate lines
    num_lines = int(height / spacing)
    direction = 1
    
    for i in range(num_lines):
        lat = lat_start + (i * spacing * lat_per_meter)
        
        if direction == 1:
            lon_a = lon_start
            lon_b = lon_start + (width * lon_per_meter)
        else:
            lon_a = lon_start + (width * lon_per_meter)
            lon_b = lon_start
        
        waypoints.append((lat, lon_a, altitude))
        waypoints.append((lat, lon_b, altitude))
        
        direction *= -1
    
    return waypoints

# Generate survey over 100m x 100m area
survey_wps = generate_survey(
    center_lat=18.5204,
    center_lon=73.8567,
    width=100,      # meters
    height=100,     # meters
    altitude=30,    # meters
    spacing=10      # meters between lines
)

print(f"Generated {len(survey_wps)} waypoints")

# Upload and execute
# upload_mission(survey_wps)
# start_mission()
```

---

## Key Takeaways

1. **Missions** = Pre-programmed waypoints with commands
2. **GUIDED mode** = Real-time computer control
3. **AUTO mode** = Execute uploaded mission
4. **Always configure failsafes** before autonomous flight
5. **Test missions in simulation** before real flight
6. **Keep manual override ready** at all times

---

[← Previous: Raspberry Pi](08-raspberry-pi-integration.md) | [Back to Index](../README.md) | [Next: AI Integration →](10-ai-integration.md)
