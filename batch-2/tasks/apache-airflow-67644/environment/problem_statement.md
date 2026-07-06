## Description

When AI operators (used for running LLM and agent workflows) produce structured typed output, they currently serialize the result to a plain dictionary before returning it via the inter-task communication system. This loses the type information entirely — downstream tasks receive an untyped dictionary instead of the original typed object. Developers who define structured output types for their AI tasks cannot rely on those types in downstream logic, and must manually re-parse or reconstruct the typed objects from the dictionary.

## Expected Behavior

- When an AI operator completes execution with a structured typed output, the result passed to downstream tasks should be the original typed object instance, not a plain dictionary.
- There should be an opt-in mode where the old behavior (returning a dict) is still available for backward compatibility.
- Operators should validate at initialization time that the output type is safe to serialize and round-trip through the inter-task communication system — specifically, classes defined inside functions cannot be reliably re-imported and should be rejected with a clear error message.
- A new registration mechanism should allow specific output type classes to be whitelisted for typed deserialization without requiring changes to global configuration.
- The serialization display layer should show string field values in a quoted Python-like format for readability, and should clean up internal module name prefixes (from dynamically loaded DAG files) that otherwise appear in displayed class names.

## Why This Matters

Downstream tasks using typed XCom rely on receiving the actual typed object, not a dict. Without this, the typed XCom feature for AI operator results is effectively unusable — developers lose type safety, IDE support, and the ability to rely on structured data contracts between tasks in an AI pipeline.
