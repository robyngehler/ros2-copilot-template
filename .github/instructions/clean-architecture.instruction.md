---
description: "Use when creating or reviewing domain, application, infrastructure, or presentation code in this ROS2 template. Defines Clean Architecture boundaries and common anti-patterns."
---

# Clean Architecture Guidance

Keep dependencies pointing inward.

## Layer Boundaries

- `domain/`: entities, value objects, domain services, interfaces, validation rules
- `application/`: use cases, orchestration, ports, transaction boundaries, workflow coordination
- `infrastructure/`: ROS2 nodes, publishers, subscribers, service servers, action servers, repositories, persistence, hardware adapters
- presentation or external interfaces: CLI, GUI, APIs, dashboards, operator workflows

## Dependency Rules

- domain must not import ROS2, TF2, transport messages, storage clients, or framework-specific runtime code
- application may depend on domain, but not directly on ROS2 infrastructure details
- infrastructure implements domain or application-facing interfaces
- presentation code should call use cases or application services instead of bypassing them

## Mapping Rules

- convert ROS2 messages into domain-friendly models in infrastructure code
- keep serialization, deserialization, topic naming, and QoS selection out of the domain layer
- keep business decisions out of thin adapters when a use case or domain service should own them

## File Placement Rules

- domain models go under `src/domain/`
- application workflows go under `src/application/`
- ROS2 nodes, launch-facing adapters, repositories, and hardware integrations go under `src/infrastructure/`

## Review Checklist

- would this file still make sense without ROS2 imports
- is message mapping isolated to infrastructure
- does the use case own the business rule instead of the transport adapter
- did a new dependency break inward-only layering

## Common Anti-Patterns

- domain entities that store `Node`, `LifecycleNode`, `Publisher`, or ROS2 message types
- use cases that call `create_publisher`, `create_subscription`, or `create_service`
- infrastructure classes that bury business validation which belongs in domain or application code
- presentation code that reaches around application services to manipulate infrastructure directly