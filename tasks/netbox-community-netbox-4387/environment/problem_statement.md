## Description

NetBox's cable path tracing currently resolves end-to-end connectivity between device interfaces when the path passes through patch panels (front port and rear port pairs). However, it has two significant gaps:

1. **Circuits are not followed as pass-throughs.** When physical cables are patched into a circuit's A-side and Z-side terminations, the system should recognize that the circuit connects those two sides and complete the path to the far-end device interface. Currently, device interfaces connected via a circuit show no connected endpoint at all, which makes the connectivity invisible in NetBox.

2. **Paths through multiple consecutive patch panels fail to resolve correctly.** When a cable path traverses more than a simple two-panel arrangement (e.g., through four panels in a row), the endpoint resolution breaks and the connected endpoint is not correctly identified.

## Expected Behavior

- When cables connect two device interfaces via a circuit (one cable on each side of the circuit), both interfaces should show each other as their connected endpoint with status "connected."
- Deleting a circuit that is part of a complete cable path should tear down the path: both endpoint interfaces should have their connected endpoint and connection status cleared.
- Cable paths through multiple consecutive patch panels (including both rear-port-to-rear-port and front-port-to-rear-port topologies) should resolve correctly regardless of the number of intermediate panels.
- Mixed topologies combining patch panels and circuits should also resolve the full end-to-end path correctly.
- Cables where one termination is a rear port and the other is a circuit termination should be permitted (position matching constraints should only apply between two rear ports).

## Why This Matters

Circuits represent carrier or provider links between locations and are fundamental to how real-world networks are documented in NetBox. Without circuit-aware path tracing, operators cannot see the true end-to-end connectivity of their network through NetBox.
