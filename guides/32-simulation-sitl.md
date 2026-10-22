# Guide 32: Simulation & SITL Testing

[← Previous: Antenna Design & RF](31-antenna-rf.md) | [Back to Index](../README.md) | [Next: ROS2 for Drones →](33-ros2-drones.md)

---

## Why Simulate?

Simulation lets you test firmware, missions, and algorithms without risking hardware. SITL (Software In The Loop) runs the actual flight controller firmware on your PC with simulated physics.

    ┌──────────────────────────────────────────────────┐
    │         SIMULATION HIERARCHY                    │
    │                                                  │
    │  SITL (Software In The Loop)                    │
    │  ├── Runs real firmware (ArduPilot/PX4) on PC   │
    │  ├── Simulated physics and sensors              │
    │  ├── Connect GCS normally (MAVLink)             │
    │  └── Free, easy to set up                       │
    │                                                  │
    │  HITL (Hardware In The Loop)                    │
    │  ├── Real flight controller hardware            │
    │  ├── Simulated sensors and physics              │
    │  ├── Tests actual hardware behavior             │
    │  └── More realistic than SITL                   │
    │                                                  │
    │  Gazebo / AirSim / Isaac Sim                    │
    │  ├── 3D rendered environments                   │
    │  ├── Camera/LiDAR simulation                    │
    │  ├── Physics engine (realistic)                 │
    │  └── Required for vision/AI testing             │
    └──────────────────────────────────────────────────┘

---

## ArduPilot SITL

### Quick Start

    # Install prerequisites (Ubuntu 22.04+)
    sudo apt install git python3-dev python3-pip python3-matplotlib
    sudo apt install python3-lxml python3-scipy python3-opencv

    # Clone ArduPilot
    git clone https://github.com/ArduPilot/ardupilot.git
    cd ardupilot
    git submodule update --init --recursive

    # Install tools
    Tools/environment_install/install-prereqs-ubuntu.sh -y
    source ~/.profile

    # Launch SITL
    cd ArduCopter
    sim_vehicle.py --console --map

    # You now have a fully functional drone in simulation!
    # MAVProxy commands work:
    mode GUIDED
    arm throttle
    takeoff 10

### Custom Location

    # Start at a custom location (Pune, India)
    sim_vehicle.py --console --map \
        --custom-location=18.5204,73.8567,560,0
    # Format: lat,lon,alt(m),heading(deg)

### Multi-Vehicle SITL

    # Terminal 1
    sim_vehicle.py -v ArduCopter -I 0 --console --map

    # Terminal 2
    sim_vehicle.py -v ArduCopter -I 1

    # Terminal 3
    sim_vehicle.py -v ArduCopter -I 2

    # Each gets its own SYSID and port

---

## PX4 SITL

    # Install PX4
    git clone https://github.com/PX4/PX4-Autopilot.git --recursive
    cd PX4-Autopilot
    bash Tools/setup/ubuntu.sh

    # Launch basic SITL
    make px4_sitl jmavsim

    # Launch with Gazebo
    make px4_sitl gz_x500

    # Connect QGroundControl
    # QGC auto-connects to localhost:14550

---

## Gazebo Simulation

### Gazebo + ArduPilot

    # Install Gazebo Garden
    sudo apt install gz-garden

    # Install ArduPilot Gazebo plugin
    git clone https://github.com/ArduPilot/ardupilot_gazebo
    cd ardupilot_gazebo
    mkdir build && cd build
    cmake .. -DCMAKE_BUILD_TYPE=Release
    make -j4
    sudo make install

    # Launch Gazebo world
    gz sim -v4 -r iris_runway.sdf

    # In another terminal, launch SITL
    sim_vehicle.py -v ArduCopter -f gazebo-iris --console --map

    # You now have a 3D drone in Gazebo controlled via ArduPilot!

### Simulated Sensors in Gazebo

    Available simulated sensors:
    ├── Camera (RGB, depth, stereo)
    ├── LiDAR (2D and 3D)
    ├── IMU (with configurable noise)
    ├── GPS (with configurable accuracy)
    ├── Barometer
    ├── Magnetometer
    ├── Optical flow
    └── Rangefinder

---

## Python Testing with SITL

    from pymavlink import mavutil
    import time

    # Connect to SITL
    conn = mavutil.mavlink_connection('tcp:127.0.0.1:5762')
    conn.wait_heartbeat()
    print("Connected to SITL")

    # Set mode to GUIDED
    conn.set_mode_apm('GUIDED')

    # Arm
    conn.arducopter_arm()
    conn.motors_armed_wait()
    print("Armed")

    # Takeoff to 10m
    conn.mav.command_long_send(
        conn.target_system, conn.target_component,
        mavutil.mavlink.MAV_CMD_NAV_TAKEOFF,
        0, 0, 0, 0, 0, 0, 0, 10)

    time.sleep(10)

    # Fly to waypoint
    conn.mav.set_position_target_global_int_send(
        0, conn.target_system, conn.target_component,
        mavutil.mavlink.MAV_FRAME_GLOBAL_RELATIVE_ALT_INT,
        0b110111111000,  # position only
        int(18.5210 * 1e7), int(73.8570 * 1e7), 10,
        0, 0, 0, 0, 0, 0, 0, 0)

    print("Flying to waypoint...")

---

## Automated Testing

    ArduPilot has a built-in test suite:

    # Run all copter tests
    Tools/autotest/autotest.py build.ArduCopter test.ArduCopter

    # Run specific test
    Tools/autotest/autotest.py test.ArduCopter.FlySquare

    # This tests:
    ├── Takeoff and landing
    ├── Flight modes
    ├── Failsafes
    ├── GPS loss behavior
    ├── Battery failsafe
    ├── Fence breach
    └── Mission execution

---

[← Previous: Antenna Design & RF](31-antenna-rf.md) | [Back to Index](../README.md) | [Next: ROS2 for Drones →](33-ros2-drones.md)
