Implement enhancements to the Keras dtype policy system to improve usability and API design. Ensure that policies can inherit from global settings, differentiate quantized from non-quantized policies, and serialize configurations more explicitly.

*   Update `FloatDTypePolicy` and `DTypePolicy`:
    *   Allow initialization with `name=None` to inherit from the current global dtype policy.
    *   Add an `is_quantized` property that always returns `False`.
    *   Ensure `get_config()` returns a dictionary with the key `"name"`.

*   Update `QuantizedDTypePolicy`:
    *   Change constructor to `__init__(self, mode: str, source_name=None)`.
        *   Validate `mode` and raise `ValueError` with "Invalid quantization mode." for unsupported modes.
        *   Use the global dtype policy name if `source_name` is `None`.
        *   Raise `ValueError` with "doesn't work well" for incompatible `mode` and `source_name` combinations.
    *   Add an `is_quantized` property that returns `True`.
    *   Ensure `get_config()` returns a dictionary with keys `"mode"` and `"source_name"`.

*   Update `QuantizedFloat8DTypePolicy`:
    *   Change constructor to `__init__(self, mode: str, source_name=None, amax_history_length: int = 1024)`.
        *   Validate `amax_history_length` is an integer, raising `TypeError` with "must be an integer." otherwise.
    *   Add an `is_quantized` property that returns `True`.
    *   Ensure `get_config()` returns a dictionary with keys `"mode"`, `"source_name"`, and `"amax_history_length"`.
    *   Include a class-level attribute `default_amax_history_length` set to `1024`.

*   Ensure all policy types support `copy.deepcopy`, `copy.copy`, and `pickle` serialization, preserving all properties.

*   Update the `get` function:
    *   Return a properly initialized policy object for valid identifiers.
    *   Raise `ValueError` with "Cannot convert `policy` into a valid pair" for malformed quantized strings.
    *   Raise `ValueError` with "Cannot interpret `dtype` argument." for non-string, non-policy inputs.

*   Update the `serialize` function:
    *   Serialize any dtype policy object into a config dictionary.
    *   Ensure layers use this serialized form in their `get_config()` methods for the `"dtype"` field.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.