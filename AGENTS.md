# AGENTS.md

This repository is a ROS2 template for agent-assisted development. It keeps the durable, tool-neutral rules here and uses Copilot-specific files under `.github/` as adapters.

## Scope

- Preserve Clean Architecture boundaries.
- Keep ROS2 implementation detail in infrastructure-facing code.
- Favor small, package-scoped changes over broad rewrites.
- Keep documentation and tests aligned with behavior changes.

## Source Of Truth Order

1. `README.md` for repository purpose and layout.
2. This file for durable engineering constraints.
3. `.github/copilot-instructions.md` for the Copilot operating model.
4. `.github/instructions/` for targeted task guidance.
5. `.github/skills/` for reusable workflows and scaffolds.

## Architecture Rules

- Keep dependencies pointing inward.
- Do not put ROS2 nodes, publishers, subscribers, TF2, transport messages, or storage clients in the domain layer.
- Keep message mapping, QoS selection, topic naming, parameter plumbing, and launch wiring in infrastructure code.
- Use application code to orchestrate use cases instead of letting transport adapters own business decisions.

## ROS2 Engineering Rules

- Prefer parameters, launch configuration, and config files over hardcoded topic names, frame IDs, ports, and robot-specific constants.
- Keep lifecycle behavior explicit when a node is managed. Do not add lifecycle complexity unless it adds operational value.
- Preserve interface compatibility unless the task explicitly allows breaking changes.
- Update tests when public behavior, interfaces, launch wiring, or runtime contracts change.

## Validation Rules

- Prefer package-scoped builds and tests while iterating.
- Run the smallest relevant validation that can prove the change.
- For wiring or startup changes, include runtime or launch-level validation.
- If validation cannot be run in the current environment, say so explicitly.

## Frontend And Integration Rules

- Do not invent browser-facing contracts from raw ROS assumptions.
- Keep any web-facing payloads documented and versionable.
- Distinguish observability tooling from product UI logic.
- Prefer explicit translation layers between ROS2 messages and web payloads when frontend work is involved.

## What Agents Must Not Do

- Do not rewrite repository structure without a concrete reason.
- Do not hardcode environment-specific values that should live in parameters or config.
- Do not bypass application boundaries just because infrastructure code is nearby.
- Do not claim validation happened when it did not.

## Definition Of Done

- The change respects layer boundaries.
- The smallest relevant documentation was updated.
- The relevant validation was run or the limitation was called out.
- The result is consistent with the existing template layout and naming.