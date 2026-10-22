# Guide 33: ROS2 for Drones

[← Previous: Simulation & SITL](32-simulation-sitl.md) | [Back to Index](../README.md) | [Next: Edge Computing & Embedded AI →](34-edge-ai.md)

---

## What Is ROS2?

ROS2 (Robot Operating System 2) is a middleware framework for robotics that provides communication between software components (nodes). For drones, it bridges the gap between flight controllers and high-level autonomy.

    ┌──────────────────────────────────────────────────┐
    │           ROS2 DRONE ARCHITECTURE               │
    │                                                  │
    │  Flight Controller (ArduPilot/PX4)              │
    │       │ MAVLink (serial/UDP)                    │
    │       ↓                                         │
    │  MAVROS / micro-ROS-agent                       │
    │       │ ROS2 topics/services                    │
    │       ↓                                         │
    │  ┌─────────────────────────────┐                │
    │  │     ROS2 Ecosystem         │                │
    │  │  ├── Perception (OpenCV)   │                │
    │  │  ├── Planning (Nav2)       │                │
    │  │  ├── SLAM (ORB-SLAM3)     │                │
    │  │  ├── AI (TensorRT)        │                │
    │  │  └── Logging (rosbag2)    │                │
    │  └─────────────────────────────┘                │
    └──────────────────────────────────────────────────┘

---

## ROS2 Installation

    # Install ROS2 Humble (Ubuntu 22.04)
    sudo apt update
    sudo apt install software-properties-common
    sudo add-apt-repository universe
    sudo apt install ros-humble-desktop

    # Source ROS2
    echo "source /opt/ros/humble/setup.bash" >> ~/.bashrc
    source ~/.bashrc

    # Install MAVROS (ArduPilot bridge)
    sudo apt install ros-humble-mavros ros-humble-mavros-extras

    # Install GeographicLib datasets
    sudo /opt/ros/humble/lib/mavros/install_geographiclib_datasets.sh

---

## MAVROS: Connecting ArduPilot to ROS2

    # Launch MAVROS connecting to SITL
    ros2 launch mavros apm.launch \
        fcu_url:=udp://127.0.0.1:14550@ \
        gcs_url:=udp://@127.0.0.1:14551

    # Important topics:
    /mavros/state              # Armed, mode, connected
    /mavros/local_position/pose  # Current position
    /mavros/global_position/global  # GPS position
    /mavros/setpoint_position/local  # Send position commands
    /mavros/imu/data           # IMU readings
    /mavros/battery            # Battery state

    # Arm and takeoff via ROS2
    ros2 service call /mavros/cmd/arming mavros_msgs/srv/CommandBool "{value: true}"
    ros2 service call /mavros/cmd/takeoff mavros_msgs/srv/CommandTOL "{altitude: 10}"

---

## Writing a ROS2 Drone Node (Python)

    # drone_controller.py
    import rclpy
    from rclpy.node import Node
    from geometry_msgs.msg import PoseStamped
    from mavros_msgs.srv import CommandBool, SetMode

    class DroneController(Node):
        def __init__(self):
            super().__init__('drone_controller')

            # Publisher for position setpoints
            self.pub = self.create_publisher(
                PoseStamped, '/mavros/setpoint_position/local', 10)

            # Timer for publishing setpoints
            self.timer = self.create_timer(0.1, self.send_setpoint)  # 10Hz

            self.target_x = 0.0
            self.target_y = 0.0
            self.target_z = 5.0

        def send_setpoint(self):
            msg = PoseStamped()
            msg.header.stamp = self.get_clock().now().to_msg()
            msg.pose.position.x = self.target_x
            msg.pose.position.y = self.target_y
            msg.pose.position.z = self.target_z
            self.pub.publish(msg)

    def main():
        rclpy.init()
        node = DroneController()
        rclpy.spin(node)
        rclpy.shutdown()

    if __name__ == '__main__':
        main()

---

## Key ROS2 Packages for Drones

| Package | Purpose | Install |
|---------|---------|---------|
| **mavros** | MAVLink ↔ ROS2 bridge | `apt install ros-humble-mavros` |
| **nav2** | Autonomous navigation stack | `apt install ros-humble-navigation2` |
| **image_transport** | Camera image handling | `apt install ros-humble-image-transport` |
| **cv_bridge** | OpenCV ↔ ROS2 images | `apt install ros-humble-cv-bridge` |
| **tf2** | Coordinate transforms | Built into ROS2 |
| **rosbag2** | Data recording/playback | Built into ROS2 |
| **rviz2** | 3D visualization | Built into desktop install |

---

## ROS2 + Camera Pipeline

    # Subscribe to camera and run detection
    from sensor_msgs.msg import Image
    from cv_bridge import CvBridge
    import cv2

    class VisionNode(Node):
        def __init__(self):
            super().__init__('vision_node')
            self.bridge = CvBridge()
            self.sub = self.create_subscription(
                Image, '/camera/image_raw', self.image_callback, 10)

        def image_callback(self, msg):
            frame = self.bridge.imgmsg_to_cv2(msg, 'bgr8')
            # Run detection
            gray = cv2.cvtColor(frame, cv2.COLOR_BGR2GRAY)
            # Detect ArUco markers for precision landing
            dictionary = cv2.aruco.getPredefinedDictionary(cv2.aruco.DICT_4X4_50)
            corners, ids, rejected = cv2.aruco.detectMarkers(gray, dictionary)
            if ids is not None:
                self.get_logger().info(f'Detected marker: {ids[0]}')

---

## ROS2 Launch File

    # drone_launch.py
    from launch import LaunchDescription
    from launch_ros.actions import Node

    def generate_launch_description():
        return LaunchDescription([
            Node(
                package='mavros',
                executable='mavros_node',
                parameters=[{
                    'fcu_url': 'udp://127.0.0.1:14550@',
                    'gcs_url': 'udp://@127.0.0.1:14551',
                }]
            ),
            Node(
                package='my_drone_pkg',
                executable='drone_controller',
            ),
            Node(
                package='my_drone_pkg',
                executable='vision_node',
            ),
        ])

---

[← Previous: Simulation & SITL](32-simulation-sitl.md) | [Back to Index](../README.md) | [Next: Edge Computing & Embedded AI →](34-edge-ai.md)
