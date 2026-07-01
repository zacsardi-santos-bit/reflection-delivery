## Description

The debug session feature currently relies on two separate standalone classes to handle the "select a host process" and "select a runspace" prompts when attaching the debugger. These classes work by registering their own VS Code commands, and the debug session configuration code calls those commands to retrieve user selections. This indirect communication path — bouncing through the VS Code command system between sibling components — makes the code harder to maintain, harder to test, and harder to reason about as a coherent unit.

The process picker and runspace picker functionality should be consolidated directly into the debug session feature as private methods. The debug configuration resolver should then invoke those methods directly on itself, rather than dispatching to external commands.

## Expected Behavior

- The debug session feature is self-contained for the attach-to-process workflow: it owns the logic for prompting the user to pick a host process and to pick a runspace.
- When attaching and no process identifier is provided, the feature internally calls its own process-picker method (not an external command). If the user cancels, the debug session is cancelled.
- When attaching and no runspace identifier is provided, the feature internally calls its own runspace-picker method with the resolved process ID. If the user cancels, the debug session is cancelled.
- The two previously standalone picker classes are removed; their functionality is merged into the debug session feature.

## Why This Matters

The refactoring simplifies the architecture, makes the attach-to-process flow more cohesive, and improves testability by allowing tests to stub the picker methods directly on the feature instance rather than intercepting the global VS Code command bus.
