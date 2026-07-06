Update the `pruneSchema` function to address the identified gaps in the schema pruning utility. Ensure that unused custom scalars and interface implementations are removed, and that the custom filter function properly preserves type dependencies.

*   Implement removal of unused custom scalar types:
    *   Ensure `pruneSchema` removes custom scalar types not referenced by any field reachable from root types.
*   Implement preservation of unused custom scalar types when specified:
    *   When `skipUnusedTypesPruning` is true, ensure `pruneSchema` retains unused custom scalar types.
*   Implement removal of unreachable interface implementations:
    *   Ensure `pruneSchema` removes object types implementing an interface if they are not reachable from any root type.
*   Implement cascading preservation of type dependencies:
    *   When a `skipPruning` filter function is provided and returns true for a type, ensure `pruneSchema` preserves that type and all its dependencies, including interfaces it implements.
*   Update the `pruneSchema` function located at `packages/utils/src/prune.ts` with the following signature:
    *   `pruneSchema(schema: GraphQLSchema, options?: PruneSchemaOptions): GraphQLSchema`
*   Ensure the `PruneSchemaFilter` type alias in `packages/utils/src/types.ts` is used correctly:
    *   `(type: GraphQLNamedType) => boolean`
*   Ensure the `PruneSchemaOptions` interface in `packages/utils/src/types.ts` is used correctly:
    *   Includes `skipPruning` and `skipUnusedTypesPruning` fields.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.