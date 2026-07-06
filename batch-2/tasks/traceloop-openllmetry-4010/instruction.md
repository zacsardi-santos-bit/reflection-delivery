I'm working on updating the Groq LLM instrumentation to align with the current generative AI observability standard.

*   Chat spans must be named using the format 'chat {model}' (operation name followed by a space and the model name), replacing the legacy 'groq.chat' span name.

*   Every span must include three core OTel generative AI attributes: gen_ai.provider_name set to 'groq', gen_ai.operation.name set to 'chat', and gen_ai.request.model set to the model name used in the request.

*   Input messages must be stored on the span as a JSON array under gen_ai.input_messages, where each element has a 'role' field and a 'parts' array of typed content objects. Plain text content produces parts with type='text' and a 'content' field. Tool role messages produce parts with type='tool_call_response', 'id', and 'response' fields. Assistant messages with tool calls produce parts with type='tool_call'.

*   Output messages must be stored on the span as a JSON array under gen_ai.output_messages, where each element has 'role', 'finish_reason', and a 'parts' array. For streaming responses the same structure applies. When content tracing is disabled, output message attributes must not be set.

*   The streaming status attribute must use gen_ai.is_streaming (not the legacy llm.is_streaming), and the total token count must use gen_ai.usage.total_tokens (not the legacy llm.usage.total_tokens).

*   Spans in non-legacy mode must include a gen_ai.response.finish_reasons list attribute containing the finish reason(s) for the response (e.g., 'stop').

*   The Groq API finish reason string 'tool_calls' (plural) must be mapped to the OTel standard value 'tool_call' (singular). None or empty string finish reasons must map to an empty string. All other unknown values must be preserved as-is.

*   _collect_finish_reasons_from_response must accept a response object and return a list of mapped finish reason strings from all choices. It must return an empty list for a None response or a response with empty choices.

*   Log event records emitted during instrumentation must carry a gen_ai.provider_name attribute set to 'groq' (not gen_ai.system). The event name and body structure remain as before.

*   _content_to_parts must convert message content to a list of typed part dicts: None or empty string returns [], a plain string returns a single text part, a list of content blocks maps text blocks to text parts, image_url blocks with regular URLs to uri parts, image_url blocks with data URLs to blob parts (with mime_type and base64 content extracted), non-dict list items are skipped, and unknown block types are preserved as generic dicts.

*   _tool_calls_to_parts must convert a list of tool call objects (dicts or Pydantic-style) to a list of tool_call part dicts. String arguments are parsed as JSON; invalid JSON is kept as a raw string. Dict arguments are used as-is. When no arguments key exists, the arguments field is omitted. Non-dict items in the input list are skipped.

*   set_model_response_attributes must record token histogram entries as exactly two separate calls in order: first histogram.record(input_token_count, attributes={..., 'gen_ai.token.type': 'input', ...}), then histogram.record(output_token_count, attributes={..., 'gen_ai.token.type': 'output', ...}). The token count must be passed as the first positional argument and the attributes dict as the keyword argument named 'attributes'. If the response has no usage data or the histogram is None, no recording occurs. A non-recording span must cause early return.

*   The dont_throw decorator must catch all exceptions from the wrapped function, call Config.exception_logger with the exception if it is set, and return None without re-raising. Config.exception_logger is a class-level attribute on Config, defaulting to None.

*   error_metrics_attributes must return a dict containing GEN_AI_PROVIDER_NAME='groq' and 'error.type' set to the exception's class name.

*   model_as_dict must handle Pydantic v2 objects (via model_dump()), Pydantic v1 objects (via dict()), objects with a parse() method (by calling it), and plain dicts (returned as-is).

*   _wrap and _awrap must suppress instrumentation (skip span creation and call the wrapped function directly) when the OpenTelemetry suppression key is set in context. _awrap must additionally suppress when the language model suppression key is set. On API exception both must record to the duration histogram (if provided) and re-raise after ending the span. Exceptions from response handling are swallowed. Stream processor exceptions end the span and re-raise. When span.is_recording() is False at the final status check, set_status must not be called.


