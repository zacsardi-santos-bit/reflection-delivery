I'm using agent capabilities that let me filter or modify the list of tools before each model request.

*   When a prepare callback passed to `PreparedToolset`, `PrepareTools`, or `PrepareOutputTools` returns `None`, the framework must raise a `UserError` (not a `TypeError`).

*   The `UserError` raised when a prepare callback returns `None` must have the exact message `"Prepare function '<name>' returned \`None\``" where `<name>` is the `__name__` attribute of the callback function (e.g., `"Prepare function 'invalid' returned \`None\``" for a callback named `invalid`).

*   The callable type accepted by `PrepareTools` and `PrepareOutputTools` for the prepare callback parameter must have a return type of `list[ToolDefinition]` (not `list[ToolDefinition] | None`); passing a callback whose return type includes `None` must be a Pyright `reportArgumentType` error.

*   The callable type accepted by `PreparedToolset` for the prepare function parameter must have a return type of `list[ToolDefinition]` (not `list[ToolDefinition] | None`); tests that intentionally pass a `None`-returning callback must suppress the resulting type error with `# pyright: ignore[reportArgumentType]`.


*   Interface details: Type: Class
Name: PrepareTools
Location: pydantic_ai_slim/pydantic_ai/capabilities/prepare_tools.py
Description: Capability that filters or modifies the list of function tools before each model request. The prepare callback parameter must accept only callbacks returning `list[ToolDefinition]` (not `list[ToolDefinition] | None`). When the callback returns `None`, a `UserError` must be raised with message `"Prepare function '<callback.__name__>' returned \`None\`"`.

Type: Class
Name: PrepareOutputTools
Location: pydantic_ai_slim/pydantic_ai/capabilities/prepare_tools.py
Description: Capability that filters or modifies the list of output tools before each model request. The prepare callback parameter must accept only callbacks returning `list[ToolDefinition]` (not `list[ToolDefinition] | None`). When the callback returns `None`, a `UserError` must be raised with message `"Prepare function '<callback.__name__>' returned \`None\`"`.

Type: Class
Name: PreparedToolset
Location: pydantic_ai_slim/pydantic_ai/toolsets/prepared.py
Description: Toolset wrapper that applies a prepare function to filter or modify tool definitions. The prepare function parameter type must be narrowed to return `list[ToolDefinition]` (not `list[ToolDefinition] | None`). The `get_tools()` method must raise `UserError` with message `"Prepare function '<callback.__name__>' returned \`None\`"` when the prepare function returns `None`.

Type: TypeAlias
Name: ToolsPrepareFunc
Location: pydantic_ai_slim/pydantic_ai/tools.py
Description: Type alias for a callable that takes a `RunContext` and `list[ToolDefinition]` and returns the tool definitions to expose. The return type must be narrowed to `Awaitable[list[ToolDefinition]] | list[ToolDefinition]` (removing `| None` variants). This is the type used for the prepare callback parameter in `PrepareTools`, `PrepareOutputTools`, and `PreparedToolset`.

Note: The exact error message format is `"Prepare function '<name>' returned \`None\`"` where `<name>` is the actual `__name__` of the callback — for example, `"Prepare function 'invalid' returned \`None\`"` for a callback function named `invalid`, and `"Prepare function 'prepare_returns_none' returned \`None\`"` for a callback named `prepare_returns_none`.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.