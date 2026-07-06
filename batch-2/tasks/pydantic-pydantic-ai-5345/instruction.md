I'm working on the pydantic-ai library and we're preparing for a major version that will remove some of the older convenience APIs for our UI protocol integration layer.

*   When `pydantic_ai.ag_ui` is imported, it must emit a `PydanticAIDeprecationWarning` with a message containing the text '`pydantic_ai.ag_ui` module is deprecated' (the full message starts with 'The ').

*   When `Agent.to_ag_ui()` is called on any agent instance, it must emit a `PydanticAIDeprecationWarning` with a message containing the text '`Agent.to_ag_ui()` is deprecated'.

*   When `AGUIApp` is instantiated (with or without extra keyword arguments), it must emit a `PydanticAIDeprecationWarning` with a message containing the text '`AGUIApp` is deprecated'.

*   The `pydantic_ai.ui` module must export `SSE_CONTENT_TYPE`, `OnCompleteFunc`, and `StateDeps` so they can be imported directly from `pydantic_ai.ui`.

*   The `pydantic_ai.ui.ag_ui` module must export both `AGUIAdapter` and `AGUIEventStream` so they can be imported from `pydantic_ai.ui.ag_ui`.

*   The `pydantic_ai.ag_ui` module must remain functional after the deprecation warning, continuing to export `handle_ag_ui_request` and `run_ag_ui` for backward compatibility.

*   `PydanticAIDeprecationWarning` must be importable from `pydantic_ai._warnings` and must be a subclass of the standard `DeprecationWarning` (so pytest.warns can match on it by category).


*   Interface details: Type: Module
Name: pydantic_ai.ag_ui
Location: pydantic_ai_slim/pydantic_ai/ag_ui.py (or pydantic_ai_slim/pydantic_ai/ag_ui/__init__.py)
Description: Must emit `PydanticAIDeprecationWarning` at module import time with a message beginning with "The " and containing the text "`pydantic_ai.ag_ui` module is deprecated". Must still export `handle_ag_ui_request` and `run_ag_ui` for backward compatibility.

Type: Method
Name: to_ag_ui
Location: pydantic_ai_slim/pydantic_ai/agent.py
Signature: to_ag_ui(self, ...) -> Any
Description: Method on the `Agent` class. Must emit `PydanticAIDeprecationWarning` with a message containing "`Agent.to_ag_ui()` is deprecated" whenever it is called.

Type: Class
Name: AGUIApp
Location: pydantic_ai_slim/pydantic_ai/ui/ag_ui/app.py
Description: When instantiated (e.g. `AGUIApp(agent)` or `AGUIApp(agent, builtin_tools=[...])`), must emit `PydanticAIDeprecationWarning` with a message containing "`AGUIApp` is deprecated".

Type: Class
Name: PydanticAIDeprecationWarning
Location: pydantic_ai_slim/pydantic_ai/_warnings.py
Description: The custom deprecation warning class used for all pydantic-ai deprecation warnings. Must be importable from `pydantic_ai._warnings`. Tests use `pytest.warns(PydanticAIDeprecationWarning, match=...)` and pytest filterwarnings with the fully-qualified category `pydantic_ai._warnings.PydanticAIDeprecationWarning`.

Type: Module
Name: pydantic_ai.ui
Location: pydantic_ai_slim/pydantic_ai/ui/__init__.py
Description: Must export `SSE_CONTENT_TYPE`, `OnCompleteFunc`, and `StateDeps` so they can be imported directly as `from pydantic_ai.ui import SSE_CONTENT_TYPE, OnCompleteFunc, StateDeps`.

Type: Module
Name: pydantic_ai.ui.ag_ui
Location: pydantic_ai_slim/pydantic_ai/ui/ag_ui/__init__.py
Description: Must export `AGUIAdapter` and `AGUIEventStream` so they can be imported as `from pydantic_ai.ui.ag_ui import AGUIAdapter, AGUIEventStream`.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.