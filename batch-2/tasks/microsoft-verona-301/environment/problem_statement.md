## Description

The runtime's build system has a configuration option that allows the custom memory allocator to be bypassed, passing allocation requests directly to the system allocator instead of using the built-in pool mechanism. The flag used to signal this mode is named after the generic system allocator, which is confusing — it doesn't make clear which library's behavior it's controlling or what "pass-through" means in context.

This flag should be renamed to something that more clearly identifies it as the allocator library's own pass-through setting, bringing the name in line with how the underlying allocator library itself refers to this mode.

## Expected Behavior

- The build flag controlling allocator pass-through mode should use the name the allocator library itself recognizes, rather than the generic system allocator name.
- Any code that conditionally compiles or executes based on whether allocator pass-through is active must reference the new flag name.
- Memory pool tests must correctly skip pool-specific checks when operating in pass-through mode, using the updated flag.
- A test for concurrent ownership weak references should use a reduced tree depth to reflect updated behavior in the underlying data structure.

## Why This Matters

Using the allocator library's own flag name avoids confusion, ensures the condition is correctly detected in all build configurations (such as when address sanitizer is enabled), and keeps the codebase consistent with the upstream allocator library's conventions.
