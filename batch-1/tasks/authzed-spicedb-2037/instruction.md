Implement a dedicated set type for object-namespace-relation identifiers within the internal development membership package and create a dispatch-grouping structure in the graph package for efficient permission check dispatching.

*   Define the `ONRSet` type in `internal/developmentmembership/onrset.go` as a value type (struct).
    *   Implement a constructor `NewONRSet(onrs ...*core.ObjectAndRelation) ONRSet` to initialize the set.
    *   Implement methods:
        *   `Add(onr *core.ObjectAndRelation) bool` to add an element, returning true if new.
        *   `Has(onr *core.ObjectAndRelation) bool` to check membership.
        *   `Update(onrs []*core.ObjectAndRelation)` to add elements from a slice.
        *   `UpdateFrom(otherSet ONRSet)` to add elements from another set.
        *   `Length() uint64` to return the count of distinct elements.
        *   `IsEmpty() bool` to check if the set is empty.
        *   `Intersect(otherSet ONRSet) ONRSet` to return common elements.
        *   `Subtract(otherSet ONRSet) ONRSet` to return elements not in the other set.
        *   `Union(otherSet ONRSet) ONRSet` to return a new set with all elements from both sets.
        *   `AsSlice() []*core.ObjectAndRelation` to convert the set to a slice.
*   Update the `FoundSubject` type in `internal/developmentmembership` to use `ONRSet` for its relationships field.
    *   Remove any nil checks on this field.
*   Remove the shared utility package's implementation and tests for the set type.

*   Add the `checkDispatchSet` type in `internal/graph/checkdispatchset.go`.
    *   Implement `newCheckDispatchSet() *checkDispatchSet` to create a new instance.
    *   Implement `addForRelationship(tpl *core.RelationTuple)` to accumulate relationship data.
    *   Implement `dispatchChunks(dispatchChunkSize uint16) []checkDispatchChunk` to group subjects by type and split them into chunks.
        *   Ensure chunks are grouped by subject type (namespace + relation).
        *   Separate subjects with caveats from those without into different chunks.
        *   Ensure `hasIncomingCaveats` is true if any subject in the chunk has a caveat.
    *   Implement `mappingsForSubject(subjectType string, subjectObjectID string, subjectRelation string) []resourceIDAndCaveat` to return resources related to a subject, including caveat information.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.