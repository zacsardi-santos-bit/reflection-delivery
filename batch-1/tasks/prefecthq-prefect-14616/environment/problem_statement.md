## Description

The logic responsible for tracking causal relationships between events — determining whether one event has arrived before the event it depends on, holding early-arriving events, and recovering events whose predecessors never showed up — is currently embedded directly inside the automation trigger processing module. This tight coupling makes it impossible to use the ordering logic independently, and multiple consumers cannot maintain separate ordering contexts without interfering with each other.

## Expected Behavior

- The causal event ordering system should be extracted into its own dedicated module, separate from the automation triggers module.
- The ordering component should be instantiated with a named scope so that multiple independent instances can coexist without sharing any state.
- The new module should expose the necessary constants, exceptions, and the ordering class so other parts of the system (including the triggers module) can import and use them.
- The triggers module should provide a way for external callers to retrieve the shared ordering instance it uses internally.

## Why This Matters

Separating the causal ordering concern into its own component makes it easier to test independently, reuse across different parts of the system, and reason about. Scoping instances also prevents subtle bugs where two consumers accidentally share ordering state, causing events in one context to appear "already seen" in another.
