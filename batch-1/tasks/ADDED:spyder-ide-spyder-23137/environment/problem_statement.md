## Description

Keyboard shortcut defaults provided by plugins are not validated when the configuration is first loaded. Each shortcut key must follow a specific two-part format that separates the context (e.g., which plugin or editor area the shortcut applies to) from the action name. Currently, if a developer accidentally provides a shortcut key that is missing the separator, or has too many separators, the configuration is accepted silently and the error only surfaces later as a confusing runtime failure.

## Expected Behavior

- When initializing the user configuration with a set of shortcut defaults, the system should immediately validate that every shortcut key conforms to the required format of exactly one separator between context and name.
- If any shortcut key is missing the separator (no context prefix), an error should be raised right away.
- If any shortcut key has more than one separator (ambiguous context/name boundary), an error should be raised right away.

## Why This Matters

Plugin developers who accidentally provide malformed shortcut keys currently get no feedback at configuration load time. The mistake manifests as a runtime error elsewhere, making it hard to diagnose. Early validation with a clear error message at initialization time makes it far easier to catch and fix shortcut configuration bugs during development.
