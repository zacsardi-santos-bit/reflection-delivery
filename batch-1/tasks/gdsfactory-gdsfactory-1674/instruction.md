Implement the serialization of the default waveguide cross-section to include its name and pin configuration in the output. Ensure that when component settings are serialized to YAML, the cross-section's name and pin configuration are fully captured.

*   Define the default 'strip' cross-section with:
    *   A 'name' parameter set to 'strip'.
    *   An 'add_pins' parameter configured with the rectangular-inside pin function.
*   Ensure that when a component's settings are serialized:
    *   The cross-section derived from the default strip configuration includes a 'name' field with the value 'strip'.
    *   The 'add_pins' block is fully expanded in the settings output, reflecting the pin-drawing callback.
*   Include in the serialized 'add_pins' block:
    *   Nested settings such as 'layer_label: null' and 'pin_length: 0.001'.
*   Update component names (hash-based identifiers) for components using the strip cross-section to reflect the inclusion of 'name' and 'add_pins' in the cross-section settings.
*   Ensure the 'disk_heater' component port center coordinates are accurate to three decimal places (e.g., -22.551 and 33.251).

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.