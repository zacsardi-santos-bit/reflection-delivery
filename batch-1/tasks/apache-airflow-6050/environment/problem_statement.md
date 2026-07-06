## Description

The Google Cloud authentication hook currently provides credential setup only through a function decorator. This means credentials can only be scoped to an entire decorated function — there is no way to apply the same credential setup to an arbitrary block of code, or to scope credential availability to just part of a function (e.g. a specific call to an external tool).

This also causes issues in operators that manage their own credential setup logic with private helper methods, duplicating credential handling code that already exists in the base hook.

## Expected Behavior

- The base GCP hook should expose a context manager interface for credential setup, in addition to the existing decorator. This context manager should set up the appropriate credential environment variable when entering the block (using a key file path or writing JSON content to a temporary file), and restore the environment to its original state when the block exits — whether normally or due to an exception.
- If the credential environment variable was not set before entering the context, it should be absent again after the context exits.
- Operators that currently maintain their own credential helper methods should be updated to use this shared context manager, removing the duplicated logic.

## Why This Matters

Having credential management available as a context manager makes it much easier to scope credentials precisely — such as for the duration of a subprocess call — and allows multiple connections to be used within the same function. It also reduces code duplication by allowing operators to delegate credential handling to the base hook instead of reimplementing it themselves.
