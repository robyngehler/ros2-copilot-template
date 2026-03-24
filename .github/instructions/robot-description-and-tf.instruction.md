---
description: "Use when editing URDF, xacro, TF2 integration, frame naming, static transforms, or robot-model related ROS2 code."
---

# Robot Description And TF Guidance

Robot modeling and transform code should stay consistent, explicit, and easy to debug.

## URDF And Xacro Rules

- prefer reusable xacro properties and macros over duplicated XML blocks
- keep link, joint, collision, inertial, and visual definitions aligned
- use descriptive frame and joint names that fit the robot domain

## Frame Tree Rules

- keep a clear and stable frame hierarchy
- document expected parent-child relationships for key frames
- avoid ad hoc frame names that drift between packages

## TF2 Rules

- keep TF2 code in infrastructure adapters or helper services
- avoid pushing `geometry_msgs` or TF2 types into the domain layer
- catch transform lookup exceptions and log the affected frames clearly
- use static broadcasters for fixed transforms and dynamic broadcasters only for changing relationships

## Review Checklist

- are frame names consistent across URDF, TF publishers, and consumers
- is the transform lookup failure path handled cleanly
- are fixed transforms modeled as static instead of repeatedly published dynamic transforms
- is domain code still free of TF2-specific dependencies