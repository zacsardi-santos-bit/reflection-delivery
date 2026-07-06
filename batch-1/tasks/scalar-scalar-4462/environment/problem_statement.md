## Description

The API reference component uses URL hash fragments to link to specific sections — headings, models, tags, operations, and webhooks. Currently the slug format for these fragments is hardcoded and cannot be customized. This makes it impossible for developers to use their own URL fragment schemes (e.g., internationalized slugs, sequential IDs, or patterns that match an existing routing system).

Additionally, the functions that generate IDs for models and webhooks currently accept plain strings or multiple positional arguments, which is inconsistent and prevents passing richer metadata to future customization hooks.

## Expected Behavior

- Developers should be able to supply optional custom slug-generation functions through the reference configuration to control how URL fragment IDs are produced for each section type: headings, models, tags, operations, and webhooks.
- When a custom generator is provided for a section type, the navigation system must use it instead of the default slugification logic.
- The model ID generator must accept a structured object with a name field rather than a raw string.
- The webhook ID generator must accept a single structured object containing both the name and HTTP method, rather than two separate positional arguments.

## Why This Matters

Without this feature, teams embedding the API reference cannot align its internal anchor links with their own URL conventions. With custom slug generators in place, the fragment IDs in the rendered documentation can match any naming scheme the consumer requires, enabling deep-link compatibility with external systems and multi-language documentation setups.
