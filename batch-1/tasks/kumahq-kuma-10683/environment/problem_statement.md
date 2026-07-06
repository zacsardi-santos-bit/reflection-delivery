## Description

Kuma's hostname generator mechanism allows operators to automatically assign DNS hostnames to services by configuring label-based selectors. However, this feature currently only supports single-zone services and external services — multi-zone services are not recognized as a valid target. There is no way to point a hostname generator at a multi-zone service, so those services cannot participate in the automatic hostname assignment workflow.

## Expected Behavior

- The hostname generator configuration should support a multi-zone service selector type, alongside the existing single-zone and external service selector types.
- A hostname generator configured with a multi-zone service selector should automatically compute and assign a hostname to any matching multi-zone service (based on label matching), and the assigned hostname should be reflected in the service's status.
- A hostname generator targeting a different service type (e.g., single-zone services) should not accidentally generate hostnames for multi-zone services.
- A multi-zone service with no matching hostname generator should not receive any automatically generated hostname.

## Why This Matters

Operators managing multi-zone deployments need a consistent, automated way to assign DNS names to their multi-zone services. Without this, they are forced to handle hostname assignment manually or use workarounds, creating an inconsistency in how different service types are treated within the mesh.
