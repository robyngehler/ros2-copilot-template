---
name: ros2_messaging
description: "Use when creating or refactoring ROS2 publishers, subscribers, message mapping layers, QoS choices, synchronized inputs, or topic-facing adapters."
---

# ros2_messaging

Use this skill when the task is about pub/sub design, topic interfaces, message conversion, callback boundaries, or synchronization.

## Load First

- `.github/instructions/ros2-communication.instruction.md`
- `.github/instructions/clean-architecture.instruction.md`

## Workflow

1. Confirm the transport primitive and topic naming.
2. Pick QoS intentionally.
3. Create publishers and subscribers in infrastructure code.
4. Convert ROS2 messages to domain or application models near the callback edge.
5. Keep callback bodies thin and delegate real logic inward.

## Domain-Driven Publisher Pattern

```cpp
#pragma once
#include <rclcpp/rclcpp.hpp>
#include "domain/entities/robot_state.hpp"
#include "robot_interfaces/msg/robot_state.hpp"

class RobotStatePublisher {
public:
    RobotStatePublisher(rclcpp::Node::SharedPtr node, const std::string& topic, const rclcpp::QoS& qos)
        : publisher_(node->create_publisher<robot_interfaces::msg::RobotState>(topic, qos)) {}

    void publish(const domain::entities::RobotState& state) {
        robot_interfaces::msg::RobotState msg;
        msg.mode = static_cast<int>(state.mode);
        publisher_->publish(msg);
    }

private:
    rclcpp::Publisher<robot_interfaces::msg::RobotState>::SharedPtr publisher_;
};
```

## Subscriber Adapter Pattern

```cpp
template<typename MsgT, typename EntityT>
class BaseSubscriber {
public:
    BaseSubscriber(
        rclcpp::Node::SharedPtr node,
        const std::string& topic,
        const rclcpp::QoS& qos,
        std::function<void(const EntityT&)> callback)
        : callback_(callback) {
        subscription_ = node->create_subscription<MsgT>(
            topic,
            qos,
            [this](const typename MsgT::SharedPtr msg) {
                callback_(convert_to_entity(msg));
            });
    }

protected:
    virtual EntityT convert_to_entity(const typename MsgT::SharedPtr msg) = 0;

private:
    rclcpp::Subscription<MsgT>::SharedPtr subscription_;
    std::function<void(const EntityT&)> callback_;
};
```

## Synchronization Guidance

- use `message_filters` when inputs must be processed together
- keep synchronization policy and buffering explicit
- do not hide time assumptions in downstream business logic

## Common Mistakes To Avoid

- parsing ROS2 messages deep inside domain code
- picking default QoS without checking update rate or reliability needs
- allowing callbacks to grow into business workflows
- mixing topic transport concerns with application decision logic