*   Interface details: ## Module: opentelemetry/instrumentation/groq/__init__.py

Type: Function
Name: _process_streaming_chunk
Location: packages/opentelemetry-instrumentation-groq/opentelemetry/instrumentation/groq/__init__.py
Signature: _process_streaming_chunk(chunk) -> tuple
Description: Processes a single streaming chunk from the Groq API. Returns a 4-tuple of (content: str | None, tool_calls_delta: list, finish_reasons: list, usage: object | None). When choices is empty returns (None, [], [], None).

Type: Function
Name: _accumulate_tool_calls
Location: packages/opentelemetry-instrumentation-groq/opentelemetry/instrumentation/groq/__init__.py
Signature: _accumulate_tool_calls(acc: dict, delta_list: list) -> None
Description: Accumulates tool call deltas into a dict keyed by index. Each entry has "id", "function" with "name" and "arguments". Argument string fragments are concatenated across calls.

Type: Function
Name: _create_stream_processor
Location: packages/opentelemetry-instrumentation-groq/opentelemetry/instrumentation/groq/__init__.py
Signature: _create_stream_processor(response, span, event_logger) -> generator
Description: Returns a sync generator that yields chunks from the response, then ends the span. When span is not recording, skips set_status but always calls span.end().

Type: Function
Name: _create_async_stream_processor
Location: packages/opentelemetry-instrumentation-groq/opentelemetry/instrumentation/groq/__init__.py
Signature: _create_async_stream_processor(response, span, event_logger) -> async generator
Description: Returns an async generator that yields chunks from the async response, then ends the span. When span is not recording, skips set_status but always calls span.end().

Type: Function
Name: _wrap
Location: packages/opentelemetry-instrumentation-groq/opentelemetry/instrumentation/groq/__init__.py
Signature: _wrap(tracer, token_histogram, choice_counter, duration_histogram, event_logger, kwargs) -> callable
Description: Returns a wrapt-style wrapper function (wrapped, instance, args, kwargs) -> result. When instrumentation is suppressed (via _SUPPRESS_INSTRUMENTATION_KEY), calls the wrapped function directly without creating a span. On API exception, records duration histogram (if provided) and re-raises; always ends span. For falsy response, ends span without calling set_status. Exceptions from _handle_response are swallowed. Stream processor exceptions end the span and re-raise. When span.is_recording() is False at final status check, skips set_status.

Type: Function
Name: _awrap
Location: packages/opentelemetry-instrumentation-groq/opentelemetry/instrumentation/groq/__init__.py
Signature: _awrap(tracer, token_histogram, choice_counter, duration_histogram, event_logger, kwargs) -> coroutine function
Description: Async version of _wrap. Suppresses when either _SUPPRESS_INSTRUMENTATION_KEY or SUPPRESS_LANGUAGE_MODEL_INSTRUMENTATION_KEY is set. Same span lifecycle behaviors as _wrap but for async calls.

Type: Function
Name: _handle_response
Location: packages/opentelemetry-instrumentation-groq/opentelemetry/instrumentation/groq/__init__.py
Signature: _handle_response(span, kwargs, response, token_histogram, choice_counter, event_logger) -> None
Description: Internal helper called within _wrap and _awrap to process a non-streaming API response. Sets span attributes (output messages, token usage, finish reasons) and emits log events via the event logger. Exceptions raised by this function are swallowed by the calling wrapper — they must not propagate. Must be defined as a module-level function in __init__.py so tests can patch it at opentelemetry.instrumentation.groq._handle_response.

Type: Function
Name: shared_metrics_attributes
Location: packages/opentelemetry-instrumentation-groq/opentelemetry/instrumentation/groq/__init__.py
Signature: shared_metrics_attributes(span, kwargs) -> dict
Description: Returns a dict of attribute key-value pairs shared across all metric recordings (e.g., provider name, model). Used by _wrap and _awrap when recording duration histogram values on the success path. Must be defined as a module-level function in __init__.py so tests can patch it at opentelemetry.instrumentation.groq.shared_metrics_attributes.

