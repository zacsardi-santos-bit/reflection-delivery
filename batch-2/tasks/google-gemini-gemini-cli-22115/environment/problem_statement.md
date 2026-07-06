## Description

The current codebase requires callers to invoke separate getter methods to retrieve the tool registry, message bus, and AI client from a configuration object during an agent loop. This is inconsistent — different parts of the system call different methods, and there is no unified contract for what an "agent loop context" must expose. This makes it harder to share these runtime dependencies across schedulers, safety checkers, and prompt builders without tightly coupling everything to the full configuration class.

## Expected Behavior

- A new unified context interface should be introduced that exposes the tool registry, message bus, AI client, the configuration object itself, and the current prompt/session identifier as direct properties (not methods).
- The main configuration class should be assignable to this new interface, meaning it must expose those same dependencies as direct properties in addition to (or instead of) the existing getter methods.
- The tool registry should also expose the message bus as a direct property.
- The tool scheduler component should accept this unified context interface as its primary input rather than accepting the full configuration object under the key it previously used.
- The safety checker component's setup method should be renamed to reflect that it accepts a context object rather than just configuration.

## Why This Matters

These changes allow different consumers — schedulers, safety checkers, prompt utilities — to interact with agent loop state through a consistent, property-based interface rather than each component needing to know which specific getter method to call. This reduces coupling and makes the contract between components clearer.
