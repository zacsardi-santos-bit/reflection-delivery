Implement the necessary changes to the OpenTelemetry instrumentation for the VertexAI generative AI SDK to capture tool/function calling interactions. Ensure that both function call requests by the model and function results sent back to the model are recorded as observability events.

*   Update the 'gen_ai.choice' event:
    *   Include a 'tool_calls' field (a list) in the event body when the model response contains function call parts.
    *   Omit the 'tool_calls' field if no function calls are present.
    *   Ensure each entry in 'tool_calls' is an object with:
        *   'id': a string formatted as '{function_name}_{index}'.
        *   'type': always the string 'function'.
        *   'function': an object containing:
            *   'name': the function name.
            *   'arguments': a dictionary of call arguments (included only when content capture is enabled).

*   Emit 'gen_ai.tool.message' log events for function response parts:
    *   Emit one event per function response part in the conversation content block.
    *   Include attributes 'gen_ai.system'='vertex_ai' and 'event.name'='gen_ai.tool.message'.
    *   Ensure each event body includes:
        *   'role': derived from the content block's role, defaulting to 'tool' if absent.
        *   'id': formatted as '{function_name}_{index}'.
        *   'content': the response payload dict (included only when content capture is enabled).

*   Ensure proper event emission:
    *   Do not emit a 'gen_ai.user.message' event for a content block consisting entirely of function response parts.
    *   In a full function-calling round trip, emit exactly 5 log events in this order:
        *   gen_ai.user.message
        *   gen_ai.assistant.message
        *   gen_ai.tool.message (first function result)
        *   gen_ai.tool.message (second function result)
        *   gen_ai.choice
    *   For text-only responses, ensure the 'gen_ai.choice' event does not include a 'tool_calls' field.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.