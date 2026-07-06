## Description

The current AI Gateway configuration has an inconsistent naming scheme for API schema specifications. When configuring the client-facing API schema, the field is called one thing (e.g., "inputSchema" with a sub-field "schema"). When configuring each backend's API schema, it uses a different top-level name (e.g., "outputSchema" with the same "schema" sub-field). This makes the configuration feel asymmetric and harder to understand — the same concept (an API schema specifier) has different names depending on context.

## Expected Behavior

All API schema specifications — whether for the client side or for individual backends — should use a unified field name. The containing field and the identifier within it should use consistent, shared names across all contexts. This applies to:
- The top-level schema on a gateway route resource
- The per-backend schema on a backend resource
- The YAML configuration consumed by the external processor
- The validation error messages produced when schemas are invalid or unsupported

## Current Behavior

- Gateway route resources use one top-level field name for the schema specification, with a sub-field for the schema identifier
- Backend resources use a different top-level field name for the same concept
- Validation errors reference these inconsistently named field paths
- The filter configuration YAML uses different top-level keys for client-side versus backend-side schema specifications, and both share the same sub-field name for the schema identifier

## Why This Matters

This inconsistency confuses operators writing or maintaining gateway configurations, because the same concept is expressed differently in different parts of the configuration. Unifying the naming makes the API more intuitive and consistent.
