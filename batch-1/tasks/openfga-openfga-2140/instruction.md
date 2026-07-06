Implement an optimization for userset relationships with set operations in the OpenFGA authorization system. Update the naming conventions for internal components to ensure consistency and clarity. Add a new analysis method to determine eligibility for fast-path evaluation of userset relationships.

*   Rename components for consistency:
    *   Change the constant `NestedUsersetKind` to `UsersetKind` throughout the `internal/graph` package.
    *   Rename the struct `NestedUsersetMapper` to `UsersetMapper` in `internal/graph/tuplemapper.go`.
    *   Ensure `wrapIterator` returns a `*UsersetMapper` when called with `UsersetKind`.

*   Implement the `UsersetCanFastPathWeight2` method in `pkg/typesystem/typesystem.go`:
    *   Method signature: `(t *TypeSystem) UsersetCanFastPathWeight2(objectType, relation, userType string, allowedUsersets []*openfgav1.RelationReference) bool`.
    *   Return `true` for:
        *   Simple usersets with direct user assignments.
        *   Multiple userset types satisfying the weight-2 constraint.
        *   Exclusion/difference, union, and intersection relations.
        *   Multiple direct user assignments and relation references.
        *   Computed usersets and nested computed usersets.
        *   Conditional relations on parent or child levels.
    *   Return `false` for:
        *   Self-referencing or recursive usersets.
        *   Wildcard/public assignments in userset chains.
        *   Involvement of tuple-to-userset (TTU) relationships.
        *   Usersets containing another nested userset as a member type.

*   Ensure the authorization check system:
    *   Correctly evaluates complex algebraic combinations (union, intersection, exclusion) in userset relationship chains.
    *   Grants access when conditions are met and denies access when users are excluded by set operations.
    *   Handles multiple parent userset types simultaneously, returning `true` if any path grants access.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.