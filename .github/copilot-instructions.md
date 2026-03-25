# ROS2 Copilot Workspace Instructions

This repository is organized for GitHub Copilot in VS Code. Treat `AGENTS.md` as the neutral engineering contract, then use this file as the Copilot-specific operating model. Keep the always-on context small, and load specialized guidance from `.github/instructions/` and `.github/skills/` only when the task actually needs it.

## Operating Model

1. Read `README.md` for repository-level orientation.
2. Read `AGENTS.md` for the durable, tool-neutral rules.
3. Treat this file as the minimal Copilot operating model.
4. Load one relevant file from `.github/instructions/` before expanding further.
5. Load a skill from `.github/skills/` only when the task needs a reusable workflow or scaffold.

## Source Of Truth Order

- repository overview: `README.md`
- neutral engineering contract: `AGENTS.md`
- workspace-wide operating model: this file
- targeted rules and constraints: `.github/instructions/`
- reusable task workflows: `.github/skills/`
- specialist Copilot personas: `.github/agents/`

Do not load every instruction or every skill by default. Match context to the task.

## What Lives Where

- architecture boundaries: `clean-architecture.instruction.md`
- package layout and node structure: `ros2-packages-and-nodes.instruction.md`
- topics, QoS, services, actions, interfaces: `ros2-communication.instruction.md`
- launch composition and lifecycle behavior: `ros2-launch-and-lifecycle.instruction.md`
- test structure and strategy: `ros2-testing.instruction.md`
- URDF, xacro, TF2, frames: `robot-description-and-tf.instruction.md`
- build, source, rosdep, colcon, debugging: `ros2-runtime-workflows.instruction.md`

## Skills

Use these only for task-shaped work:

- `ros2_node_creation`
- `ros2_launch_config`
- `ros2_service_action`
- `ros2_messaging`
- `ros2_testing`
- `ros2_lifecycle`
- `ros2_transforms`
- `ros2_diagnostics`
- `ros2_bag`

## Custom Agents

Use these when a task benefits from a narrower persona instead of the default generalist:

- `ros-backend`
- `frontend-ui`
- `integration-review`

## Default Engineering Expectations

- keep ROS2 framework details out of the domain layer
- keep message mapping in infrastructure code
- prefer lifecycle nodes only when managed state adds real value
- update tests when public behavior changes

## Quick Runtime Reference

- build: `colcon build --symlink-install`
- test: `colcon test`
- source overlay: `source install/setup.bash`
- inspect graph: `ros2 node list`, `ros2 topic list`, `ros2 service list`, `ros2 action list`

Use `.github/instructions/ros2-runtime-workflows.instruction.md` when the task involves environment setup, rosdep, build failures, dependency order, or runtime debugging.
```
colcon build --symlink-install
colcon build --packages-select <pkg_name>
colcon build --packages-up-to <pkg_name>
colcon test
colcon test --packages-select <pkg_name>
colcon test-result --all
source install/setup.bash
```

## Introspection Commands

```bash
ros2 node list
ros2 node info <node_name>
ros2 topic list
ros2 topic info <topic>
ros2 topic echo <topic>
ros2 service list
ros2 service type <service>
ros2 action list
ros2 param list
ros2 param get <node> <param>
```

## Debugging Commands

```bash
ros2 doctor
rqt_graph
rqt_console
ros2 run tf2_tools view_frames
```

## Workflow Rules

- source the workspace overlay before runtime checks
- prefer package-scoped builds and tests while iterating
- use graph and topic introspection before assuming wiring bugs
- verify parameter values and namespaces before changing code

## Safety Rule

- avoid destructive cleanup commands unless they are truly required for the task
- prefer explaining why a clean build is needed before wiping build artifacts
