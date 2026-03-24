---
name: ros2_lifecycle
description: "Use when implementing or refactoring ROS2 lifecycle nodes, lifecycle publishers, transition handling, or lifecycle-aware launch orchestration."
---

# ros2_lifecycle

Use this skill when the component benefits from explicit configure, activate, deactivate, cleanup, and error semantics.

## Load First

- `.github/instructions/ros2-launch-and-lifecycle.instruction.md`
- `.github/instructions/clean-architecture.instruction.md`

## Workflow

1. Decide whether lifecycle management actually adds value.
2. Allocate resources in `on_configure`.
3. Start active behavior only in `on_activate`.
4. Stop behavior in `on_deactivate`.
5. Release resources in `on_cleanup`.
6. Define error behavior deliberately.

## Lifecycle States

```text
unconfigured -> inactive -> active
            <- cleanup <- deactivate
```

## C++ Lifecycle Template

```cpp
#pragma once

#include <rclcpp_lifecycle/lifecycle_node.hpp>
#include <rclcpp_lifecycle/lifecycle_publisher.hpp>

class ManagedNode : public rclcpp_lifecycle::LifecycleNode {
public:
    using CallbackReturn = rclcpp_lifecycle::node_interfaces::LifecycleNodeInterface::CallbackReturn;

    explicit ManagedNode(const std::string& node_name)
        : rclcpp_lifecycle::LifecycleNode(node_name) {}

    CallbackReturn on_configure(const rclcpp_lifecycle::State&) override;
    CallbackReturn on_activate(const rclcpp_lifecycle::State&) override;
    CallbackReturn on_deactivate(const rclcpp_lifecycle::State&) override;
    CallbackReturn on_cleanup(const rclcpp_lifecycle::State&) override;
};
```

## Lifecycle Client Pattern

```cpp
#include <lifecycle_msgs/srv/change_state.hpp>

class LifecycleClient {
public:
    LifecycleClient(rclcpp::Node::SharedPtr node, const std::string& target_node);
    bool change_state(std::uint8_t transition_id);
};
```

## Common Mistakes To Avoid

- doing normal publishing work before activation
- opening hardware connections in constructors instead of lifecycle callbacks
- skipping cleanup and relying on process shutdown
- using lifecycle nodes where a normal node would be simpler and clearer