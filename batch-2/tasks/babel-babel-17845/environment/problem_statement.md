## Description

When using Babel's React JSX transformation in development mode, source location information (file name, line number, column number) and a reference to the current execution context are automatically injected into every JSX element. Currently there is no way to use development-mode JSX transformation without these extra annotations.

Some environments and toolchains want the development variant of JSX — which signals to React that code is running in debug mode — but do not need or want source location data injected into every element. For example, JSX generated programmatically rather than authored directly by humans often lacks meaningful source positions, making these annotations noise rather than useful debugging aids.

## Expected Behavior

- A new opt-in boolean option should control whether source location and context information is injected during development-mode JSX transformation.
- When this option is not set (the default), development-mode JSX transforms should produce lean output without any source location or context arguments.
- When this option is explicitly enabled, the existing behavior (including source location info and context) should be preserved.
- The option should be validated as a boolean, and passing a non-boolean value should produce a clear error message.
- The default normalized value of this option should be disabled (false).

## Why This Matters

This gives developers fine-grained control over what development-mode JSX transformation injects. It avoids unnecessary output bloat for codebases that generate JSX programmatically and don't benefit from source location annotations, while preserving the existing behavior for those who need it via explicit opt-in.
