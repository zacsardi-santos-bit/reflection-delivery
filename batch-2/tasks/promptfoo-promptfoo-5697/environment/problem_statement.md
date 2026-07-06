## Description

When filtering evaluation results by metadata, users can currently only match on specific values — checking whether a metadata field equals a string or contains a substring. There is no way to simply filter for results where a metadata field *exists* at all, regardless of what value it holds. This is a significant gap: users often want to segment results by whether a piece of metadata was recorded, without knowing or caring about its exact value.

## Expected Behavior

- A new "Exists" filter operator should be available for metadata field filters, allowing users to find results where a metadata field is present and has a meaningful value.
- The "Exists" operator should match records where the metadata field holds any non-empty value — numbers (including zero), booleans (including false), arrays and objects (including empty ones), and non-blank strings.
- The "Exists" operator should NOT match records where the field is absent, null, an empty string, or a whitespace-only string.
- Metadata field names that contain special characters (such as quotes or backslashes) must be handled correctly when filtering.
- In the filter UI, the "Exists" option should only appear for metadata-type filters, not for other filter types (metrics, plugins, etc.).
- When "Exists" is selected as the operator, the value input should be hidden since no value is needed.
- Selecting the "Exists" operator should clear any previously entered filter value.
- Switching the filter type away from metadata while "Exists" is selected should reset the operator to "Equals."
- A metadata filter using the "Exists" operator with a field selected should count as an applied filter, even though no value is entered.

## Why This Matters

Without this feature, it is impossible to segment evaluation results based on metadata presence alone. For example, there is no way to quickly find all runs that have a "source" field set to any value, which makes metadata-based workflows harder to manage at scale.
