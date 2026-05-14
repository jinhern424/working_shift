# working_shift

### Z-axis
#### Foxglove setup
In Foxglove — add a Button panel, set topic to /wheel_toggle, message type std_msgs/Bool, value {"data": true}. Each click toggles the wheel.

```bash
# Source ROS first
source /opt/ros/humble/setup.bash

# Check if topics appear
ros2 topic list

# You should see:
# /current_z_pos
# /target_z
# /main_state
# /change_main_state

# Monitor current z position
ros2 topic echo /current_z_pos

# Send a target z position (in metres)
ros2 topic pub /target_z std_msgs/msg/Float32 "data: 0.01"

# Trigger homing
ros2 topic pub /main_state std_msgs/msg/String "data: homing"
```

### Cam + pump

#### Foxglove Setup
| Panel | Topic | Type | Value |
| :--- | :--- | :--- | :--- |
| Button | `/cam_control` | `std_msgs/String` | `{"data": "up"}` |
| Button | `/cam_control` | `std_msgs/String` | `{"data": "down"}` |
| Slider/Input | `/pump_cmd` | `std_msgs/Int32` | `{"data": 2000}` |

```bash
# Move cam up
ros2 topic pub --once /cam_control std_msgs/msg/String "data: 'up'"

# Move cam down
ros2 topic pub --once /cam_control std_msgs/msg/String "data: 'down'"

# Cam up, then pump runs for 3 seconds
ros2 topic pub --once /cam_control std_msgs/msg/String "data: 'up'"
ros2 topic pub --once /pump_cmd std_msgs/msg/Int32 "data: 3000"
```

### stepper motor
#### Foxglove Setup

| Panel | Topic | Purpose |
| :--- | :--- | :--- |
| Plot | `/current_xy_pos` | Live XY position graph |
| Raw Messages | `/xy_status` | Status string |
| Raw Messages | `/xy_minmax` | Axis limits |
| Publish button | `/main_state` | Trigger homing |
| Publish button | `/target_xy` | Send XY target |

```bash
ros2 run micro_ros_agent micro_ros_agent serial --dev /dev/ttyUSB0 -b 115200

# Terminal 1 - watch position
ros2 topic echo /current_xy_pos

# Terminal 2 - watch status
ros2 topic echo /xy_status

# Terminal 3 - watch axis limits
ros2 topic echo /xy_minmax

## Step 1 — Home first (always do this before moving):
bashros2 topic pub --once /main_state std_msgs/msg/String "data: 'homing'"
Watch /xy_status — should go homing → available, and /change_main_state should publish free.

## Step 2 — Test small moves:
bash# Move to x=100, y=50 (in steps)
ros2 topic pub --once /target_xy geometry_msgs/msg/Point "{x: 100.0, y: 50.0, z: 0.0}"

# Move to x=-200, y=100
ros2 topic pub --once /target_xy geometry_msgs/msg/Point "{x: -200.0, y: 100.0, z: 0.0}"

# Return to center
ros2 topic pub --once /target_xy geometry_msgs/msg/Point "{x: 0.0, y: 0.0, z: 0.0}"

## Step 3 — Test limits (should clamp safely):
# Try to move beyond xMax=750 — should clamp to 750
ros2 topic pub --once /target_xy geometry_msgs/msg/Point "{x: 9999.0, y: 0.0, z: 0.0}"

## Step 4 — Test move rejection before homing:
bash# Restart ESP32, then immediately try to move (should reject)
ros2 topic pub --once /target_xy geometry_msgs/msg/Point "{x: 100.0, y: 100.0, z: 0.0}"
ros2 topic echo /xy_status
# should print: "move rejected: homing not done"
```
