# ROS2 Copilot Template

This repository is the official VS Code and GitHub Copilot focused fork of the ROS2 Clean Architecture template. It preserves the original architectural goals, restructures the guidance around Copilot-native customization primitives, and now adds a neutral `AGENTS.md` layer so the template is not coupled to a single tool surface.

## Purpose

The template is meant for ROS2 projects that want:

- Clean Architecture boundaries that stay intact while features grow
- consistent ROS2 patterns in Python and C++
- better context management for GitHub Copilot inside VS Code
- reusable guidance for nodes, launch files, messaging, lifecycle handling, testing, TF2, diagnostics, and bagging

## What Changed In This Fork

The old Claude-centric layout has been replaced by a Copilot-centric layout:

- `AGENTS.md` now holds the durable, tool-neutral engineering contract
- `.github/copilot-instructions.md` is the always-on workspace instruction file
- `.github/instructions/` holds targeted, task-specific guidance that Copilot can load only when needed
- `.github/agents/` holds specialized Copilot personas for backend work, frontend-facing work, and integration review
- `.github/skills/` holds reusable deep-dive workflows and templates for common ROS2 tasks
- the former `rules` and `commands` content is now expressed as instruction files so context stays narrower and easier for Copilot to route

## Context Model

This template is optimized for selective context loading rather than loading every rule up front.

1. Start with `AGENTS.md` for the durable engineering contract.
2. Read `.github/copilot-instructions.md` for the repository-wide Copilot operating model.
3. Load only the instruction file that matches the task.
4. Load a skill or custom agent only when the task needs a reusable workflow or a narrower persona.

That keeps prompts smaller and reduces irrelevant context during normal development.

## Repository Layout

```text
AGENTS.md
.github/
├── copilot-instructions.md
├── agents/
│   ├── frontend-ui.agent.md
│   ├── integration-review.agent.md
│   └── ros-backend.agent.md
├── instructions/
│   ├── context-routing.instruction.md
│   ├── clean-architecture.instruction.md
│   ├── ros2-packages-and-nodes.instruction.md
│   ├── ros2-communication.instruction.md
│   ├── ros2-launch-and-lifecycle.instruction.md
│   ├── ros2-testing.instruction.md
│   ├── robot-description-and-tf.instruction.md
│   └── ros2-runtime-workflows.instruction.md
└── skills/
	├── ros2_node_creation/
	├── ros2_launch_config/
	├── ros2_service_action/
	├── ros2_messaging/
	├── ros2_testing/
	├── ros2_lifecycle/
	├── ros2_transforms/
	├── ros2_diagnostics/
	└── ros2_bag/
```

## Instruction Files

Use `.github/instructions/` for targeted guidance:

- `context-routing.instruction.md`: how Copilot should choose the smallest relevant context
- `clean-architecture.instruction.md`: domain, application, infrastructure, and presentation boundaries
- `ros2-packages-and-nodes.instruction.md`: package structure, naming, parameters, logging, and node layout
- `ros2-communication.instruction.md`: topics, QoS, messages, services, and actions
- `ros2-launch-and-lifecycle.instruction.md`: launch composition and lifecycle node behavior
- `ros2-testing.instruction.md`: unit, integration, and launch testing strategy
- `robot-description-and-tf.instruction.md`: URDF, xacro, TF2, and robot frame guidance
- `ros2-runtime-workflows.instruction.md`: build, test, sourcing, introspection, and debugging commands

## Custom Agents

Use `.github/agents/` when a task benefits from a narrower specialist persona:

- `ros-backend`: ROS2 package, node, messaging, launch, lifecycle, and test work
- `frontend-ui`: frontend-facing integration work with explicit backend contracts
- `integration-review`: end-to-end review of contracts, validation, and architectural fit

## Skills

Use `.github/skills/` when Copilot needs a task-focused workflow rather than a general rule set:

- `ros2_node_creation`: create or refactor ROS2 nodes and package scaffolding
- `ros2_launch_config`: structure launch files and parameter loading
- `ros2_service_action`: implement service and action patterns
- `ros2_messaging`: build publishers, subscribers, and message mapping layers
- `ros2_testing`: add tests across the ROS2 testing pyramid
- `ros2_lifecycle`: implement managed nodes and state transitions
- `ros2_transforms`: integrate TF2 without leaking framework dependencies into the domain
- `ros2_diagnostics`: add diagnostics and health monitoring
- `ros2_bag`: record, replay, and inspect ROS2 bag data

## How To Use In VS Code

Open the repository in VS Code and work with Copilot in natural language. For best results:

1. Describe the ROS2 task directly.
2. Mention the architectural layer or package when it matters.
3. Ask for a specific skill when you want scaffolded output.
4. Keep requests scoped to one subsystem when possible.

Examples:

- "Create a lifecycle-enabled Python sensor node using the `ros2_node_creation` skill."
- "Refactor this publisher/subscriber flow to keep ROS2 message mapping in infrastructure only."
- "Add launch tests for this package using the testing instruction and the `ros2_testing` skill."

## Core Principles

- Domain code stays free of ROS2 dependencies.
- Application code orchestrates use cases and ports.
- Infrastructure code owns ROS2 nodes, adapters, message conversions, and hardware integration.
- Presentation code depends inward.
- Tests and documentation move with meaningful interface changes.

## License

This project remains available under the Apache 2.0 License.
