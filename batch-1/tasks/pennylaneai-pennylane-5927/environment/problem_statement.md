## Description

PennyLane has two device interfaces: an older "legacy" interface that many community and internal devices were built on, and a newer, improved interface. As the framework evolves toward the new interface, users and developers who have devices implemented with the older interface are left without a clean way to use them where the new interface is expected.

We need an adapter class that wraps legacy devices so they can be used transparently with the new device interface.

## Expected Behavior

- Users should be able to wrap any legacy device with the adapter and use it wherever the new device interface is expected.
- The adapter should expose the wrapped device's metadata (name, author, version, wires) through the new interface.
- Shot configuration — including partitioned shots — should be properly translated to the new interface's shot representation.
- Execution tracking should work through the adapter.
- Debugging/snapshot functionality should remain available when the underlying device supports it.
- The adapter's preprocessing pipeline should include the legacy device's batch transform, expansion function, and mid-circuit measurement deferral (unless the device natively supports mid-circuit measurements).
- Post-select mode should be forwarded to the underlying device when it supports mid-circuit measurements.
- Batches of circuits with different shot counts should be handled correctly, with each circuit executed individually and tracked separately.
- Gradient computation should be supported when the underlying device declares the corresponding capability: device-native Jacobians, adjoint differentiation, or backpropagation via pass-through devices.
- Attempting to use a differentiation method the underlying device does not support should produce a clear error.
- Backpropagation should not be offered for circuits that include sparse Hamiltonian measurements.

## Why This Matters

Without this adapter, users who want to migrate to the new device interface would have to rewrite existing legacy devices from scratch. The adapter allows a smooth transition by bridging both interfaces, enabling gradients, tracking, preprocessing, and all other new-interface features to work with legacy devices without modification.
