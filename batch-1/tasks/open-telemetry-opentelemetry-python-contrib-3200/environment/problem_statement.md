## Description

The OpenTelemetry instrumentation for AWS Bedrock currently only captures GenAI telemetry for the higher-level conversational API. Applications that use the lower-level direct model invocation path — which allows developers to control the full request body for each model — receive no observability data at all. No operation type, no token usage, no inference parameters, and no completion reasons appear in traces for these calls.

This leaves a significant instrumentation gap because many real-world Bedrock applications use direct model invocations rather than the conversational API. Three widely-used model families each have distinct request and response formats that the instrumentation needs to handle.

## Expected Behavior

- Spans should be created for direct model invocations with the correct GenAI operation type: text completion for Amazon Titan models, and chat for Amazon Nova and Anthropic Claude models
- Inference parameters from the request body (temperature, nucleus sampling probability threshold, maximum token count, and stop sequences) should be captured as span attributes, extracted from the correct model-family-specific fields
- Token usage (input and output counts) and the completion/stop reason should be captured from the streaming model response
- After telemetry is extracted from the streaming response body, the response must remain fully readable by application code
- When a call fails due to an invalid model identifier, the span should record an error status and capture the error classification indicating a validation failure for the model identifier
- No log events should be emitted when instrumenting these calls

## Why This Matters

Without this support, developers cannot observe or monitor direct model invocations through OpenTelemetry, making it impossible to track token consumption, error rates, or generation parameters for a large class of Bedrock usage patterns.
