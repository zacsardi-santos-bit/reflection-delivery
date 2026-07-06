## Description

Agola currently supports pipeline configurations written in JSON, YAML, and Jsonnet formats. However, there is no support for Starlark — a Python-like scripting language designed for use as a configuration language. Adding Starlark support would allow users to write dynamic, programmable pipeline definitions using a familiar scripting syntax.

## Expected Behavior

- Users should be able to place a Starlark script in their repository's `.agola/` directory with a `.star` extension, and the system should recognize and execute it.
- Starlark configs should receive the current build context (branch, tag, ref type, commit SHA, pull request ID, etc.) just like Jsonnet configs do today, allowing dynamic configuration based on the triggering event.
- The system should convert the output of the Starlark script to the internal JSON representation used for pipeline configuration.
- The Starlark-to-JSON conversion utility should correctly handle all relevant value types: dictionaries (with string keys only), lists, strings (including those with special characters), integers, floats, booleans, and null values.
- If a Starlark dictionary uses a non-string key, the conversion should fail with a clear error message.

## Why This Matters

Supporting Starlark gives users a third scripting option for dynamic configurations, alongside Jsonnet. Users familiar with Python-like syntax can now write expressive, reusable pipeline definitions without learning Jsonnet, while still having access to the full build context at configuration time.
