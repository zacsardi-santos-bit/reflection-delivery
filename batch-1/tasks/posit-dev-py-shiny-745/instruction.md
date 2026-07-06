Implement a helper function and utility to manage select input widget configurations and JavaScript expression identification.

*   Implement the `_update_options` function in `shiny/ui/_input_select.py` with the following behavior:
    *   Accept parameters: `options` (dict), `remove_button` (bool), `multiple` (bool).
    *   Return the options dict unchanged if `remove_button` is False.
    *   Add `"remove_button"` to the `"plugins"` list if `remove_button` is True and `multiple` is True, ensuring no duplicates.
    *   Add `"clear_button"` to the `"plugins"` list if `remove_button` is True and `multiple` is False, ensuring no duplicates.
    *   Create a `"plugins"` key with the appropriate plugin if it does not exist when a plugin must be added.
    *   Preserve all pre-existing plugins and non-plugin keys in the options dict.

*   Implement the `js_eval` callable in `shiny/ui/_utils.py`:
    *   Wrap a string to mark it as a JavaScript expression.
    *   Ensure it is distinguishable from a plain string value in options dictionaries.

*   Implement the `extract_js_keys` function in `shiny/ui/_utils.py`:
    *   Accept a nested dictionary and return a flat list of dot-notation paths to keys whose values are `js_eval`-wrapped instances.
    *   Concatenate ancestor and descendant key names with a `.` separator for deeply nested dicts.
    *   Exclude keys with plain string values or sub-dict values from the result.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.