## Description

The TUI component currently imports many types and utilities directly from the core library. This creates a direct dependency between the two layers, which prevents clean separation of the architectural boundary between the UI and the underlying agent/session logic.

We want to introduce a transitional compatibility layer — a re-export module — so the TUI no longer holds a direct dependency on the core library. Instead, TUI code should access the same types through this intermediate shim. This allows us to enforce the boundary between layers and gradually migrate legacy startup and configuration paths toward the proper protocol-based communication channel.

## Expected Behavior

- The TUI package exposes a compatibility module that re-exports all the types and utilities that were previously imported directly from the core library.
- All TUI source files (including those used in tests) access these types through the compatibility module instead of through a direct core library import.
- The core library is no longer listed as a direct dependency of the TUI package.

## Why This Matters

Enforcing this boundary ensures that the TUI and core library can be evolved independently. It also improves discoverability — developers know that anything marked as "legacy" in the compatibility module is a transitional path that should eventually be replaced with a proper RPC or protocol-based alternative. Keeping the direct dependency prevents future tooling and CI checks from catching accidental boundary violations.
