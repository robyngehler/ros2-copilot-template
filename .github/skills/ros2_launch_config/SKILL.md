---
name: ros2_launch_config
description: "Use when creating or refactoring ROS2 launch files, launch arguments, parameter loading, multi-node composition, or lifecycle startup behavior."
---

# ros2_launch_config

Use this skill when the user asks for a launch file, a bringup entrypoint, launch composition, parameter injection, or lifecycle startup sequencing.

## Load First

- `.github/instructions/ros2-launch-and-lifecycle.instruction.md`
- `.github/instructions/ros2-packages-and-nodes.instruction.md`

## Workflow

1. Decide whether the launch file is a top-level bringup file or a reusable sub-launch.
2. Declare every meaningful launch argument explicitly.
3. Resolve package paths and parameter files in a readable way.
4. Compose nodes rather than building one oversized launch script.
5. Add lifecycle event handlers only where startup sequencing actually matters.

## Structure Pattern

```text
package_name/
└── launch/
    ├── bringup.launch.py
    ├── sensors.launch.py
    └── includes/
```

## Skeleton

```python
import os
from ament_index_python.packages import get_package_share_directory
from launch import LaunchDescription
from launch.actions import DeclareLaunchArgument
from launch.substitutions import LaunchConfiguration
from launch_ros.actions import Node


def generate_launch_description():
    config_file = os.path.join(
        get_package_share_directory("robot_core"),
        "config",
        "params.yaml",
    )
    namespace_arg = DeclareLaunchArgument("robot_name", default_value="robot_1")
    namespace = LaunchConfiguration("robot_name")

    controller = Node(
        package="robot_core",
        executable="controller",
        namespace=namespace,
        parameters=[config_file],
        output="screen",
    )

    return LaunchDescription([namespace_arg, controller])
```

## Parameter File Guidance

- keep long parameter surfaces in YAML, not inline dictionaries
- use launch-time overrides sparingly and visibly
- keep parameter names aligned with node declarations

## Lifecycle Integration

```python
from launch import LaunchDescription
from launch.actions import EmitEvent, RegisterEventHandler
from launch.event_handlers import OnProcessStart
from launch_ros.actions import LifecycleNode
from launch_ros.events.lifecycle import ChangeState
from lifecycle_msgs.msg import Transition


def generate_launch_description():
    driver_node = LifecycleNode(
        package="robot_drivers",
        executable="lidar_driver",
        name="lidar",
        output="screen",
    )

    configure_event = RegisterEventHandler(
        OnProcessStart(
            target_action=driver_node,
            on_start=[
                EmitEvent(
                    event=ChangeState(
                        lifecycle_node_matcher=lambda n: n == driver_node,
                        transition_id=Transition.TRANSITION_CONFIGURE,
                    )
                )
            ],
        )
    )

    return LaunchDescription([driver_node, configure_event])
```

## Common Mistakes To Avoid

- hiding important launch behavior inside helper code with unclear defaults
- pushing too many unrelated nodes into one file
- using inline parameters for complex configurations that belong in `config/`
- auto-activating lifecycle nodes without understanding error handling