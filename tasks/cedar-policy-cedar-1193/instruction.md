Implement the necessary changes to ensure Cedar's schema-based entity validation correctly rejects entities with record attributes that violate the schema. Additionally, provide direct count methods for policies and templates in a policy set.

*   Update the `PolicySet` struct in `cedar-policy/src/api.rs`:
    *   Implement the `num_of_policies(&self) -> usize` method to return the total number of policies in the set without requiring iteration.
    *   Implement the `num_of_templates(&self) -> usize` method to return the total number of templates in the set without requiring iteration.

*   Modify entity validation logic:
    *   When constructing an `Entities` collection from a list of `Entity` values with a `Schema`:
        *   Reject entities with record attributes containing fields not defined in the schema by returning `Err(EntitiesError::InvalidEntity(_))`.
        *   Reject entities with record attributes where an existing field has the wrong type, even if `additionalAttributes: true` is set, by returning `Err(EntitiesError::InvalidEntity(_))`.
    *   When parsing an entity from JSON with a `Schema`:
        *   Reject entities with a nested `Set<Set<Record>>` attribute if any innermost record is missing a required field, returning an error.
        *   Allow parsing to succeed (return `Ok`) if all innermost records have required fields with correct types and any optional fields present also have correct types.
        *   Allow parsing to succeed (return `Ok`) if the entity provides a subset of optional keys with correctly typed values, even for nested optional sub-records.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.