## Description

The stateful dependency inference engine recognizes foreign key field names when they follow an underscore-separated naming convention (e.g., a field referencing a Location resource's identifier). However, it fails to recognize the same relationship when field names follow camelCase naming conventions — which is very common in JSON APIs.

This means that APIs using camelCase field names for foreign key relationships miss out on automatic dependency detection, resulting in fewer inferred stateful test scenarios. A field named with a resource type directly followed by an identifier suffix should be recognized as referencing that resource's identifier, but currently these camelCase patterns are completely ignored by the inference engine.

## Expected Behavior

- CamelCase foreign key field names following the pattern of a resource name joined directly with an identifier suffix should be recognized as FK references, with the resource name extracted and the identifier suffix determined.
- Plural variants (where the field name ends in the plural form of the identifier suffix) should be recognized as array foreign keys.
- Fields that look similar but are not FK references — such as plain identifier fields with no resource prefix, short words that happen to end in an identifier-like suffix, or common English words — should continue to produce no FK inference.
- Existing underscore-separated naming should continue to work exactly as before.
- When FK fields appear inside nested objects in a request body, the system should create the correct input slot linking the nested field to the target resource.

## Why This Matters

Many modern APIs follow camelCase naming conventions. Without this fix, the tool generates fewer stateful test flows for these APIs than for snake_case APIs, reducing test coverage and making the tool inconsistently useful depending on the API's naming style.
