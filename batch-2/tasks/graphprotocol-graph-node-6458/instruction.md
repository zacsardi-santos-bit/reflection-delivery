I'm working on a graph-node feature where immutable entity types need to support a "skip duplicates" mode.

*   RowGroup::new must accept only entity_type: EntityType as its constructor argument — the previous second boolean parameter for immutability must be removed. Immutability and skip-duplicates behavior must be derived from the entity type itself.

*   The @entity directive in GraphQL schemas must accept a skipDuplicates: Boolean argument that is only valid when combined with immutable: true. Schemas declaring skipDuplicates: true without immutable: true must fail schema validation.

*   EntityType::skip_duplicates() must return true if and only if the entity type is declared with both immutable: true and skipDuplicates: true in its @entity directive, and false for all other entity types.

*   For entity types where EntityType::skip_duplicates() returns true: when an Insert modification is pushed for an entity that was already inserted in a DIFFERENT block, the operation must succeed (Ok) and the duplicate row must be silently discarded — the total row count for that entity must remain 1.

*   For entity types where EntityType::skip_duplicates() returns true: when an Insert modification is pushed for an entity that was already inserted in the SAME block, the operation must succeed (Ok) and the row must be kept — the total row count must increase to 2 (same-block inserts are not discarded).

*   For entity types where EntityType::skip_duplicates() returns true: RowGroup::append_row called with an Overwrite or Remove modification (normally invalid for immutable entities) must return Ok(()) instead of an error.

*   For regular immutable entity types where EntityType::skip_duplicates() returns false, pushing an Insert for an entity already inserted in a different block must return an error (existing behavior preserved).


*   Interface details: Type: Function
Name: RowGroup::new
Location: graph/src/components/store/write.rs
Signature: pub fn new(entity_type: EntityType) -> Self
Description: Constructor for RowGroup. Takes only the entity type — the previous second boolean parameter (immutable: bool) must be removed. Immutability and skip-duplicates behavior are determined from the entity type itself at runtime.

Type: Function
Name: RowGroup::append_row
Location: graph/src/components/store/write.rs
Signature: fn append_row(&mut self, row: EntityModification) -> Result<(), StoreError>
Description: Appends an entity modification row. For entity types where skip_duplicates() returns true: cross-block duplicate inserts must return Ok(()) and be silently discarded (row not added); Overwrite and Remove modifications must return Ok(()) instead of an error. For regular immutable entity types (skip_duplicates() == false), cross-block duplicate inserts still return Err.

Type: Function
Name: EntityType::skip_duplicates
Location: graph/src/schema/entity_type.rs
Signature: pub fn skip_duplicates(&self) -> bool
Description: Returns true if this entity type was declared with both immutable: true and skipDuplicates: true in its @entity directive. Returns false for all other entity types.

Type: Function
Name: InputSchema::skip_duplicates
Location: graph/src/schema/input/mod.rs
Signature: pub(in crate::schema) fn skip_duplicates(&self, entity_type: Atom) -> bool
Description: Returns true if the given entity type atom has skipDuplicates: true in its @entity directive. Used internally by EntityType::skip_duplicates().


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.