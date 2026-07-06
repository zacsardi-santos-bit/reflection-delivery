## Description

The schema pruning utility has two correctness gaps and one behavioral issue with its custom filtering mechanism.

**Gap 1 — Unused custom scalars are not removed.**
When a schema defines a custom scalar type that is never referenced by any field reachable from the root, pruning the schema should remove that scalar. Currently it survives pruning, leaving the schema larger than it should be.

**Gap 2 — Unused interface implementations are not removed.**
When a schema has multiple types that all implement the same interface, but only one of those implementations is actually reachable from a root type (via a query field), the unreachable implementations should be pruned. Currently they are retained even though no query path can reach them.

**Gap 3 — The "skip pruning" filter does not cascade to type dependencies.**
There is an option to provide a custom filter function that tells the pruner to keep certain types intact. However, when the pruner keeps a type because the filter matched it, it does not also preserve the types that the kept type depends on — for example, any interfaces the kept type implements. This means those interface types get pruned even though the kept type relies on them, which can produce an inconsistent schema.

## Expected Behavior

- Custom scalars not reachable from any root type should be removed during pruning.
- Skipping unused type pruning via an option flag should also apply to unused custom scalars.
- Object types implementing an interface but not reachable from the root should be removed during pruning.
- When a type is protected from pruning by a custom filter, all types it depends on (such as the interfaces it implements) should also be preserved.

## Why This Matters

Together these fixes ensure that schema pruning is complete, correct, and safe to use with custom type-protection filters.
