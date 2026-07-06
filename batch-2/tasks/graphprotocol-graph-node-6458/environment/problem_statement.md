## Description

Immutable entity types in subgraphs currently reject any attempt to write the same entity across different blocks, returning an error. This is a problem for subgraphs that use block polling handlers or other indexing patterns where the mapping naturally produces the same entity data on every block. There is currently no way to declare that an immutable entity type should silently ignore duplicate writes across blocks.

## Expected Behavior

- It should be possible to annotate an immutable entity type so that if the same entity is written again in a later block, that write is silently discarded rather than causing a failure.
- When an entity write is skipped due to this annotation, the operation should report success (not an error).
- Attempts to overwrite or delete entities of this annotated type (which are normally invalid operations for immutable entities) should also be silently ignored and return success.
- Same-block duplicate writes for this entity type should still be kept (not dropped).
- Standard immutable entity types without this annotation must continue to reject cross-block duplicates with an error, preserving existing behavior.

## Why This Matters

Subgraphs with block polling handlers commonly write the same entity on every block. Without this feature, any such entity declared as immutable will fail on the second block it is processed. Developers need a way to express "this entity is immutable but can be safely written multiple times — just keep the first write and ignore subsequent ones."
