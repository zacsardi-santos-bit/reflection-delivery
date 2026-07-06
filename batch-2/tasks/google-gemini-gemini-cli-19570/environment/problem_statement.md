## Description

When the assistant is switched into the fully-automatic approval mode (where all tool actions are approved without user confirmation), two inconsistencies exist compared to how other special modes behave:

1. **Stale system instructions**: Entering the fully-automatic mode does not trigger a refresh of the system instructions, unlike entering plan mode, which always triggers a refresh. This can leave the active system instructions misaligned with the current operating mode.

2. **Unnecessary plan-entry tool**: The tool that lets the assistant switch into a planning workflow remains registered and available even when already in the fully-automatic mode. Since this mode bypasses all approval requirements, offering the ability to enter plan mode is redundant and potentially confusing.

## Expected Behavior

- Switching into the fully-automatic approval mode should refresh system instructions, consistent with how entering and exiting plan mode works.
- The plan-entry tool should not be registered when the assistant is operating in fully-automatic approval mode, even if the planning feature is otherwise enabled in the configuration.
- Switching between other non-special modes (those that are neither the planning mode nor the fully-automatic mode) should continue to leave system instructions unchanged.

## Why This Matters

These inconsistencies mean that the assistant's behavior and available tools may not accurately reflect the current approval mode. Fixing this ensures the assistant's instructions and available capabilities are always consistent with the active mode.