Type: Class
Name: GroqInstrumentor
Location: packages/opentelemetry-instrumentation-groq/opentelemetry/instrumentation/groq/__init__.py
Description: Main instrumentor class. instrument(tracer_provider, meter_provider) sets up patching; when TRACELOOP_METRICS_ENABLED env var is "false", histograms are set to None. ModuleNotFoundError from wrap_function_wrapper is swallowed. uninstrument() removes patching.

---

## Module: opentelemetry/instrumentation/groq/span_utils.py

Type: Function
Name: _content_to_parts
Location: packages/opentelemetry-instrumentation-groq/opentelemetry/instrumentation/groq/span_utils.py
Signature: _content_to_parts(content) -> list
Description: Converts message content to a list of typed part dicts. None or "" returns []. Plain string returns [{"type": "text", "content": <string>}]. List items of type "text" return {"type": "text", "content": item["text"]}. List items of type "image_url" with https:// URL return {"type": "uri", "modality": "image", "uri": <url>}. List items of type "image_url" with data URL return {"type": "blob", "modality": "image", "mime_type": <mime>, "content": <base64_data>}. Non-dict list items are skipped. Unknown block types are preserved as-is.

Type: Function
Name: _tool_calls_to_parts
Location: packages/opentelemetry-instrumentation-groq/opentelemetry/instrumentation/groq/span_utils.py
Signature: _tool_calls_to_parts(tool_calls) -> list
Description: Converts a list of tool calls to part dicts with type="tool_call". None or [] returns []. Each dict item produces {"type": "tool_call", "id": ..., "name": ..., "arguments": ...}. String arguments are parsed as JSON; if invalid JSON, returned as raw string. Dict arguments used as-is. When no arguments key, "arguments" is omitted from result. Non-dict items are skipped. Pydantic-style objects with .id, .function.name, .function.arguments are handled.

Type: Function
Name: set_input_attributes
Location: packages/opentelemetry-instrumentation-groq/opentelemetry/instrumentation/groq/span_utils.py
Signature: set_input_attributes(span, kwargs) -> None
Description: Sets GEN_AI_INPUT_MESSAGES as JSON on span from kwargs["messages"]. Returns early if span is not recording. Does not set attribute for empty messages. Messages with role "tool" create parts of type "tool_call_response" with "id" (from tool_call_id) and "response" fields. Messages with tool_calls create tool_call parts.

Type: Function
Name: set_model_input_attributes
Location: packages/opentelemetry-instrumentation-groq/opentelemetry/instrumentation/groq/span_utils.py
Signature: set_model_input_attributes(span, kwargs) -> None
Description: Sets model-level input attributes (e.g., GEN_AI_TOOL_DEFINITIONS as JSON if tools provided). Returns early if span is not recording. Tool definitions not set when content tracing is disabled (TRACELOOP_TRACE_CONTENT=False). Unserializable tools silently ignored (exception swallowed, attribute not set).

Type: Function
Name: set_response_attributes
Location: packages/opentelemetry-instrumentation-groq/opentelemetry/instrumentation/groq/span_utils.py
Signature: set_response_attributes(span, response) -> None
Description: Sets GEN_AI_OUTPUT_MESSAGES as JSON on span from response choices. Returns early if span is not recording or content tracing is disabled. Does not set attribute for empty choices. Finish reason "tool_calls" is mapped to "tool_call". Legacy function_call in message is converted to tool_call part. Invalid JSON arguments returned as raw string. Dict arguments used as-is.

Type: Function
Name: set_streaming_response_attributes
Location: packages/opentelemetry-instrumentation-groq/opentelemetry/instrumentation/groq/span_utils.py
Signature: set_streaming_response_attributes(span, content, finish_reason=None, tool_calls=None) -> None
Description: Sets GEN_AI_OUTPUT_MESSAGES as JSON for streaming responses. Returns early if span is not recording. Non-empty content creates text parts. Tool calls add tool_call parts. Finish reason "tool_calls" mapped to "tool_call". Output structure: [{"role": "assistant", "finish_reason": ..., "parts": [...]}].

