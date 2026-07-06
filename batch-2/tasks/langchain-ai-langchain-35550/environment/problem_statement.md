## Description

When defining tool argument schemas that include fields with lazy/factory-based defaults (e.g., a list or dictionary that should be initialized fresh for each call), those fields are incorrectly marked as required in the generated JSON schema. This means AI models see these optional arguments as mandatory, causing them to attempt to supply values for parameters that should have working defaults.

## Expected Behavior

- A tool argument schema field that has a factory-based default should be treated as optional — it must **not** appear in the required list of the generated schema.
- The internal utility that builds subset models from an existing model should preserve factory-based defaults from the original model's fields so that the "required" designation is correct in the resulting schema.
- Only fields with no default at all (neither a static value nor a factory) should be listed as required.

## Why This Matters

Incorrect "required" metadata in tool schemas breaks tool calling workflows: the AI model is told it must supply values for arguments that the developer intentionally gave defaults, leading to avoidable validation errors and worse tool-calling behavior.
