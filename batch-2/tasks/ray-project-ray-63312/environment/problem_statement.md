## Description

The GPU profiling manager can stall indefinitely when checking whether a node has GPUs, and it performs this potentially-stalling check even when the GPU profiling tool is not installed.

Two related issues exist:

1. **FabricManager hang**: When detecting GPU presence, the code invokes a system utility in a way that triggers communication with NVIDIA's FabricManager service. On nodes where FabricManager is not responding properly, this call can block indefinitely — stalling the entire node process. The fix is to use a more targeted query mode of the utility that avoids contacting FabricManager.

2. **Unnecessary GPU check when profiling tool is absent**: The GPU detection runs even when the required GPU profiling tool is not installed. Since the profiling manager cannot do anything useful without it, this unnecessary call risks triggering the FabricManager hang on nodes that are missing the tool — which is wasteful and potentially harmful.

## Expected Behavior

- The GPU detection command must use targeted flags that query only GPU names in a simple, parseable format, avoiding any interaction with FabricManager.
- When the required GPU profiling tool binaries are absent, the profiling manager's enabled check must short-circuit and return disabled without ever running the GPU detection step.

## Why This Matters

Nodes in GPU clusters can become unresponsive during startup because of the hanging GPU detection call. Fixing the query flags and the evaluation order ensures that the profiling setup does not cause node stalls.
