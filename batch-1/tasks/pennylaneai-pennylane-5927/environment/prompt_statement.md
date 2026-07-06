I'm working on a project that uses some legacy quantum devices built with PennyLane's older device interface, and I'd like to use them with the newer device interface without rewriting everything. I need an adapter class that wraps a legacy device and makes it compatible with the new interface.

The adapter should expose the underlying device's properties (like its name, author, version, and wires) through the new interface, and translate shot configuration — including partitioned shots — into the new interface's shot representation. It should also forward tracking, so things like execution counts and shot totals are captured correctly.

The preprocessing pipeline for the adapter should bridge the legacy expansion and batch-transform functions into the new transform pipeline. It should also defer mid-circuit measurements unless the underlying device natively supports them, and pass along post-selection mode to the underlying device when appropriate.

When executing a batch of circuits with different shot counts, each circuit should be handled individually, with each circuit's shots tracked separately.

The adapter should also handle gradient computation correctly. If the underlying device supports adjoint differentiation, the adapter should configure the execution for it (with the appropriate gradient keyword arguments). If the device provides its own Jacobian, the adapter should recognize that capability. For backpropagation via pass-through devices, the adapter should substitute the appropriate pass-through device at execution time — but only when needed, and not for circuits containing sparse Hamiltonian observables. Attempting to use a differentiation method the underlying device doesn't support should raise a clear device error.

Finally, snapshot/debugger functionality should remain accessible through the adapter when the underlying device supports it.
