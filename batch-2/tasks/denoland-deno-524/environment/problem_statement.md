## Description

The Deno runtime currently has no dedicated module for parsing command-line flags. Argument handling is scattered across the codebase, and there is no structured representation of the flags that control runtime behavior. This makes it difficult to cleanly separate user-facing flags (such as enabling debug logging, reloading cached resources, allowing file writes, or allowing network access) from script arguments passed to user code.

Additionally, there is no clean layer that intercepts certain flags before they reach the underlying V8 engine. The help flag, for example, should be consumed by the runtime rather than forwarded to V8, while the option that requests V8's own help output should be translated into the form that V8 actually understands.

## Expected Behavior

- A new module should parse all recognized runtime flags from the argument list and return a structured representation of the enabled flags alongside the remaining non-flag arguments.
- Recognized flags should include: enabling debug logging, toggling module reload, granting write access, granting network access, showing version info, and showing help.
- A preprocessing step should separate flags intended for the runtime from those destined for the underlying engine, translating or filtering as appropriate.

## Why This Matters

Without this, runtime configuration is inconsistent and hard to extend. A clean flags module gives the runtime a single source of truth for all command-line options, making it straightforward to add new flags and ensuring user-facing options do not leak into the engine in unexpected forms.
