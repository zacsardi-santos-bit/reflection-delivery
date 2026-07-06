## Description

The MLflow GenAI scoring infrastructure currently maintains multiple separate backend adapter classes for each scorer integration (deepeval, phoenix, ragas, trulens). Each integration has one class for Databricks-managed endpoints and a different class for external/gateway providers. This duplication scatters routing logic, retry handling, and LLM provider dispatch across many individual scorer modules, making the code harder to maintain and evolve.

## Problem

When adding support for new LLM providers or changing how backends communicate, developers must update the same routing and dispatch logic in many different places. The split between "Databricks" and "gateway" classes within each scorer framework is an internal implementation artifact that leaks into the public-facing API. Users and downstream code must know which class to instantiate rather than relying on a single, uniform adapter.

## Expected Behavior

- A single unified backend client should be introduced to centralize all routing decisions, retry logic, and LLM provider dispatch. All scorer integrations should delegate to this shared client.
- Each scorer framework should expose exactly one model adapter class instead of two (no separate "Databricks" and "gateway" variants).
- The shared backend client should correctly route requests based on the model URI: bare Databricks identifiers, endpoint URIs, native provider URIs (with required credentials), gateway URIs, and unsupported providers falling back to an external completion library.
- Retry behavior and structured response format handling should be managed by the shared backend client.

## Why This Matters

Centralizing the backend routing in one place reduces duplication, makes it easier to add or update LLM provider support, and presents a simpler API to scorer authors. Instead of knowing which of two adapter classes to use, a scorer author supplies a model URI to the shared client and gets back a uniform interface regardless of which backend is in use.
