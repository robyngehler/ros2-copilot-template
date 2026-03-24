---
name: ros2_node_creation
description: "Use when creating or refactoring a ROS2 node or package in Python or C++. Covers package structure, node setup, parameters, publishers, subscribers, manifests, and launch wiring."
---

# ros2_node_creation

Use this skill when the task is to add a new node, split responsibilities in an existing node, or scaffold a package around a node implementation.

## Load First

- `.github/instructions/clean-architecture.instruction.md`
- `.github/instructions/ros2-packages-and-nodes.instruction.md`
- `.github/instructions/ros2-communication.instruction.md` if the node exposes topics, services, or actions

## Workflow

1. Identify the layer boundary: domain, application, infrastructure, or presentation.
2. Decide whether this is a Python package, C++ package, or mixed workspace addition.
3. Create the package surface first: manifest, build config, source folder, config, launch, and tests.
4. Keep ROS2 APIs in infrastructure code and business rules in domain or application code.
5. Add only the node capabilities actually needed: parameters, publishers, subscribers, services, actions, timers.
6. Finish with launch wiring and focused tests.

## Output Checklist

- package structure is conventional and complete
- executable naming matches the package manifest and launch file
- parameters live in `config/` when non-trivial
- message mapping stays out of domain code
- tests cover the public node behavior that changed

## Reference Layout

```text
package_name/
├── package.xml
├── CMakeLists.txt or setup.py
├── resource/
├── package_name/ or src/
├── include/
├── config/
├── launch/
└── test/
```

## Python Skeleton

```python
import rclpy
from rclpy.node import Node


class SensorNode(Node):
    def __init__(self) -> None:
        super().__init__("sensor_node")
        self.declare_parameter("publish_rate_hz", 10.0)
        self._publish_rate_hz = self.get_parameter("publish_rate_hz").value
        self._publisher = self.create_publisher(...)
        self._subscription = self.create_subscription(...)
        self._timer = self.create_timer(1.0 / self._publish_rate_hz, self._publish)

    def _publish(self) -> None:
        ...
```

## C++ Skeleton

```cpp
#pragma once

#include <rclcpp/rclcpp.hpp>

class SensorNode : public rclcpp::Node {
public:
    explicit SensorNode(const rclcpp::NodeOptions& options = rclcpp::NodeOptions())
        : rclcpp::Node("sensor_node", options) {
        this->declare_parameter("publish_rate_hz", 10.0);
        publisher_ = this->create_publisher<...>("state", 10);
        subscription_ = this->create_subscription<...>("input", 10, ...);
    }

private:
    rclcpp::Publisher<...>::SharedPtr publisher_;
    rclcpp::Subscription<...>::SharedPtr subscription_;
};
```

## Common Mistakes To Avoid

- putting business rules inside callbacks because it feels convenient
- creating a package without wiring launch, config, and test surfaces
- hardcoding QoS, topic names, or parameter defaults without checking repository conventions
- leaking ROS2 message types into domain models