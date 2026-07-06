Implement enhancements to the partial caching system to correctly handle GraphQL queries using type conditions with unions or interfaces. Ensure that the cache merging process is aware of concrete object types and includes type metadata in sub-queries to resolve type-specific cached fields accurately.

*   Update the `merge_cache_entry` method:
    *   Add a parameter `type_relationships: &'a dyn TypeRelationships` to handle subtype relationships.
    *   Ensure it resolves polymorphic shapes using the `__typename` field during cache merging.

*   Update the `merge_specific_defer_from_cache_entry` method:
    *   Add a parameter `type_relationships: &'a dyn TypeRelationships` for subtype relationship resolution.
    *   Ensure it resolves polymorphic shapes using the `__typename` field during cache merging.

*   Implement the `NoSubtypes` struct:
    *   Ensure it implements the `TypeRelationships` trait.
    *   Use it in tests where no subtype relationships are needed.

*   Modify sub-query generation:
    *   Automatically inject a `__typename` field in selection sets containing fragments, unless explicitly present.
    *   Apply this injection at all nesting levels of selection sets.

*   Update the `PolymorphicShape` type:
    *   Implement `concrete_shape_for_typename` to resolve the correct `ConcreteShape` using `typename` and `type_relationships`.

*   Ensure correct cache and response merging:
    *   Use `__typename` to resolve `ConcreteShape` for polymorphic shapes during merging.
    *   Handle both cache entries and live responses.

*   Validate the partial caching system:
    *   Ensure correct responses for queries using union inline fragments and named fragments.
    *   Ensure correct responses for queries using interface inline fragments and named fragments.
    *   Ensure compatibility with the `@defer` directive for streaming responses.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.