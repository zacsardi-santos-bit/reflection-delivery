## Description

Table functions in the query engine have a two-phase lifecycle: a **bind phase** that resolves input arguments and produces a bind state (including output schema and cardinality), and an **execution phase** that actually processes and emits data. Currently, the execution and scanning methods (for creating partition states, polling for output, and finalizing) have no access to the bind state — they only receive the operator state and partition state.

This limitation forces implementations to either duplicate bind-time data inside the operator state or to operate without it. There is also a design inconsistency: the bind function is currently an instance method, even though it has no meaningful reason to be tied to a specific struct instance.

## Expected Behavior

- The bind state produced during the bind phase should be passed as a parameter to all downstream execution and scanning methods: partition state creation, polling, and finalization.
- The bind function on both table execute functions and table scan functions should be a standalone (static) operation rather than an instance method.
- All existing table function implementations throughout the codebase must be updated to conform to the new signatures.
- The series generation table function must remain fully functional with the updated interface, including the ability to execute with valid start/stop/step inputs.

## Why This Matters

Passing the bind state through to execution gives implementations direct access to original configuration without needing to copy it into downstream states. This simplifies implementations, reduces redundancy, and opens the door for execution logic that depends on bind-time decisions.
