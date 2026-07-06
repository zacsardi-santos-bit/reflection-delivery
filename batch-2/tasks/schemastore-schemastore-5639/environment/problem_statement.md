## Description

There is currently no JSON schema available for the yamlfmt configuration file format in SchemaStore. As a result, developers who use yamlfmt in their projects get no editor autocompletion, no inline validation, and no helpful feedback when they write configuration files. Invalid settings — such as unsupported values for line ending style, output format, or path matching mode — go undetected until runtime.

## Expected Behavior

- A schema should validate top-level configuration fields including line ending style, path matching strategy, output format, and related options, rejecting any unsupported values for those fields.
- A schema should validate formatter-specific settings, distinguishing between the default (basic) formatter type and the alternate formatter type, allowing each type only its own supported properties.
- When the alternate formatter type is selected, any property that belongs exclusively to the default formatter type should be flagged as invalid.
- Fields like the array formatting style and quote style should only accept their documented valid options; unrecognized values should be rejected.

## Why This Matters

Developers relying on yamlfmt in their CI pipelines or editor workflows currently have no way to catch typos or unsupported values in their configuration files until yamlfmt itself runs and reports an error. A schema enables instant feedback, reduces debugging time, and improves the overall developer experience when setting up and maintaining yamlfmt configurations.
