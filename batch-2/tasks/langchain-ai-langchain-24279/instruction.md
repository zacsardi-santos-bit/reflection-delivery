Implement a mechanism to annotate tool arguments as "injected at runtime" to prevent them from being included in the schema exposed to language models. Ensure that these arguments are excluded from model tool-calling schemas but included in the full input schema for application use. Handle tool invocation with and without these arguments appropriately.

*   Create a public class `InjectedToolArg` in `libs/core/langchain_core/tools.py`:
    *   Use it as a class reference or instance within `Annotated` type hints to mark arguments as runtime-injected.
    *   Ensure it is importable via `from langchain_core.tools import InjectedToolArg`.

*   Update `BaseTool` in `libs/core/langchain_core/tools.py`:
    *   Implement a `tool_call_schema` property that returns a `Type[BaseModel]`:
        *   Exclude fields annotated with `InjectedToolArg` from `properties` and `required`.
        *   Set schema title to the tool's name and description to the tool's description.

*   Ensure `get_input_schema()` returns all arguments, including those with `InjectedToolArg`:
    *   For `BaseTool` subclasses without explicit `args_schema`, set schema title to '{name}Schema' and description to the full raw docstring.

*   Allow tools to be invoked with all arguments provided, returning the expected result:
    *   Support invocation via a tool-call dictionary with keys 'name', 'args', 'id', and 'type', returning a `ToolMessage`.

*   Handle errors when invoking tools without required injected arguments:
    *   Raise `TypeError` for `BaseTool` subclasses without explicit `args_schema`.
    *   Raise `ValidationError` for tools with explicit `args_schema`, including those created with the `@tool` decorator.

*   Modify `convert_to_openai_function` in `libs/core/langchain_core/utils/function_calling.py`:
    *   Use `tool_call_schema` instead of `args_schema` to build the OpenAI function parameters, ensuring injected arguments are absent from the 'parameters' dict, 'properties', and 'required' list.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.