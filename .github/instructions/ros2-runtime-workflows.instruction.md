---
description: "Use when building, sourcing, testing, introspecting, or debugging ROS2 workspaces in this template. Replaces the old command cheat sheet with task-oriented workflow guidance."
---

# ROS2 Runtime Workflows

Use this file for command-oriented work instead of a standalone commands reference.

## Environment Baseline

- source the ROS2 installation before build, test, rosdep, or runtime commands
- prefer Ubuntu system Python for ROS2 workspace builds when using apt-installed ROS2
- do not run `colcon build` from an active Python virtual environment unless the workspace is intentionally built around that interpreter
- if Python tooling is needed in a venv, deactivate it before ROS2 build and runtime commands

## Core Commands

```bash
source /opt/ros/humble/setup.bash
colcon build --symlink-install
colcon build --packages-select <pkg_name>
colcon build --packages-up-to <pkg_name>
colcon test
colcon test --packages-select <pkg_name>
colcon test-result --all
source install/setup.bash
```

## Build Dependency Rule

- prefer `colcon build --packages-up-to <pkg_name>` over `--packages-select <pkg_name>` when dependencies may not already be built
- use `--packages-select` only when dependency state is already known to be valid

## Recovery After Build Artifact Reset

If you deleted `build/`, `install/`, or `log/` and then continue from another shell, reset stale overlay variables first.

```bash
unset AMENT_PREFIX_PATH COLCON_PREFIX_PATH CMAKE_PREFIX_PATH
deactivate 2>/dev/null || true
source /opt/ros/humble/setup.bash
colcon build --symlink-install
```

Stale prefix variables are noisy but usually not the root failure. Wrong Python interpreter selection is often the real problem.

## rosdep Guidance

- source the ROS2 installation before running `rosdep`
- expect some build tools or ROS-internal Python packages to be installed by apt rather than resolved as rosdep keys
- treat missing rosdep keys carefully before changing package metadata; some entries are not meant to be rosdep-installable

```bash
source /opt/ros/humble/setup.bash
rosdep update
rosdep install --from-paths src --ignore-src -r -y
```

## Python Interpreter Checks

If build errors mention `empy`, `catkin_pkg`, `rosidl_adapter`, or unexpected Python modules, verify the interpreter first.

```bash
which python3
python3 -c "import sys; print(sys.executable, sys.version)"
env | grep -E 'VIRTUAL_ENV|CONDA|PYTHON'
```

## Workspace Layout Rule

- keep discoverable packages directly under `src/` unless the workspace intentionally uses a different colcon-compatible layout
- do not leave stray top-level `package.xml` or `CMakeLists.txt` files in `src/` that pretend to be meta-packages unless that layout is explicitly supported

## Interface File Hygiene

- keep `.msg`, `.srv`, and `.action` files as plain UTF-8 without BOM
- ensure line endings and field formatting are valid before debugging generator failures
- if interface parsing fails unexpectedly, inspect the raw file formatting before changing semantics

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