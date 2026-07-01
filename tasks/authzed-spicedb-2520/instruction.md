Implement fixes for schema change validation and add support for new query patterns in the SpiceDB permission system. Ensure that schema changes are correctly validated and that new query patterns are defined and validated according to the specified requirements.

Requirements:

*   Define new query shapes in the `queryshape` package:
    *   Create an exported variable `AllSpecificQueryShapes` of type `[]Shape` in `pkg/datastore/queryshape/queryshape.go` to include all non-varying query shapes.
    *   Define the constant `FindResourceAndSubjectWithRelations` as `Shape = "find-resource-and-subject-with-relations"` and replace the former `FindResourceOfTypeAndRelation`.
    *   Ensure `FindSubjectOfTypeAndRelation` and `FindResourceRelationForSubjectRelation` remain as `Shape`-typed constants.

*   Implement indexing hints:
    *   Ensure `IndexingHintForQueryShape` in `internal/datastore/crdb/schema/indexes.go` returns a non-nil, non-NoIndexingHint value for all shapes in `AllSpecificQueryShapes`.

*   Validate new query shapes:
    *   For `FindResourceAndSubjectWithRelations`, enforce validation rules in `validateQueryShape` in `internal/datastore/proxy/indexcheck/queryshapevalidators.go`:
        *   Require `OptionalResourceType`, `OptionalResourceRelation`, at least one and exactly one subjects selector, subject type in the selector, and a non-empty subject relation filter.
        *   Forbid `OptionalResourceIds` and subject ids in the selector.
    *   For `FindSubjectOfTypeAndRelation`, enforce:
        *   Forbid `OptionalResourceType`, `OptionalResourceIds`, and `OptionalResourceRelation`.
        *   Require at least one and exactly one subjects selector, subject type in the selector, and a non-empty subject relation filter.
        *   Forbid subject ids in the selector.
    *   For `FindResourceRelationForSubjectRelation`, enforce:
        *   Require `OptionalResourceType`, `OptionalResourceRelation`, at least one and exactly one subjects selector, subject type in the selector, and a non-empty subject relation filter.

*   Update schema change validation:
    *   In `ApplySchemaChanges` in `internal/services/shared/schema.go`, ensure removal of a subject type variant is blocked only if relationships of that exact variant exist.
    *   Return an error with the pattern 'cannot remove allowed type `<type>` from relation `<relation>` in object definition `<definition>`, as a relationship exists with it' when applicable.

*   Update steel-thread testing infrastructure:
    *   Define `stClients` struct in `internal/services/steelthreadtesting/definitions.go` with fields `PermissionsClient` and `SchemaClient`.
    *   Update `stOperation` type to accept `stClients` as its second parameter.
    *   Modify existing operation functions to use `clients.PermissionsClient` for permissions calls.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.