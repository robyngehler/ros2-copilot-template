---
description: "Use when deciding which repository guidance to load for a task in this Copilot-first ROS2 template. Covers context minimization, routing, and when to use instructions versus skills."
---

# Context Routing For Copilot

This repository is intentionally organized so Copilot does not need to load every guidance file up front.

## Routing Order

Use this order when gathering context:

1. Read `README.md` for repository purpose and structure.
2. Use `.github/copilot-instructions.md` for the always-on operating model.
3. Load the single most relevant file from `.github/instructions/`.
4. Load a file from `.github/skills/` only when the task needs a reusable workflow or scaffold.

## Choose Instructions When

- the task is mainly about standards, constraints, or repository conventions
- you need rules for architecture, testing, communication, launch structure, or runtime workflows
- the answer should be short and focused on how work should be done in this repository

## Choose Skills When

- the user wants a node, launch file, service, action, test suite, lifecycle implementation, TF2 integration, diagnostics, or bagging workflow created or refactored
- the task benefits from a checklist, template, scaffold, or repeatable implementation pattern

## Context Minimization Rules

- do not load unrelated instruction files just because they exist
- prefer one specific instruction over a broad sweep across the repository
- if a task spans multiple areas, load the dominant instruction first and add one more only if the task genuinely crosses boundaries
- when editing code, gather only the files and instruction documents needed to finish the task safely

## Practical Mapping

- architecture or dependency boundaries: `clean-architecture.instruction.md`
- package layout or new nodes: `ros2-packages-and-nodes.instruction.md`
- topics, QoS, messages, services, actions: `ros2-communication.instruction.md`
- launch files or lifecycle behavior: `ros2-launch-and-lifecycle.instruction.md`
- tests: `ros2-testing.instruction.md`
- URDF, xacro, TF2, frame trees: `robot-description-and-tf.instruction.md`
- build, source, debug, introspection commands: `ros2-runtime-workflows.instruction.md`

## Escalation Rule

If the task is ambiguous, ask for the package, layer, or ROS2 component first instead of loading everything.