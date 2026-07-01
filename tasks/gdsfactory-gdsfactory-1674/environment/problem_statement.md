## Description

The default waveguide cross-section is not fully capturing its configuration when component settings are serialized to YAML. Specifically, the cross-section's name identifier and its pin-placement callback settings are missing from the serialized output. This means that when a component's settings are saved or exported, the cross-section description is incomplete — you can see that a cross-section function was used, but not its identifying name or how pins should be drawn on it.

## Expected Behavior

- The default waveguide cross-section should be defined with its name identifier and pin-drawing configuration included as part of its definition.
- When any component using this cross-section has its settings serialized (for example, in regression snapshots or YAML netlists), the cross-section's name and pin configuration should appear in the output.
- The serialized cross-section settings should fully describe the cross-section so it can be reconstructed from its settings alone.

## Why This Matters

Without this fix, components appear to use the same cross-section even when they differ in pin configuration or name, making it impossible to distinguish cross-section variants from their serialized settings alone. This also causes incorrect component hash identifiers for components whose cross-section configuration was previously only partially captured.
