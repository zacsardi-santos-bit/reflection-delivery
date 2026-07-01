Implement the OpenTelemetry instrumentation for AWS Bedrock to capture telemetry for direct model invocations. Extend the existing functionality to handle distinct request and response formats for Amazon Titan, Amazon Nova, and Anthropic Claude models, ensuring that all relevant GenAI attributes are extracted and recorded.

*   Extend the `_BedrockRuntimeExtension` class in `instrumentation/opentelemetry-instrumentation-botocore/src/opentelemetry/instrumentation/botocore/extensions/bedrock.py`.
    *   Include "InvokeModel" in `_HANDLED_OPERATIONS` alongside "Converse".
    *   Implement the `extract_attributes` method to:
        *   Detect "InvokeModel" calls by checking for a "body" key in the call context params.
        *   Parse the body as JSON and set `GEN_AI_OPERATION_NAME`, `GEN_AI_REQUEST_TEMPERATURE`, `GEN_AI_REQUEST_TOP_P`, `GEN_AI_REQUEST_MAX_TOKENS`, and `GEN_AI_REQUEST_STOP_SEQUENCES` using model-family-specific field names.
        *   For Amazon Titan models, extract attributes from the 'textGenerationConfig' object.
        *   For Amazon Nova models, extract attributes from the 'inferenceConfig' object.
        *   For Anthropic Claude models, extract attributes from top-level JSON body keys.

*   Implement the `on_success` method to handle "InvokeModel" responses:
    *   Detect responses by checking for a StreamingBody.
    *   Read and replenish the stream to maintain response readability.
    *   Parse the JSON and set `GEN_AI_USAGE_INPUT_TOKENS`, `GEN_AI_USAGE_OUTPUT_TOKENS`, and `GEN_AI_RESPONSE_FINISH_REASONS` using model-family-specific field names.
    *   For Amazon Titan responses, extract from top-level and 'results[0]' fields.
    *   For Amazon Nova responses, extract from 'usage' and top-level fields.
    *   For Anthropic Claude responses, extract from 'usage' and top-level fields.

*   Implement the `on_error` method to:
    *   Set `StatusCode.ERROR` and `ERROR_TYPE` to 'ValidationException' for failed calls with invalid model identifiers.

*   Ensure no log events are emitted during the instrumentation of "InvokeModel" calls, regardless of success or failure.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.