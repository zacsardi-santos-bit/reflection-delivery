## Description

The MLflow AI Gateway currently has no mechanism to intercept and enforce content policies on traffic flowing through it. There is no way to automatically validate that incoming requests are safe before they reach a language model, nor any way to verify or clean up a model's response before it is returned to the caller. This gap means teams cannot enforce safety, compliance, or content-quality guarantees at the gateway layer without building custom middleware outside MLflow.

## Expected Behavior

- A guardrail component should be configurable to run either before a request is forwarded to the LLM or after a response is received from it.
- When configured for **validation**, the guardrail should invoke a scorer function to evaluate the content. If the scorer indicates the content is acceptable, the content passes through unchanged. If the scorer indicates a problem, the guardrail should block the request/response and produce an informative error that includes the guardrail's name and the reason provided by the scorer.
- When configured for **sanitization**, the guardrail should invoke a scorer to check the content, and if a problem is found, automatically call a secondary LLM endpoint to rewrite the content into an acceptable form before forwarding it. The sanitized output should be requested in a structured JSON format matching the expected payload shape.
- The guardrail component should be reconstructable from a persisted database entity, with the scorer being deserialized from its stored representation. When running inside the gateway server, model routing URIs that point to gateway-internal endpoints must be automatically translated to an appropriate form so evaluation calls are routed correctly.

## Why This Matters

Without a guardrail layer, enforcing content safety or compliance policies requires custom solutions outside the gateway. This feature enables teams to configure reusable, scorer-driven guardrails that integrate natively into the MLflow gateway pipeline, supporting both hard blocking and automatic content remediation workflows.
