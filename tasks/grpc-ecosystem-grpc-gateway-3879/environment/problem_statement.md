## Description

When generating a field mask from a JSON request body, fields whose values are empty objects are silently dropped from the resulting field mask. This means that if a client sends a field intentionally set to an empty message (such as a variant type that selects an option with no sub-fields), the generated field mask will not include that field — and the corresponding update operation will ignore it entirely.

## Expected Behavior

- When a JSON request body contains a field whose value is an empty object, that field should be recognized as present and included in the generated field mask.
- Fields set to empty objects represent intentional client input and should not be silently discarded.

## Current Behavior

Currently, if a field's value in the JSON body is an empty object, no path is added to the field mask for that field. This causes downstream update operations to skip those fields, even when the client explicitly provided them.

## Why This Matters

Clients using variant fields or similar constructs where one variant is an empty message type cannot currently signal their intent via a JSON request body. The field mask generation drops their input, making it impossible to select such fields through a partial update mechanism. Fixing this ensures that all present fields — including those with empty message values — are faithfully represented in the field mask.
