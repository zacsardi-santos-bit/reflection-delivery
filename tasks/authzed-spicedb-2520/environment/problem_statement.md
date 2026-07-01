## Description

There are two related issues with how the permission system handles schema changes and query validation:

**Bug: Incorrect relationship check when removing one of multiple allowed subject type variants**

When a relation allows both a plain subject type reference and a subject type with a specific sub-relation (e.g., both a bare type reference and a type-through-relation reference), removing just one of those allowed variants should only be blocked if relationships actually exist for that specific variant. Currently, the system checks incorrectly and may block a valid schema change (when no relationships of the exact removed variant exist) or, conversely, fail to block a removal when it should.

For example, if a relation allows both direct type references and type-through-relation references:
- Removing the direct type variant should succeed if only type-through-relation relationships exist
- Removing the type-through-relation variant should succeed if only direct type relationships exist
- Either removal should be blocked if the respective variant actually has matching relationships in the data

**Feature: Support for new query shape categories**

The system needs three new named query patterns for common relationship lookup scenarios:
- Finding resources of a given type and relation that are associated with subjects of a specific type and relation
- Finding subjects of a specific type and relation across all resources (without any resource filter)
- Finding resource-relation pairs that match a given subject-relation combination

Each new pattern needs proper index selection for efficient database queries and validation rules that enforce the required and forbidden filter fields.

## Expected Behavior

- Removing an allowed subject type variant from a relation should be blocked only when relationships of exactly that variant exist, not when other variants of the same type namespace exist
- Each new query pattern enforces strict input validation: certain fields are required, others are forbidden, and exactly one subject selector is required per query
- Every named query pattern must have an appropriate database index hint assigned to it
