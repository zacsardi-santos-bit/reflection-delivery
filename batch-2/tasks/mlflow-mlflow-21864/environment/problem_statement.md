## Description

MLflow's evaluation and discovery tools can call LLMs internally as part of their pipeline, but these calls currently only work with direct external provider endpoints. There is no support for routing these calls through a locally running MLflow gateway service. This means teams who use MLflow's gateway as a proxy for their model calls cannot use it as the target model for evaluation workflows.

## Expected Behavior

- When a gateway endpoint is specified as the model target (using the gateway URI scheme), the system should detect this, retrieve the gateway's connection details (service URL, API key, and any custom request headers), and route the LLM call through that gateway.
- When a standard provider URI is used, the call should continue to work exactly as before, using the existing URI conversion logic with no gateway-specific parameters.
- The LLM call interface should support requesting structured JSON output mode as a call option.
- The LLM call interface should support passing a structured schema (defined as a model class) to request structured responses.
- When a token usage tracker is provided, the system should populate it with the number of input tokens, output tokens, and the monetary cost after each LLM call.

## Why This Matters

This makes it possible to use the MLflow gateway as a unified proxy for LLM calls throughout the evaluation pipeline, enabling centralized credential management, rate limiting, and routing without requiring callers to supply credentials directly. Token and cost tracking enables usage monitoring across evaluation runs.
