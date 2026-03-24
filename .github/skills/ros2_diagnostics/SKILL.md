---
name: ros2_diagnostics
description: "Use when adding ROS2 diagnostics, health monitoring, frequency checks, diagnostic_updater integration, or status reporting around nodes and hardware adapters."
---

# ros2_diagnostics

Use this skill when the task is to expose health status, publish diagnostics, or add operator-visible monitoring surfaces.

## Load First

- `.github/instructions/clean-architecture.instruction.md`
- `.github/instructions/ros2-packages-and-nodes.instruction.md`

## Domain Model Example

```python
from dataclasses import dataclass
from enum import Enum


class HealthLevel(Enum):
    OK = 0
    WARN = 1
    ERROR = 2
    STALE = 3


@dataclass
class ComponentHealth:
    name: str
    level: HealthLevel
    message: str
    values: dict
```

## Infrastructure Pattern

```cpp
#include <diagnostic_updater/diagnostic_updater.hpp>

class DiagnosticsManager {
public:
    explicit DiagnosticsManager(rclcpp::Node::SharedPtr node)
        : updater_(node) {
        updater_.setHardwareID(node->get_name());
    }

    void register_monitor(
        const std::string& name,
        std::function<void(diagnostic_updater::DiagnosticStatusWrapper&)> callback) {
        updater_.add(name, callback);
    }

private:
    diagnostic_updater::Updater updater_;
};
```

## Frequency Monitoring Pattern

```cpp
#include <diagnostic_updater/publisher.hpp>
```

## Common Mistakes To Avoid

- publishing vague diagnostics that cannot drive debugging
- burying health logic inside transport glue without a domain-facing model
- omitting hardware IDs or clear component names
- reporting only errors and never reporting degraded states or stale data