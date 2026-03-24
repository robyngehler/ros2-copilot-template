---
description: "Use when creating or refactoring ROS2 launch files, lifecycle nodes, startup sequences, or managed state transitions. Covers launch composition and lifecycle operating rules."
---

# ROS2 Launch And Lifecycle Guidance

Launch code should describe composition and startup behavior cleanly. Lifecycle code should make component state explicit and safe.

## Launch File Rules

- prefer small launch files that compose other launch files instead of one large entrypoint
- declare launch arguments explicitly and document defaults through descriptions
- keep package paths and parameter file resolution readable
- separate environment setup, arguments, node declarations, and event handlers

## Parameter Loading Rules

- prefer config files over long inline parameter dictionaries when the parameter surface is non-trivial
- keep launch-time overrides explicit
- avoid silently changing behavior through hidden environment assumptions

## Lifecycle Node Rules

- allocate resources in `on_configure`
- activate publishers and start active behavior in `on_activate`
- stop active behavior in `on_deactivate`
- release resources in `on_cleanup`
- use `on_error` when recovery strategy matters

## Behavior Rule

- only perform main publishing or control work while the node is active
- keep lifecycle transitions idempotent where possible
- log state transitions and failure reasons clearly

## Review Checklist

- does the launch file stay readable as more nodes are added
- are lifecycle resources created and torn down in the correct callbacks
- does runtime behavior differ correctly between inactive and active states
- are parameter defaults and launch arguments aligned