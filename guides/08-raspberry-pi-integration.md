# Guide 08: Raspberry Pi Integration

[← Previous: Assembly](07-assembly-guide.md) | [Back to Index](../README.md) | [Next: Autonomous Missions →](09-autonomous-missions.md)

---

## Why Add a Companion Computer?

### Flight Controller vs Companion Computer

| Aspect | Flight Controller | Raspberry Pi |
|--------|------------------|--------------|
| Response time | <1ms | 50-200ms |
| Memory | Megabytes | Gigabytes |
| Languages | C++ mainly | Python, anything |
| Internet | No | WiFi/Ethernet |
| Camera | Limited | Full support |
| AI/ML | No | Yes |
| Purpose | Keep drone flying | Smart decisions |

### What Companion Computer Enables

- Computer vision (object detection, tracking)
- AI/ML inference
- Complex mission planning
- Data logging (high-resolution)
- Internet connectivity
- Custom sensors
- Natural language commands

---

## Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                     RASPBERRY PI                            │
│  ┌─────────────────────────────────────────────────────┐   │
│  │  Your Python Script                                  │   │
│  │  - Mission logic                                     │   │
│  │  - Computer vision                                   │   │
│  │  - AI decisions                                      │   │
│  └─────────────────────┬───────────────────────────────┘   │
│                        │ pymavlink                          │
│                        ▼                                    │
│  ┌─────────────────────────────────────────────────────┐   │
│  │  MAVLink Communication                               │   │
│  └─────────────────────┬───────────────────────────────┘   │
└────────────────────────┼────────────────────────────────────┘
                         │ Serial / USB / Ethernet
                         ▼
┌─────────────────────────────────────────────────────────────┐
│                   FLIGHT CONTROLLER                         │
│  - Motor control       - Sensor fusion                      │
│  - Stabilization       - Safety systems                     │
└─────────────────────────────────────────────────────────────┘
```

---

## Hardware Connection Options

### Option 1: Serial/UART (Most Common)

```
Raspberry Pi                Flight Controller
                           
GPIO 14 (Pin 8) TX ────────→ TELEM2 RX
GPIO 15 (Pin 10) RX ←─────── TELEM2 TX
GND (Pin 6) ←─────────────→ GND

Baud rate: 921600
```

**Pros:** Simple, reliable, no drivers needed
**Cons:** Need to configure Pi serial port

### Option 2: USB

```
Raspberry Pi USB ←────────→ Flight Controller USB

Device: /dev/ttyACM0
Baud rate: Usually auto-detected
```

**Pros:** Easy, auto-detected
**Cons:** USB cable bulk, potential disconnect

### Option 3: Ethernet (Advanced FCs)

```
Raspberry Pi Ethernet ←────→ Flight Controller Ethernet

FC IP: 192.168.10.14
Pi IP: 192.168.10.15
Port: 15001 (UDP MAVLink)
```

**Pros:** Higher bandwidth, easier debugging
**Cons:** Only some FCs support (CUAV V6X, etc.)

---

## Raspberry Pi Setup

### Enable Serial Port

```bash
sudo raspi-config
```

Navigate to:
```
Interface Options → Serial Port
  → Login shell over serial? NO
  → Serial hardware enabled? YES
```

### Disable Bluetooth (Frees UART)

Add to `/boot/config.txt`:
```bash
dtoverlay=disable-bt
```

Then:
```bash
sudo systemctl disable hciuart
sudo reboot
```

### Serial Port Names

| Pi Model | Primary UART | Device |
|----------|--------------|--------|
| Pi 3/4 (after BT disable) | PL011 | /dev/ttyAMA0 |
| Pi 3/4 (with BT) | Mini UART | /dev/ttyS0 |
| Alternative | Alias | /dev/serial0 |

### Install MAVLink Libraries

```bash
# Install pip if needed
sudo apt install python3-pip

# Install pymavlink
pip3 install pymavlink

# Optional: Install dronekit (higher-level API)
pip3 install dronekit
```

---

## Flight Controller Configuration

### For Serial Connection (ArduPilot)

```
SERIAL2_PROTOCOL = 2      # MAVLink2
SERIAL2_BAUD = 921        # 921600 baud
```

### For Ethernet Connection (CUAV V6X)

```
NET_ENABLED = 1
NET_P1_TYPE = 1           # UDP client
NET_P1_PORT = 15001
NET_P1_PROTOCOL = 2       # MAVLink2
NET_P1_IP0 = 192
NET_P1_IP1 = 168
NET_P1_IP2 = 10
NET_P1_IP3 = 15           # Raspberry Pi IP
```

---

## MAVLink Connection

### Connection Strings

| Method | Connection String |
|--------|-------------------|
| Serial | `/dev/ttyAMA0:921600` |
| USB | `/dev/ttyACM0:115200` |
| Ethernet (Pi listens) | `udpin:0.0.0.0:15001` |
| Ethernet (Pi connects) | `udpout:192.168.10.14:15001` |

### Basic Connection Script

```python
from pymavlink import mavutil

