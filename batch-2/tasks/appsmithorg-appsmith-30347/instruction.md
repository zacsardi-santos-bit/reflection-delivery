Ensure the REST API plugin transmits JSON bodies exactly as configured when they are already valid JSON. Avoid re-serialization to preserve key order and number types. Implement logic to handle non-strict JSON by normalizing it through parsing and re-serialization.

*   Transmit valid JSON bodies exactly as configured:
    *   Preserve key order, number types, and string values.
    *   Avoid re-serialization for valid JSON inputs.
*   Handle plain number bodies:
    *   Transmit numbers unchanged, maintaining their original format.
*   Re-serialize non-strict JSON bodies:
    *   Parse and re-serialize bodies with syntax issues (e.g., trailing commas).
    *   Allow key reordering in re-serialized output.
*   Manage request headers for non-JSON content types:
    *   Ensure 'Content-Type' header appears before custom headers in serialized output when wrapping requests.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.