Implement a unified schema object for tools in LangChain core to optimize schema access and cache management. Ensure the schema is recomputed only when necessary and handles optional fields and uncommon collection types correctly.

*   Implement the `ToolSchema` class in `libs/core/langchain_core/tools/schema.py`.
    *   Ensure it is importable from `langchain_core.tools`.
    *   Include attributes:
        *   `name: str` - the tool's name.
        *   `approximate_chars: int` - pre-computed character count for token estimation.
    *   Implement the method `validate_python(data: Any) -> Any` to validate and coerce input data.

*   Update tool objects to expose a `tool_schema` property in `libs/core/langchain_core/tools/base.py`.
    *   Ensure `tool_schema` returns a `ToolSchema` instance.
    *   Maintain cache identity on repeated accesses.
    *   Invalidate cache when the tool's name or description changes, returning a new `ToolSchema` object with the updated name.

*   Ensure `ToolSchema`'s `approximate_chars` attribute matches the tool's `_approximate_schema_chars` attribute.

*   Modify `_convert_typed_dict_to_openai_function` in `libs/core/langchain_core/utils/function_calling.py` to:
    *   Exclude `NotRequired` fields from the "required" list but include them in "properties".
    *   Include fully required fields in "required".
    *   Handle `typing.MutableSet` fields without raising a `TypeError`, ensuring they appear in "properties".

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.