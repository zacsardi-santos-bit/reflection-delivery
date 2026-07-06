I'm working with MLflow's automatic tracing for Amazon Bedrock and I need the recorded spans to conform to the OpenTelemetry Generative AI semantic conventions standard rather than using MLflow-specific attribute keys.

*   The `_convert_image` function must accept a Bedrock image dict with a 'format' key (string) and a 'source' dict containing a 'bytes' key. When 'bytes' is raw bytes, it must return a dict with 'type': 'blob', 'modality': 'image', 'mime_type': 'image/<format>', and 'content': the base64-encoded UTF-8 string of the bytes.

*   When `_convert_image` receives a dict where 'source.bytes' is already a string (e.g., a base64 string), it must return a dict with 'type': 'blob', 'modality': 'image', 'mime_type': 'image/<format>', and 'content': the same string passed through unchanged.

*   The `BedrockConverseConverter` class must be registered in mlflow/tracing/export/genai_semconv/translator.py under the 'bedrock' provider case in the _get_converter function, so that it is dispatched when the autolog integration detects the provider is 'bedrock' and the MLFLOW_ENABLE_OTEL_GENAI_SEMCONV env var is set.

*   When the Bedrock autolog integration is active and a converse call is made, the resulting span must have GenAiSemconvKey.OPERATION_NAME set to the string 'chat'.

*   The span must have GenAiSemconvKey.REQUEST_MODEL set to the model ID, GenAiSemconvKey.REQUEST_TEMPERATURE, GenAiSemconvKey.REQUEST_MAX_TOKENS, and GenAiSemconvKey.REQUEST_TOP_P set from the request's inferenceConfig.

*   The span must have GenAiSemconvKey.INPUT_MESSAGES set to a JSON-serialized array of message objects. Each message object has a 'role' field and a 'parts' field (list). Text content blocks must be represented as {'type': 'text', 'content': <text>}.

*   The span must have GenAiSemconvKey.OUTPUT_MESSAGES set to a JSON-serialized array of message objects using the same structure as input messages.

*   The span must have GenAiSemconvKey.USAGE_INPUT_TOKENS set to the inputTokens count and GenAiSemconvKey.USAGE_OUTPUT_TOKENS set to the outputTokens count from the response's usage field.

*   When a converse request contains tool calls, the assistant message parts must represent tool call blocks as {'type': 'tool_call', 'id': <toolUseId>, 'name': <name>, 'arguments': <input dict>}.

*   Tool result messages must be represented with role 'tool' and parts using {'type': 'tool_call_response', 'id': <toolUseId>, 'result': <JSON string of result content>}.

*   The span must have GenAiSemconvKey.TOOL_DEFINITIONS set to a JSON-serialized array of tool definition objects. Each tool definition must have a 'name' field but must NOT have a 'function' key (i.e., tool definitions are not wrapped in a function envelope).

*   When a converse request includes a 'system' field, the span must have GenAiSemconvKey.SYSTEM_INSTRUCTIONS set to a JSON-serialized array of objects with 'type': 'text' and 'content': <text>.

*   When a converse request includes image content blocks, the image must be represented in the message parts as {'type': 'blob', 'modality': 'image', 'mime_type': 'image/<format>', 'content': <base64-encoded string>}.

*   When the OTEL GenAI semantic convention mode is enabled, the Bedrock autolog span must not contain any attributes whose key starts with 'mlflow.'.


*   Interface details: Type: Function
Name: _convert_image
Location: mlflow/bedrock/genai_semconv_converter.py
Signature: _convert_image(image: dict) -> dict
Description: Converts a Bedrock image content block into the GenAI semantic convention blob format. The input dict has keys "format" (string, e.g. "jpeg", "png") and "source" containing a "bytes" value that is either raw bytes or an already-encoded base64 string. Returns a dict with keys: "type" (always "blob"), "modality" (always "image"), "mime_type" (e.g. "image/jpeg"), and "content" (base64-encoded string for raw bytes input, or passthrough for string input).

Type: Module
Name: genai_semconv_converter
Location: mlflow/bedrock/genai_semconv_converter.py
Description: Module providing conversion logic that translates Bedrock API request/response structures into the OpenTelemetry GenAI semantic convention format. Must be importable as `mlflow.bedrock.genai_semconv_converter`. Consumed by the Bedrock autolog integration to populate span attributes.

Type: Class
Name: BedrockConverseConverter
Location: mlflow/bedrock/genai_semconv_converter.py
Description: Converter class that translates Bedrock Converse API inputs and outputs into the OpenTelemetry GenAI semantic convention format. Must be registered in the translator at mlflow/tracing/export/genai_semconv/translator.py under the "bedrock" provider case (within the _get_converter function) so that Bedrock autolog spans are converted to GenAI semconv attributes when the MLFLOW_ENABLE_OTEL_GENAI_SEMCONV environment variable is set. The translator imports it as: from mlflow.bedrock.genai_semconv_converter import BedrockConverseConverter. Must implement conversion methods that:
  - Convert request messages list to GenAI semconv message parts format
  - Convert response output message to GenAI semconv message parts format
  - Extract system instructions from the request's "system" field
  - Extract inference parameters (temperature, maxTokens, topP) from "inferenceConfig" and map them to GenAiSemconvKey constants
  - Extract tool definitions from "toolConfig.tools" and serialize them to GenAiSemconvKey.TOOL_DEFINITIONS

Type: Class
Name: GenAiSemconvKey
Location: mlflow/tracing/constant.py
Description: Class holding OpenTelemetry GenAI semantic convention attribute key constants. Must include the following attributes:
  - OPERATION_NAME: key for operation type (value used is "chat")
  - REQUEST_MODEL: key for the model identifier
  - REQUEST_TEMPERATURE: key for temperature inference parameter
  - REQUEST_MAX_TOKENS: key for max tokens inference parameter
  - REQUEST_TOP_P: key for top-p inference parameter
  - INPUT_MESSAGES: key for serialized input messages array (JSON string)
  - OUTPUT_MESSAGES: key for serialized output messages array (JSON string)
  - USAGE_INPUT_TOKENS: key for input token count
  - USAGE_OUTPUT_TOKENS: key for output token count
  - SYSTEM_INSTRUCTIONS: key for serialized system instructions array (JSON string)
  - TOOL_DEFINITIONS: key for serialized tool definitions array (JSON string)


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.