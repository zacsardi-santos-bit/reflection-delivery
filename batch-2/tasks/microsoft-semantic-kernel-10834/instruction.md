Refactor the OpenAI Realtime service code to improve module organization and encapsulation. Move internal utilities and event type definitions to a private module and ensure the orchestration object attribute is private.

*   Move internal utilities and event type definitions:
    *   Ensure the following symbols are importable from `semantic_kernel.connectors.ai.open_ai.services._open_ai_realtime`:
        *   `ListenEvents`
        *   `SendEvents`
        *   `_create_openai_realtime_client_event`
        *   `update_settings_from_function_call_configuration`
    *   Create or update the private module file at `python/semantic_kernel/connectors/ai/open_ai/services/_open_ai_realtime.py` to contain these symbols.

*   Maintain public API for service classes:
    *   Ensure the following classes remain importable from `semantic_kernel.connectors.ai.open_ai.services.open_ai_realtime`:
        *   `OpenAIRealtimeWebRTC`
        *   `OpenAIRealtimeWebsocket`
    *   The public module file is located at `python/semantic_kernel/connectors/ai/open_ai/services/open_ai_realtime.py`.

*   Encapsulate internal state:
    *   In the `OpenAIRealtimeWebsocket` class, rename the attribute holding the orchestration object to `_kernel` to indicate it is private.
    *   Ensure this change is reflected in the appropriate module file.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.