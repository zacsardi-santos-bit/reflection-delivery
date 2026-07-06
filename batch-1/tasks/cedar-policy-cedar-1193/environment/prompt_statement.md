I'm working with Cedar's schema-based entity validation and found a bug: when an entity has a record attribute that contains extra fields not allowed by the schema, the validation passes instead of rejecting the entity. For example, if my schema says a record should only have one specific field, but the entity has that field plus an extra one, it should fail validation — but currently it doesn't.

This problem also shows up for more complex nested structures, like sets of sets of records. If a record inside the innermost set is missing a required field, validation should reject it, but instead it passes through. Conversely, valid entities with the correct structure (required fields present, optional fields either absent or correctly typed) should still pass.

I'd also like to have a more convenient way to get the count of policies and templates in a policy set, rather than having to iterate over a collection just to count items.

Could you fix the entity validation logic so that record attributes are properly checked against the schema — rejecting entities with extra fields, wrong-typed fields, or missing required fields inside nested structures — while still accepting valid entities? And please add direct count methods on the policy set for policies and templates.
