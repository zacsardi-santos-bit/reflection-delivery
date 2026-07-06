## Description

The current monitoring/error system uses scattered boolean flags to control whether an error should be reported as an info-level event or suppressed entirely. This approach is inconsistent — different error classes use different boolean property names, and it's not clear or expressive enough to represent more than two states. We need a unified, named log-level concept across all error types.

## Expected Behavior

- A single named log-type concept should replace the boolean "should log as info" flags across all error classes
- The concept should distinguish between at least three states: a regular error capture, an info-level capture, and a fully suppressed/ignored capture
- All relevant error constructors should accept this new log-type parameter in place of the old boolean flags
- A helper that fetches email update status should accept the new log-type parameter instead of the old boolean
- A new reusable hook should bridge the remote feature flag that controls logging verbosity and the new log-type system, so that any component or function can easily get the correct log-type value without duplicating the translation logic

## Why This Matters

Developers currently have to track multiple slightly different boolean parameters across error types, making it easy to introduce inconsistencies or misuse the API. By consolidating into a shared named type with clear values, the intent at each call site becomes explicit, the API surface is easier to learn and use correctly, and runtime control of monitoring verbosity via remote configuration becomes straightforward and centralized.