Type: Function
Name: set_model_streaming_response_attributes
Location: packages/opentelemetry-instrumentation-groq/opentelemetry/instrumentation/groq/span_utils.py
Signature: set_model_streaming_response_attributes(span, usage, finish_reasons=None) -> None
Description: Sets token usage and finish reasons for streaming. Returns early if span is not recording. Usage with completion_tokens sets GEN_AI_USAGE_OUTPUT_TOKENS; prompt_tokens sets GEN_AI_USAGE_INPUT_TOKENS. None usage skips token attributes. finish_reasons list sets GEN_AI_RESPONSE_FINISH_REASONS; None skips it. Unknown finish reasons preserved as-is.

Type: Function
Name: set_model_response_attributes
Location: packages/opentelemetry-instrumentation-groq/opentelemetry/instrumentation/groq/span_utils.py
Signature: set_model_response_attributes(span, response, token_histogram) -> None
Description: Sets token usage from response and records histogram. Returns early if span is not recording (histogram.record not called). When response has no usage, histogram not called. None histogram doesn't raise. When histogram is provided, makes exactly two ordered calls: first histogram.record(input_token_count, attributes={..., "gen_ai.token.type": "input"}), then histogram.record(output_token_count, attributes={..., "gen_ai.token.type": "output"}). The token count is passed as the first positional argument; "attributes" is passed as a keyword argument. Input tokens are always recorded before output tokens.

Type: Function
Name: _map_groq_finish_reason
Location: packages/opentelemetry-instrumentation-groq/opentelemetry/instrumentation/groq/span_utils.py
Signature: _map_groq_finish_reason(reason) -> str
Description: Maps a Groq API finish reason string to OTel standard. "tool_calls" -> "tool_call". None -> "". "" -> "". Any other value preserved as-is.

Type: Function
Name: _collect_finish_reasons_from_response
Location: packages/opentelemetry-instrumentation-groq/opentelemetry/instrumentation/groq/span_utils.py
Signature: _collect_finish_reasons_from_response(response) -> list
Description: Extracts finish reasons from all choices in a response, mapping each via _map_groq_finish_reason. Returns [] for None response or empty choices. Each choice.finish_reason is mapped and included in the returned list.

---

## Module: opentelemetry/instrumentation/groq/event_emitter.py

Type: Function
Name: emit_event
Location: packages/opentelemetry-instrumentation-groq/opentelemetry/instrumentation/groq/event_emitter.py
Signature: emit_event(event, event_logger) -> None
Description: Emits a single event to the event logger. Returns immediately if event_logger is None. Returns immediately if should_emit_events() returns False. Raises TypeError with message matching "Unsupported event type" for unsupported event types.

Type: Function
Name: emit_message_events
Location: packages/opentelemetry-instrumentation-groq/opentelemetry/instrumentation/groq/event_emitter.py
Signature: emit_message_events(kwargs, event_logger) -> None
Description: Emits message events from kwargs["messages"]. For assistant messages with tool_calls, passes tool_calls to the MessageEvent (present in emitted body). For messages without tool_calls, tool_calls key must not appear in emitted body.

Type: Function
Name: _emit_message_event
Location: packages/opentelemetry-instrumentation-groq/opentelemetry/instrumentation/groq/event_emitter.py
Signature: _emit_message_event(event, event_logger) -> None
Description: Emits a single message event log record. Unknown/invalid roles fall back to event name "gen_ai.user.message". Non-assistant roles have tool_calls removed from body. Assistant role keeps tool_calls in body. When TRACELOOP_TRACE_CONTENT is False: content and function arguments removed from body.

Type: Function
Name: _emit_choice_event
Location: packages/opentelemetry-instrumentation-groq/opentelemetry/instrumentation/groq/event_emitter.py
Signature: _emit_choice_event(event, event_logger) -> None
Description: Emits a choice event log record. Non-assistant roles keep role in message body. Non-None tool_calls are kept in body. When TRACELOOP_TRACE_CONTENT is False: content and role removed from message body; function arguments removed from tool_calls.

