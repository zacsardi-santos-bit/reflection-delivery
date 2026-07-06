## Description

Cedar's schema-based entity validation is not correctly rejecting entities whose record attributes contain fields that violate the schema. If a schema defines a record type with a specific set of fields, an entity can be constructed with that record containing extra, disallowed fields and validation passes silently — when it should be rejected. This bug exists for record attributes directly on entities as well as for records nested inside sets (e.g., a set of sets of records).

## Expected Behavior

- When an entity is validated against a schema, any record attribute that contains a field not permitted by the schema must be rejected with an invalid entity error.
- When an entity has a record attribute with a field of the wrong type, it must also be rejected.
- These checks must apply recursively for nested structures like sets of records or sets of sets of records.
- Valid entities whose attributes fully conform to the schema (including correct handling of required vs. optional fields) must continue to pass validation.

## Additional Request

It would also be convenient to have direct count methods on a policy set for both policies and templates, rather than needing to obtain an iterator and call count on it.

## Why This Matters

This is a correctness issue: the schema is meant to be a contract that all entities must satisfy. Silent schema violations undermine the guarantees that Cedar provides, and may allow malformed data to flow through authorization checks undetected.
