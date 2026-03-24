---
name: ros2_transforms
description: "Use when integrating TF2, frame transforms, transform lookup services, static transforms, or frame-aware robot modeling without leaking TF2 into the domain layer."
---

# ros2_transforms

Use this skill when the request touches TF2, frame trees, transform lookups, static broadcasters, or frame-aware infrastructure adapters.

## Load First

- `.github/instructions/robot-description-and-tf.instruction.md`
- `.github/instructions/clean-architecture.instruction.md`

## Domain Rule

The domain layer should not depend on TF2 or `geometry_msgs`.

## Domain Pose Example

```python
from dataclasses import dataclass


@dataclass
class Pose:
    position: tuple
    orientation: tuple
    frame_id: str
    timestamp: float
```

## TF2 Wrapper Service (C++)

```cpp
#pragma once

#include <rclcpp/rclcpp.hpp>
#include <tf2_ros/buffer.h>
#include <tf2_ros/transform_listener.h>

class TFService {
public:
    explicit TFService(rclcpp::Node::SharedPtr node);
    std::optional<domain::entities::Pose> get_transform(
        const std::string& target_frame,
        const std::string& source_frame);
};
```

## Static Transform Broadcaster Pattern

```cpp
#include <tf2_ros/static_transform_broadcaster.h>

class TFPublisher {
public:
    explicit TFPublisher(rclcpp::Node::SharedPtr node);
    void publish_static(const std::string& parent, const std::string& child);
};
```

## Common Mistakes To Avoid

- passing TF2 or `geometry_msgs` types into domain services
- using dynamic broadcasts for fixed transforms
- ignoring transform exceptions and silently returning bad state
- mixing frame conversion logic with unrelated application rules