---

## Module: opentelemetry/instrumentation/groq/event_models.py

Type: Class
Name: MessageEvent
Location: packages/opentelemetry-instrumentation-groq/opentelemetry/instrumentation/groq/event_models.py
Description: Event model for input messages.
Signature: MessageEvent(content, role, tool_calls=None)

Type: Class
Name: ChoiceEvent
Location: packages/opentelemetry-instrumentation-groq/opentelemetry/instrumentation/groq/event_models.py
Description: Event model for response choices.
Signature: ChoiceEvent(index, message, finish_reason, tool_calls=None)

---

## Module: opentelemetry/instrumentation/groq/utils.py

Type: Function
Name: dont_throw
Location: packages/opentelemetry-instrumentation-groq/opentelemetry/instrumentation/groq/utils.py
Signature: dont_throw (decorator)
Description: Decorator that catches all exceptions from the decorated function. Calls Config.exception_logger(exception) if it is set. Returns None when exception is caught. Does not propagate exceptions.

Type: Function
Name: error_metrics_attributes
Location: packages/opentelemetry-instrumentation-groq/opentelemetry/instrumentation/groq/utils.py
Signature: error_metrics_attributes(exception) -> dict
Description: Returns a dict with GEN_AI_PROVIDER_NAME="groq" and "error.type" set to the exception's class name string.

Type: Function
Name: model_as_dict
Location: packages/opentelemetry-instrumentation-groq/opentelemetry/instrumentation/groq/utils.py
Signature: model_as_dict(model) -> dict
Description: Converts a model object to a plain dict. Pydantic v2 objects (with model_dump): calls model_dump(). Pydantic v1 objects (with dict but no model_dump, or when _PYDANTIC_VERSION is 1.x): calls dict(). Objects with parse() method: calls parse(). Plain dicts: returned as-is.

Type: Constant
Name: TRACELOOP_TRACE_CONTENT
Location: packages/opentelemetry-instrumentation-groq/opentelemetry/instrumentation/groq/utils.py
Description: String constant used as environment variable key to control whether message content is included in traces.

Type: Constant
Name: _PYDANTIC_VERSION
Location: packages/opentelemetry-instrumentation-groq/opentelemetry/instrumentation/groq/utils.py
Description: Module-level string constant holding the installed Pydantic version (e.g., "1.9.0" or "2.0.0"). Used by model_as_dict to select the correct serialization path. Must be defined at module level so tests can patch it at opentelemetry.instrumentation.groq.utils._PYDANTIC_VERSION.

---

## Module: opentelemetry/instrumentation/groq/config.py

Type: Class
Name: Config
Location: packages/opentelemetry-instrumentation-groq/opentelemetry/instrumentation/groq/config.py
Description: Configuration class for the Groq instrumentation. Has a class-level exception_logger attribute (default None) that is called with the exception when dont_throw catches one.
Signature: Config.exception_logger (class attribute, callable or None)

---

## Span and Log Event Attribute Requirements

The instrumentation must set the following span attributes using OTel generative AI semantic conventions:
- Span name: `"chat {model}"` (operation + space + model name)
- `gen_ai.provider_name` = "groq" (GenAiProviderNameValues.GROQ.value)
- `gen_ai.operation.name` = "chat" (GenAiOperationNameValues.CHAT.value)
- `gen_ai.request.model` = model name string
- `gen_ai.input_messages` = JSON string (array of message objects with role and parts)
- `gen_ai.output_messages` = JSON string (array of message objects with role, finish_reason, and parts)
- `gen_ai.is_streaming` = True/False (SpanAttributes.GEN_AI_IS_STREAMING)
- `gen_ai.usage.total_tokens` (SpanAttributes.GEN_AI_USAGE_TOTAL_TOKENS)
- `gen_ai.usage.input_tokens`
- `gen_ai.usage.output_tokens`
- `gen_ai.response.finish_reasons` = list of finish reason strings

Log event records must set `gen_ai.provider_name` = "groq" (not `gen_ai.system`).


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.