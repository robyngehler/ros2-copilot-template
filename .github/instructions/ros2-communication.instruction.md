---
description: "Use when working on ROS2 topics, QoS, publishers, subscribers, custom messages, services, or actions. Covers naming, transport choices, and message boundary rules."
---

# ROS2 Communication Guidance

Use ROS2 communication primitives intentionally and keep transport concerns outside the domain.

## Choose The Right Primitive

- topics for continuous or streaming data
- services for short request-response operations
- actions for long-running work with feedback, cancellation, or progress tracking

## Topic Naming

- prefer descriptive, lowercase names
- include namespace when the system is multi-robot or multi-instance
- avoid abbreviations unless the repository already standardizes them
- group by responsibility when it improves clarity, such as state, control, perception, or navigation

## QoS Selection

- high-rate sensor streams usually favor best-effort and volatile QoS
- commands and important state transitions usually need reliable delivery
- latched or persistent state often needs transient-local durability
- choose depth intentionally instead of leaving queue sizes arbitrary

## Boundary Rule

- ROS2 message types belong to infrastructure code
- convert messages to domain entities or application DTOs near the edge
- keep serialization and transport-specific validation out of business logic

## Interface Package Guidance

- place custom messages under `msg/`
- place services under `srv/`
- place actions under `action/`
- keep package manifests and interface generation dependencies aligned with the files you define
- keep interface files free of BOM characters and malformed line breaks
- when an interface parser reports unexpected field-definition errors, check file encoding and raw formatting before changing the interface design

## Review Checklist

- is the chosen primitive appropriate for the interaction pattern
- does QoS match data criticality and update rate
- are names consistent with the surrounding system
- is message mapping isolated away from the domain layer