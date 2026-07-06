Implement OpenTelemetry instrumentation for the OpenAI Python client's Responses API to ensure observability. Create spans for API calls that capture detailed information about the request and response, similar to existing instrumentation for the chat completions API.

*   Create an OpenTelemetry span named 'openai.response' for each call to the Responses API create method.
    *   Set 'gen_ai.system' to 'openai'.
    *   Set 'gen_ai.request.model' to the model name from the request.
    *   Set 'gen_ai.response.model' to the model identifier from the response.
*   Record input prompts in the span:
    *   For plain string inputs, use 'gen_ai.prompt.0.content' and set 'gen_ai.prompt.0.role' to 'user'.
    *   For message list inputs, record each message with 'gen_ai.prompt.{i}.content' and 'gen_ai.prompt.{i}.role'.
        *   JSON-encode content if it is a structured list.
*   Record the response text output as 'gen_ai.completion.0.content' and set 'gen_ai.completion.0.role' to 'assistant'.
*   Capture tool (function) definitions and calls:
    *   Record request functions with 'llm.request.functions.{i}.name', 'llm.request.functions.{i}.description', and 'llm.request.functions.{i}.parameters' (JSON-encoded).
    *   Record response tool calls with 'gen_ai.completion.0.tool_calls.{i}.name', 'gen_ai.completion.0.tool_calls.{i}.arguments', and 'gen_ai.completion.0.tool_calls.{i}.id'.
*   Set 'gen_ai.response.id' to the response identifier from the API.
*   Apply instrumentation to both synchronous and asynchronous create and retrieve methods of the Responses API.
*   Implement the new module at 'packages/opentelemetry-instrumentation-openai/opentelemetry/instrumentation/openai/v1/responses_wrappers.py', following existing patterns for API wrappers.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.