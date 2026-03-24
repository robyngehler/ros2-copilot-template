---
description: "Use when creating or modifying ROS2 packages, nodes, parameters, package manifests, setup files, or general ROS2 project layout in Python or C++."
---

# ROS2 Packages And Nodes

Use conventional ROS2 structure and keep generated code predictable.

## Package Layout

Typical package contents:

```text
package_name/
├── package.xml
├── CMakeLists.txt or setup.py
├── resource/
├── package_name/ or src/
├── include/
├── launch/
├── config/
└── test/
```

## Workspace Layout Rules

- keep ROS2 packages directly discoverable under `src/` in a standard colcon workspace
- avoid accidental meta-package files at the `src/` root unless the workspace explicitly depends on that layout
- when a package is Python-based, ensure `setup.py` or the chosen Python package configuration is present and complete
- keep `package.xml`, `CMakeLists.txt`, and Python packaging files readable and properly formatted rather than flattened into one line

## Naming Rules

- package names in `snake_case`
- topics, services, and actions in descriptive lowercase names
- message, service, and action type names in PascalCase
- node names should reflect the component role, not the implementation language

## Node Design Rules

- declare parameters close to node initialization
- keep node startup readable and explicit
- separate parameter loading, publisher setup, subscriber setup, service setup, and timer setup
- prefer composition over large monolithic node classes

## Python Package Rules

- use `setup.py` or modern package configuration consistently
- install launch and config files as package data
- expose node executables through `console_scripts` when appropriate

## C++ Package Rules

- keep public headers under `include/<package_name>/`
- declare dependencies explicitly in `CMakeLists.txt` and `package.xml`
- use warning flags and keep target-level dependencies explicit

## Parameters And Logging

- document meaningful parameters in config files
- avoid hardcoded constants that should be parameters
- use ROS2 log levels intentionally: debug for diagnosis, info for lifecycle and state changes, warn for degraded operation, error for failures

## Node Change Checklist

- package manifest updated if dependencies changed
- launch files updated if executable names or parameters changed
- tests updated when node behavior changed
- docs updated when a new public node or parameter surface was introduced
- Python packages include the expected packaging entrypoints and installed resources