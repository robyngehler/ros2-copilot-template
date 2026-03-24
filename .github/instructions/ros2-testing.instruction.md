---
description: "Use when adding or reviewing unit tests, integration tests, launch tests, mocks, or ROS2 package test structure in this template."
---

# ROS2 Testing Guidance

Test behavior at the lowest useful layer first, then add ROS2 integration only where it provides value.

## Testing Pyramid

- unit tests for pure domain and application logic
- integration tests for ROS2 node behavior, callbacks, transport wiring, and adapter boundaries
- launch or end-to-end tests for startup orchestration and system behavior

## Test Structure

- keep tests under `test/` or `tests/` consistently with the package style
- mirror the source structure where that improves discoverability
- separate pure logic tests from ROS2 runtime tests

## Robotics Rules

- mock hardware interfaces in automated tests
- test safety checks, failure paths, and timeout behavior
- cover parameter edge cases and invalid command handling

## Python Guidance

- use `pytest`
- manage `rclpy.init()` and `rclpy.shutdown()` through fixtures
- avoid hidden global ROS state between tests

## C++ Guidance

- use `gtest` and `gmock`
- keep node lifecycle in `SetUp` and `TearDown` predictable
- use `spin_some` or explicit waits for asynchronous behavior

## Review Checklist

- does new behavior have at least one focused test
- are message mapping and adapter boundaries exercised where risk is high
- are mocks used instead of real hardware
- do tests cover both success and failure paths