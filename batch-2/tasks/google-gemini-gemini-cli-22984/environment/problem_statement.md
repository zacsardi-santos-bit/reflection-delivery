## Description

The codebase lacks a centralized, well-tested set of utilities for converting between the AI model's native message format and the framework's internal content representation. Currently, any code that needs to translate model response parts into the internal format (or vice versa) has to implement this logic ad hoc, leading to inconsistencies — especially around edge cases like thought parts, media with missing metadata, unknown content types, and tool result displays.

## Expected Behavior

- There should be a dedicated utility module in the agent directory, exporting the conversion functions.
- Converting from the AI model's parts format to the internal format should handle all known part types (plain text, thinking/thought parts with optional signatures, inline media data, file URI references) and gracefully serialize any unrecognized types rather than silently dropping them.
- Function call and function response parts from the model should be skipped when building the content representation.
- Converting in the reverse direction (internal format back to model parts) should support text, thought, media (both inline binary data and URI-based references), and reference parts; media with no payload should be omitted; missing MIME types should fall back to a sensible default.
- There should be a utility for converting tool result display values (which may be a plain string, a structured object, or absent) into the internal content format.
- There should be a utility for assembling the metadata record for tool response events, merging a data payload with additional metadata fields and returning undefined when no fields are present.

## Why This Matters

Having a single, tested home for these conversions reduces duplication, makes the conversion rules explicit and auditable, and ensures that unusual or future content types degrade gracefully rather than causing silent data loss.
