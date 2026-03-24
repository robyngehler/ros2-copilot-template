---
name: ros2_service_action
description: "Use when implementing or refactoring ROS2 services or actions, including interface definitions, servers, clients, feedback handling, cancellation, and domain mapping."
---

# ros2_service_action

Use this skill when the task involves request-response APIs, long-running goals, feedback channels, or cancellation semantics.

## Load First

- `.github/instructions/ros2-communication.instruction.md`
- `.github/instructions/clean-architecture.instruction.md`

## Choose Correctly

- choose a service for short operations with a fast response
- choose an action for work that needs progress feedback, cancellation, or a long-running execution path

## Workflow

1. Define the `.srv` or `.action` contract first.
2. Keep transport contracts stable and explicit.
3. Map requests into domain inputs near the ROS2 edge.
4. Execute application or domain use cases outside thin transport glue.
5. Map results and failures back into responses, results, and feedback.

## Service Definition Example

```text
# srv/SetRobotMode.srv
string mode
bool force_change
---
bool success
string message
string previous_mode
```

## Service Server Skeleton

```cpp
#pragma once

#include <rclcpp/rclcpp.hpp>
#include "robot_interfaces/srv/set_robot_mode.hpp"
#include "domain/use_cases/set_robot_mode.hpp"

namespace infrastructure::ros2::services {

class RobotModeServiceNode : public rclcpp::Node {
public:
    explicit RobotModeServiceNode(std::shared_ptr<domain::use_cases::SetRobotModeUseCase> use_case)
        : rclcpp::Node("robot_mode_service"), use_case_(std::move(use_case)) {
        using namespace std::placeholders;
        service_ = this->create_service<robot_interfaces::srv::SetRobotMode>(
            "set_mode",
            std::bind(&RobotModeServiceNode::handle_set_mode, this, _1, _2));
    }

private:
    void handle_set_mode(
        const std::shared_ptr<robot_interfaces::srv::SetRobotMode::Request> request,
        std::shared_ptr<robot_interfaces::srv::SetRobotMode::Response> response);

    std::shared_ptr<domain::use_cases::SetRobotModeUseCase> use_case_;
    rclcpp::Service<robot_interfaces::srv::SetRobotMode>::SharedPtr service_;
};

} // namespace
```

## Action Definition Example

```text
# action/NavigateToPoint.action
geometry_msgs/Point target_point
float32 max_velocity
bool avoid_obstacles
---
bool success
string message
float32 total_distance
float32 total_time
---
geometry_msgs/Point current_position
float32 distance_remaining
float32 estimated_time
string status
```

## Action Server Skeleton

```cpp
#pragma once

#include <rclcpp/rclcpp.hpp>
#include <rclcpp_action/rclcpp_action.hpp>
#include "robot_interfaces/action/navigate_to_point.hpp"

class NavigationActionServer : public rclcpp::Node {
public:
    using NavigateToPoint = robot_interfaces::action::NavigateToPoint;
    using GoalHandle = rclcpp_action::ServerGoalHandle<NavigateToPoint>;

    explicit NavigationActionServer();

private:
    rclcpp_action::GoalResponse handle_goal(
        const rclcpp_action::GoalUUID& uuid,
        std::shared_ptr<const NavigateToPoint::Goal> goal);
    rclcpp_action::CancelResponse handle_cancel(const std::shared_ptr<GoalHandle> goal_handle);
    void handle_accepted(const std::shared_ptr<GoalHandle> goal_handle);
};
```

## Common Mistakes To Avoid

- using services for long-running operations that need progress or cancellation
- embedding business rules directly inside service or action callbacks
- returning vague error strings instead of structured, actionable responses
- forgetting to handle cancellation paths in actions