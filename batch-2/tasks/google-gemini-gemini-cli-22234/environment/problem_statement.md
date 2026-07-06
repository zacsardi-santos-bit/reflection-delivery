## Description

When the just-in-time context mode is active, the project memory content is already injected as part of the system instructions. However, the same content is also being included again in the environment context that gets sent to the model, causing it to appear twice in the full context window.

## Expected Behavior

- When the just-in-time context mode is enabled, the environment memory content should be **excluded** from the environment context, since it is already present in the system instructions.
- When the just-in-time context mode is disabled, the environment memory content should continue to appear in the environment context as before.

## Why This Matters

Duplicating the project memory wastes valuable context space and can confuse the model by repeating the same instructions twice. The environment context builder should be aware of whether JIT context is active and skip the memory content accordingly to avoid this redundancy.
