## Description

When converting a Cedar schema to human-readable (natural) format, actions that have no applies-to specification are incorrectly rendered with a spurious empty applies-to block. This is wrong: an action that carries no restriction on principals or resources should simply be declared by name with a terminating semicolon — no applies-to clause should appear at all.

## Expected Behavior

- An action with no applies-to constraint should be written in natural schema format as just the action declaration (name and semicolon) with nothing else attached.
- No empty or default applies-to block should be injected for such an action.
- The conversion should succeed without error.

## Why This Matters

This bug breaks schema round-tripping. A schema that defines a bare, unconstrained action cannot be faithfully serialized and then re-read because the serialized form gains content that was never there. Any tooling that relies on converting schemas to human-readable format and back will produce incorrect results for this case.