# Connect to FC via serial
connection = mavutil.mavlink_connection('/dev/ttyAMA0', baud=921600)

# Wait for heartbeat
print("Waiting for heartbeat...")
connection.wait_heartbeat()
print(f"Connected! System {connection.target_system}, Component {connection.target_component}")

# Receive messages
while True:
    msg = connection.recv_match(blocking=True)
    if msg:
        print(f"{msg.get_type()}: {msg.to_dict()}")
```

### Ethernet Connection Script

```python
from pymavlink import mavutil

# Pi listens, FC connects
connection = mavutil.mavlink_connection('udpin:0.0.0.0:15001')

# Wait for heartbeat
connection.wait_heartbeat()
print("Connected via Ethernet!")
```

---

## Common MAVLink Messages

### Reading Telemetry

```python
# Get GPS position
msg = connection.recv_match(type='GPS_RAW_INT', blocking=True)
lat = msg.lat / 1e7  # Convert to degrees
lon = msg.lon / 1e7
alt = msg.alt / 1000  # Convert to meters

# Get attitude
msg = connection.recv_match(type='ATTITUDE', blocking=True)
roll = msg.roll    # radians
pitch = msg.pitch
yaw = msg.yaw

# Get battery
msg = connection.recv_match(type='BATTERY_STATUS', blocking=True)
voltage = msg.voltages[0] / 1000  # Convert to volts
remaining = msg.battery_remaining  # Percentage
```

### Sending Commands

```python
# Arm motors
connection.mav.command_long_send(
    connection.target_system,
    connection.target_component,
    mavutil.mavlink.MAV_CMD_COMPONENT_ARM_DISARM,
    0,  # Confirmation
    1,  # 1 = arm, 0 = disarm
    0, 0, 0, 0, 0, 0
)

# Disarm motors
connection.mav.command_long_send(
    connection.target_system,
    connection.target_component,
    mavutil.mavlink.MAV_CMD_COMPONENT_ARM_DISARM,
    0, 0, 0, 0, 0, 0, 0, 0
)

# Takeoff to 10 meters
connection.mav.command_long_send(
    connection.target_system,
    connection.target_component,
    mavutil.mavlink.MAV_CMD_NAV_TAKEOFF,
    0,
    0, 0, 0, 0, 0, 0,
    10  # Altitude in meters
)
```

### Changing Flight Mode

```python
# Get mode ID
mode_id = connection.mode_mapping()['GUIDED']

# Set mode
connection.mav.set_mode_send(
    connection.target_system,
    mavutil.mavlink.MAV_MODE_FLAG_CUSTOM_MODE_ENABLED,
    mode_id
)
```

---

## Flight Modes via Python

| Mode | ID | Description |
|------|----|-------------|
| STABILIZE | 0 | Manual + auto-level |
| ALT_HOLD | 2 | Maintains altitude |
| LOITER | 5 | GPS position hold |
| GUIDED | 4 | Accepts computer commands |
| AUTO | 3 | Follows waypoints |
| RTL | 6 | Return to launch |
| LAND | 9 | Autonomous landing |

### Pre-flight Checks

```python
def wait_for_arm_ready():
    """Wait until drone is ready to arm"""
    while True:
        msg = connection.recv_match(type='SYS_STATUS', blocking=True)
        
        # Check if pre-arm checks pass
        if msg.onboard_control_sensors_health & mavutil.mavlink.MAV_SYS_STATUS_PREARM_CHECK:
            return True
        
        time.sleep(0.5)
```

---

## Complete Example: Takeoff and Land

```python
#!/usr/bin/env python3
"""
Simple takeoff and land example
ALWAYS TEST WITH PROPS OFF FIRST
"""

from pymavlink import mavutil
import time

# Connect
connection = mavutil.mavlink_connection('/dev/ttyAMA0', baud=921600)
connection.wait_heartbeat()
print("Connected!")

