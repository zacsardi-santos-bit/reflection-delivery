Fix the schema generation so that fields with factory-based defaults are not incorrectly marked as required. Ensure that fields with default_factory are treated as optional in the generated JSON schema.

*   Update the `_create_subset_model_v2` function in `libs/core/langchain_core/utils/pydantic.py`:
    *   Preserve the `default_factory` from the original model's field when creating the subset model.
    *   Ensure that if a field in the original model has a `default_factory` (not `None`), the subset model's corresponding field uses that `default_factory` instead of a static default.
*   Modify the JSON schema generation:
    *   Include in the 'required' list only fields with no default at all.
    *   Exclude fields with a `default_factory` from the 'required' list.
*   When converting a tool to an OpenAI tool schema:
    *   Ensure the resulting schema's function parameters do not list fields with `default_factory` under 'required'.
    *   Only fields with no default value of any kind should be marked as required.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.