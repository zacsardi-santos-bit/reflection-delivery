## Description

The MLflow AI gateway routes requests to underlying AI providers, but currently has no mechanism to enforce content safety or policy rules on the traffic passing through it. We need to add a guardrail system that lets operators attach evaluation and sanitization rules to gateway endpoints.

## Expected Behavior

- Operators should be able to configure one or more guardrails on a gateway endpoint, each associated with a scorer that evaluates content.
- Before a request is forwarded to the provider, all "pre-request" guardrails should be evaluated. If any fails, the request should be rejected with an error and the provider should never be called.
- After the provider responds, all "post-response" guardrails should be evaluated. If any fails, the response should be blocked and an error returned to the caller.
- In addition to a blocking (validation) mode, a guardrail can be configured in a rewriting (sanitization) mode: when the scorer fails, the content is passed to a separate "sanitizer" endpoint which rewrites it, and the rewritten content is used instead of rejecting the request entirely.
- A sanitization guardrail with no sanitizer endpoint configured should fall back to blocking the request with an error.
- Trusted internal callers should be able to bypass all guardrail checks by including a specific request header with the exact value "1". Any other value for that header must not bypass the guardrails.
- When multiple guardrails are attached to the same endpoint, they should be evaluated in the order defined by their configured priority (ascending execution order), regardless of the order they were registered.

## Why This Matters

Without guardrails, the gateway has no way to enforce organizational policies, safety rules, or content filters on AI-generated traffic. This feature makes the gateway suitable for production deployments where content must be validated or sanitized before reaching end users.
