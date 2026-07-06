I'm working with an AI agent framework that supports tool deferral — where a tool can signal that it needs to be executed later or that it requires human approval before proceeding.

*   The CallDeferred and ApprovalRequired exception classes already exist in pydantic_ai.exceptions and must remain importable from that location. Each has an optional 'metadata' attribute (a dict or None).

*   The DeferredToolRequests type already exists in pydantic_ai.tools and must remain importable from that location.

*   InstrumentationSettings must accept version=5 as a valid version value (extending the existing Literal[1, 2, 3, 4] to Literal[1, 2, 3, 4, 5]).

*   When a tool raises CallDeferred or ApprovalRequired during execution, the instrumented tool span must include a 'pydantic_ai.tool.deferral.name' attribute set to the string class name of the raised exception ('CallDeferred' or 'ApprovalRequired').

*   When the deferral exception's metadata is not None, the span must also include a 'pydantic_ai.tool.deferral.metadata' attribute. The value must be the metadata serialized as a JSON string if the metadata is JSON-serializable; otherwise it must fall back to repr(metadata) as a string.

*   When the deferral exception has no metadata (metadata is None), the 'pydantic_ai.tool.deferral.metadata' attribute must NOT be present on the span at all.

*   Under v5 instrumentation (InstrumentationSettings(version=5)), when a tool raises CallDeferred or ApprovalRequired, the span must NOT record an exception event and must NOT set the span status to ERROR — the span status should remain UNSET. The span ends without an error-level flag.

*   Under v2 instrumentation (InstrumentationSettings(version=2)), when a tool raises CallDeferred or ApprovalRequired, the span must record an exception event (with the exception type set to 'pydantic_ai.exceptions.CallDeferred' or 'pydantic_ai.exceptions.ApprovalRequired') and must set the span status to ERROR (logfire.level_num=17).


*   Interface details: The following symbols already exist in the codebase and do NOT need to be created — they must simply be importable from the indicated locations:

- `CallDeferred` from `pydantic_ai.exceptions` (already exists; has optional `metadata` attribute)
- `ApprovalRequired` from `pydantic_ai.exceptions` (already exists; has optional `metadata` attribute)
- `DeferredToolRequests` from `pydantic_ai.tools` (already exists)

The following changes must be made to existing code:

Type: Class (modification)
Name: InstrumentationSettings
Location: pydantic_ai_slim/pydantic_ai/models/instrumented.py
Description: The `version` parameter of InstrumentationSettings must be extended to accept `5` as a valid version value (in addition to 1, 2, 3, 4). InstrumentationSettings(version=5) must be a valid instantiation.
Signature: InstrumentationSettings(version: Literal[1, 2, 3, 4, 5] = ..., ...)

Type: Span attributes (new behavior in instrumentation)
Name: pydantic_ai.tool.deferral.name
Location: pydantic_ai_slim/pydantic_ai/_tool_manager.py (or equivalent instrumentation file)
Description: When a tool raises CallDeferred or ApprovalRequired, the tool execution span must have a 'pydantic_ai.tool.deferral.name' string attribute set to the class name of the raised exception ('CallDeferred' or 'ApprovalRequired').

Type: Span attributes (new behavior in instrumentation)
Name: pydantic_ai.tool.deferral.metadata
Location: pydantic_ai_slim/pydantic_ai/_tool_manager.py (or equivalent instrumentation file)
Description: When the deferral exception has non-None metadata, the tool execution span must have a 'pydantic_ai.tool.deferral.metadata' attribute. The value is the metadata serialized as a JSON string if possible; otherwise falls back to repr(metadata). This attribute must NOT be set when metadata is None.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.