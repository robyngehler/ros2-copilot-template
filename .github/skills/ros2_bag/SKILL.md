---
name: ros2_bag
description: "Use when adding ROS2 bag recording, replay, filtering, storage configuration, or bag-based analysis workflows in infrastructure code or tooling."
---

# ros2_bag

Use this skill when the request is about recording ROS2 data, replaying captured data, or building tooling around rosbag2.

## Load First

- `.github/instructions/ros2-runtime-workflows.instruction.md`
- `.github/instructions/clean-architecture.instruction.md`

## Domain Interface Example

```python
class IDataRecorder(ABC):
    @abstractmethod
    def start_recording(self, config: RecordingConfig) -> bool:
        pass

    @abstractmethod
    def stop_recording(self) -> None:
        pass
```

## Recorder Pattern

```cpp
#pragma once

#include <rclcpp/rclcpp.hpp>
#include <rosbag2_cpp/writer.hpp>

class BagRecorder {
public:
    explicit BagRecorder(rclcpp::Node::SharedPtr node) : node_(node) {}
    bool start_recording(const std::string& bag_name, const std::vector<std::string>& topics);
    void stop_recording();

private:
    rclcpp::Node::SharedPtr node_;
    std::unique_ptr<rosbag2_cpp::Writer> writer_;
};
```

## Reader Pattern

```cpp
#include <rosbag2_cpp/reader.hpp>
```

## Common Mistakes To Avoid

- recording every topic by default without storage intent
- mixing bagging concerns into domain logic
- ignoring long-running storage limits and splitting behavior
- using bag replay as a substitute for focused automated tests