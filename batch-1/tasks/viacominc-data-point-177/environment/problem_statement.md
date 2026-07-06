## Description

When creating entity types, the entity identifier is currently embedded inside the specification object itself. This tightly couples the entity's identity to its structural configuration, which makes it harder to separate concerns and reuse validation logic across different entity types.

Additionally, there is no reusable utility for validating that an entity's configuration only uses recognized property names. Configuration mistakes — such as using an unrecognized key in an entity spec — can silently pass through without any helpful error.

## Expected Behavior

- The entity factory should accept the entity identifier as a separate argument rather than requiring it to be a property inside the specification object.
- A new validation utility should be available that checks a specification object against a list of permitted property names and throws a descriptive error for any unrecognized keys.
- A higher-level variant of this utility should automatically include the standard set of base modifier properties as always-allowed keys, so that only truly unexpected keys cause an error.
- When a specification only contains allowed keys (or is empty), the validation utility should return successfully.

## Why This Matters

Separating the entity id from its specification makes the API cleaner and more composable. The new validation utilities allow entity type factories to catch configuration mistakes early and surface helpful errors, rather than failing silently or with obscure runtime errors later in processing.