def set_mode(mode_name):
    """Change flight mode"""
    mode_id = connection.mode_mapping()[mode_name]
    connection.mav.set_mode_send(
        connection.target_system,
        mavutil.mavlink.MAV_MODE_FLAG_CUSTOM_MODE_ENABLED,
        mode_id
    )
    time.sleep(1)
    print(f"Mode set to {mode_name}")

def arm():
    """Arm the drone"""
    connection.mav.command_long_send(
        connection.target_system,
        connection.target_component,
        mavutil.mavlink.MAV_CMD_COMPONENT_ARM_DISARM,
        0, 1, 0, 0, 0, 0, 0, 0
    )
    time.sleep(2)
    print("Armed!")

def takeoff(altitude):
    """Takeoff to specified altitude"""
    connection.mav.command_long_send(
        connection.target_system,
        connection.target_component,
        mavutil.mavlink.MAV_CMD_NAV_TAKEOFF,
        0, 0, 0, 0, 0, 0, 0, altitude
    )
    print(f"Taking off to {altitude}m")

def land():
    """Land the drone"""
    set_mode('LAND')
    print("Landing...")

# Main sequence
try:
    set_mode('GUIDED')
    arm()
    takeoff(10)
    
    # Wait at altitude
    time.sleep(30)
    
    land()
    
except KeyboardInterrupt:
    print("Interrupted! Landing...")
    land()
```

---

## Motor Testing (PROPS OFF!)

```python
#!/usr/bin/env python3
"""
Test individual motors - REMOVE ALL PROPELLERS FIRST!
"""

from pymavlink import mavutil
import time

connection = mavutil.mavlink_connection('/dev/ttyAMA0', baud=921600)
connection.wait_heartbeat()

def test_motor(motor_number, duration=2):
    """
    Test a single motor (1-4)
    Throttle: 1100-1200 for slow spin
    """
    print(f"Testing motor {motor_number}...")
    
    # Create motor test command
    connection.mav.command_long_send(
        connection.target_system,
        connection.target_component,
        mavutil.mavlink.MAV_CMD_DO_MOTOR_TEST,
        0,
        motor_number - 1,  # Motor instance (0-indexed)
        1,                  # Throttle type (1 = percent)
        10,                 # Throttle value (10%)
        duration,           # Duration in seconds
        0, 0, 0
    )
    
    time.sleep(duration + 1)
    print(f"Motor {motor_number} test complete")

# Test each motor
print("⚠️  REMOVE ALL PROPELLERS BEFORE CONTINUING!")
input("Press Enter to continue...")

for motor in [1, 2, 3, 4]:
    test_motor(motor)
    time.sleep(1)

print("All motor tests complete!")
```

---

## Troubleshooting

### No Connection

| Symptom | Check |
|---------|-------|
| No heartbeat | Wiring TX/RX crossed? |
| Permission denied | Add user to dialout group |
| Device not found | Check /dev/tty* exists |
| Connection timeout | Baud rate match? |

```bash
# Add user to dialout group
sudo usermod -a -G dialout $USER
# Logout and login again

# Check if device exists
ls -la /dev/ttyAMA0
ls -la /dev/ttyACM0

# Test serial port
sudo screen /dev/ttyAMA0 921600
```

### MAVLink Not Stable

| Issue | Solution |
|-------|----------|
| Packet loss | Lower baud rate |
| Delays | Check CPU usage on Pi |
| Disconnects | Check wiring, power |
| Garbled data | Verify baud rate matches |

### Ethernet Connection (CUAV V6X)

```bash
# Set Pi IP (run after each reboot)
sudo ip addr add 192.168.10.15/24 dev eth0

# Verify
ip addr show eth0 | grep "192.168.10.15"

# Test connectivity
ping -c 3 192.168.10.14
```

---

## Best Practices

### Power

- Use 5V BEC rated for 2-3A
- Don't power Pi from FC 5V output (usually too weak)
- Add capacitor near Pi for voltage stability

### Mounting

- Vibration isolation (foam tape)
- Secure against movement
- Adequate ventilation/cooling
- Protect from crashes

### Software

- Always have manual override available
- Implement timeouts and failsafes
- Log everything
- Test with props off first!

---

## Key Takeaways

1. **TX→RX crossover** for serial connections
2. **Disable Bluetooth** to free primary UART
3. **GUIDED mode** for computer control
4. **Always test with props off** first
5. **Implement failsafes** in your code
6. **Log everything** for debugging

---

[← Previous: Assembly](07-assembly-guide.md) | [Back to Index](../README.md) | [Next: Autonomous Missions →](09-autonomous-missions.md)
