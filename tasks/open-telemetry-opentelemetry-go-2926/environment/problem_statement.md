## Description

The metrics SDK is missing a way for users to configure how individual instruments are selected and transformed before their data is exported. Currently, there is no mechanism to match instruments by name or instrumentation scope, rename them, override their descriptions, or restrict which attributes they report. This makes it impossible to implement flexible, user-driven metric customization.

## Expected Behavior

- Users should be able to define a "view" that selects instruments by name, including support for wildcard patterns where a single-character wildcard matches exactly one character and a multi-character wildcard matches zero or more characters.
- Users should be able to match instruments by their instrumentation library scope (by name, version, and/or schema URL), where only non-empty fields need to match.
- A view should be able to rename a matched instrument or update its description.
- A view should be able to filter an instrument's reported attributes down to a specified set of keys, passing all attributes through if no filter is configured.
- Attempting to create a view with no match criteria should be rejected with an error.
- Attempting to rename an instrument while also using a wildcard name pattern (which could match multiple instruments) should be rejected with an error.

## Why This Matters

Without a view system, SDK users have no way to customize metric output — they cannot rename instruments, suppress unwanted attributes, or adapt instrument descriptions to their needs. This change provides the foundational building block for configurable metric views in the SDK.
