## Description

Select input widgets in the framework support optional button controls for removing or clearing selections. In multi-select mode a remove button lets users deselect individual items; in single-select mode a clear button lets users reset the entire selection. Currently there is no standardized helper for injecting the correct button plugin into the widget's configuration options, meaning developers who want this behavior and also want to supply their own custom configuration have no clean way to do both without the two concerns stepping on each other.

Additionally, when building widget configuration that may contain JavaScript expressions mixed with plain string values, there is no utility to identify which nested keys hold JavaScript expressions — information that is necessary for correct serialization to the browser.

## Expected Behavior

- A helper function should accept a configuration options dict plus flags indicating whether the remove/clear button is requested and whether the select allows multiple values, and return the updated dict with the appropriate button plugin appended to the plugin list.
- If the requested plugin is already present in the list, the function should not add a duplicate.
- All other keys and pre-existing plugins in the options dict must be preserved.
- A wrapper type should exist to mark a string value as a JavaScript expression rather than a literal string.
- A utility function should recursively walk a nested options dictionary and return a flat list of dot-notation paths to every key whose value is such a JavaScript expression.

## Why This Matters

Without these utilities, adding remove/clear button behavior to select inputs while respecting developer-supplied custom options requires fragile ad-hoc logic. The JavaScript-key extraction utility is similarly needed so that the framework can correctly distinguish and serialize JavaScript values embedded in nested configuration objects.
