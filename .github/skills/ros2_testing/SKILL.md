---
name: ros2_testing
description: "Use when adding or refactoring unit tests, integration tests, launch tests, ROS2 fixtures, mocks, or coverage around nodes, adapters, and clean-architecture boundaries."
---

# ros2_testing

Use this skill when a change needs tests or when existing tests should be reorganized around the ROS2 testing pyramid.

## Load First

- `.github/instructions/ros2-testing.instruction.md`
- `.github/instructions/clean-architecture.instruction.md`

## Workflow

1. Put pure logic under unit tests first.
2. Add integration tests only for ROS2 wiring and callback behavior.
3. Use launch tests for multi-node or startup orchestration.
4. Mock hardware and unstable external systems.
5. Cover failure paths, not only happy paths.

## Test Pyramid

```text
unit -> integration -> launch
```

## Python Fixture Pattern

```python
import pytest
import rclpy
from rclpy.node import Node


@pytest.fixture(scope="module")
def ros_context():
    rclpy.init()
    yield
    rclpy.shutdown()


@pytest.fixture
def test_node(ros_context):
    node = Node("test_helper")
    yield node
    node.destroy_node()
```

## C++ Pattern

```cpp
#include <gtest/gtest.h>
#include <gmock/gmock.h>

class MockRobotRepository : public repositories::IRobotRepository {
public:
    MOCK_METHOD(entities::RobotState, get_state, (), (override));
    MOCK_METHOD(void, set_mode, (entities::RobotMode), (override));
};
```

## Launch Test Pattern

```python
from launch import LaunchDescription
from launch_ros.actions import Node
from launch_testing.actions import ReadyToTest


def generate_test_description():
    app_node = Node(package="my_robot", executable="main_node")
    return LaunchDescription([app_node, ReadyToTest()])
```

## Common Mistakes To Avoid

- testing hardware directly in automated CI paths
- writing only launch tests when unit tests would be simpler and faster
- ignoring error handling and timeout behavior
- letting ROS2 global state leak between tests