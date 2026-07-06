I'm working on an AI agent operator in Airflow and I want to add support for a "code mode" that lets the model write a single block of code calling all the tools it needs in one turn, rather than making one model round-trip per tool call.

*   AgentOperator must accept a new boolean parameter code_mode (default False) and store it as self.code_mode; _build_code_mode must never be called during __init__.

*   When AgentOperator is constructed with both durable=True and code_mode=True, it must raise a ValueError whose message matches the pattern 'durable=True and code_mode=True'.

*   When AgentOperator executes with code_mode=False (the default), the capabilities keyword must not be present in the call to create_agent.

*   When AgentOperator executes with code_mode=True, it must call _build_code_mode() exactly once and pass capabilities=[<result>] to create_agent.

*   When AgentOperator executes with code_mode=True and agent_params already contains a capabilities list, existing capabilities must be preserved and the CodeMode capability appended: the final capabilities list passed to create_agent is [<existing...>, <CodeMode>].

*   _build_code_mode() must raise AirflowOptionalProviderFeatureException with a message matching 'code-mode' when pydantic_ai_harness itself is missing (the ModuleNotFoundError's name attribute equals 'pydantic_ai_harness' or starts with 'pydantic_ai_harness.').

*   _build_code_mode() must re-raise the original ModuleNotFoundError unchanged when the import failure is caused by a broken transitive dependency (i.e. the error name does not refer to pydantic_ai_harness itself).

*   The module airflow/providers/common/ai/utils/tool_definition.py must define a module-level boolean _SUPPORTS_RETURN_SCHEMA that is True when pydantic_ai ToolDefinition has a return_schema field, and False otherwise.

*   return_schema_kwargs(schema) must return {'return_schema': schema} when _SUPPORTS_RETURN_SCHEMA is True, and {} when _SUPPORTS_RETURN_SCHEMA is False.

*   HookToolset tool definitions must include return_schema={'type': 'string'} (via return_schema_kwargs) when _SUPPORTS_RETURN_SCHEMA is True.

*   SQLToolset tool definitions must include return_schema={'type': 'string'} (via return_schema_kwargs) when _SUPPORTS_RETURN_SCHEMA is True.


*   Interface details: Type: Function
Name: _build_code_mode
Location: providers/common/ai/src/airflow/providers/common/ai/operators/agent.py
Signature: _build_code_mode() -> Any
Description: Lazily imports and instantiates a CodeMode capability from pydantic_ai_harness. Raises AirflowOptionalProviderFeatureException (message must contain "code-mode") when pydantic_ai_harness itself is missing. Re-raises the original ModuleNotFoundError unchanged when the failure is caused by a broken transitive dependency (error name does not refer to pydantic_ai_harness itself).

Type: Class
Name: AgentOperator
Location: providers/common/ai/src/airflow/providers/common/ai/operators/agent.py
Description: Existing operator extended with a new code_mode parameter.
Signature: __init__(self, ..., code_mode: bool = False, ...) — new parameter only; all existing parameters unchanged. Stores self.code_mode = code_mode. Raises ValueError("durable=True and code_mode=True cannot be used together.") when both durable=True and code_mode=True are passed. Does NOT call _build_code_mode() during __init__. During agent build/execute: if code_mode is True, calls _build_code_mode() once, merges with any existing capabilities from agent_params (preserving them), and passes the combined list as capabilities= to create_agent. If code_mode is False, capabilities is not added to the create_agent call.

Type: Module
Name: tool_definition
Location: providers/common/ai/src/airflow/providers/common/ai/utils/tool_definition.py
Description: Version-compatibility helpers for building pydantic-ai ToolDefinition objects.

Type: Constant
Name: _SUPPORTS_RETURN_SCHEMA
Location: providers/common/ai/src/airflow/providers/common/ai/utils/tool_definition.py
Signature: _SUPPORTS_RETURN_SCHEMA: bool
Description: Module-level boolean. True when pydantic_ai.tools.ToolDefinition has a return_schema field (detected via dataclasses.fields); False otherwise.

Type: Function
Name: return_schema_kwargs
Location: providers/common/ai/src/airflow/providers/common/ai/utils/tool_definition.py
Signature: return_schema_kwargs(schema: dict[str, Any]) -> dict[str, Any]
Description: Returns {"return_schema": schema} when _SUPPORTS_RETURN_SCHEMA is True; returns {} when _SUPPORTS_RETURN_SCHEMA is False.

Type: Function (usage in existing class)
Name: HookToolset.get_tools  (modification)
Location: providers/common/ai/src/airflow/providers/common/ai/toolsets/hook.py
Description: When building each ToolDefinition, must pass **return_schema_kwargs({"type": "string"}) so that tool_def.return_schema == {"type": "string"} when _SUPPORTS_RETURN_SCHEMA is True.

Type: Function (usage in existing class)
Name: SQLToolset.get_tools  (modification)
Location: providers/common/ai/src/airflow/providers/common/ai/toolsets/sql.py
Description: When building each ToolDefinition, must pass **return_schema_kwargs({"type": "string"}) so that tool_def.return_schema == {"type": "string"} when _SUPPORTS_RETURN_SCHEMA is True.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.