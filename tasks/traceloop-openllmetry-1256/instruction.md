Implement tracing for the automatic polling method in the OpenAI instrumentation package to ensure it generates telemetry identical to the manual approach. Ensure that a single trace span is produced when using the create-and-poll convenience method, capturing all relevant attributes without generating additional spans for polling requests.

*   Produce a single trace span named 'openai.assistant.run' when using the create-and-poll method.
    *   Ensure polling requests do not generate additional spans.
*   Set the span attribute 'llm.request.type' to 'chat'.
*   Include the model name in the span:
    *   Set 'gen_ai.request.model' and 'gen_ai.response.model' to the model name from the assistant's definition.
*   Record system instructions in the span:
    *   Include the assistant's base system instructions as 'gen_ai.prompt.0.content' with 'gen_ai.prompt.0.role' set to 'system'.
    *   If run-level override instructions are provided, include them as 'gen_ai.prompt.1.content' with 'gen_ai.prompt.1.role' set to 'system'.
*   Capture all thread messages in the span:
    *   Include each message as indexed attributes: 'gen_ai.completion.{idx}.content' for the text value of the first content block, and 'gen_ai.completion.{idx}.role' for the message's role.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.