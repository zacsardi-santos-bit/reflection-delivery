Implement schema generation for GraphQL interface types to ensure that relationship fields include full querying capabilities. Ensure that these capabilities are consistent with those provided for concrete types, including filtering, pagination, and aggregation options.

*   Ensure that when a GraphQL interface type defines a relationship field to another node type, the generated schema includes the following querying arguments on that interface field:
    *   `directed: Boolean = true`
    *   `options: <RelatedType>Options`
    *   `where: <RelatedType>Where`

*   Ensure that the corresponding connection field on the interface includes pagination and filtering arguments:
    *   `after: String`
    *   `directed: Boolean = true`
    *   `first: Int`
    *   `sort: [<Interface><Relationship>ConnectionSort!]`
    *   `where: <Interface><Relationship>ConnectionWhere`

*   In the experimental schema mode, generate the following for interface relationship fields:
    *   An aggregate input type containing:
        *   Logical operators: `AND`, `NOT`, `OR`
        *   Count comparison fields: `count`, `count_GT`, `count_GTE`, `count_LT`, `count_LTE`
        *   A `node` field pointing to a node-level aggregation where input
    *   A node-level aggregation where input type containing:
        *   Logical operators: `AND`, `NOT`, `OR`
        *   Field-level equality filters, such as `id_EQUAL: ID @deprecated(reason: "Aggregation filters that are not relying on an aggregating function will be deprecated.")`

*   Ensure the interface's `Where` input type includes:
    *   A deprecated singular relationship filter
    *   An aggregate filter (`moviesAggregate`)
    *   A deprecated singular connection filter
    *   Collection-style connection and relationship filters with suffixes: `_ALL`, `_NONE`, `_NOT` (deprecated), `_SINGLE`, and `_SOME`

*   Apply all behaviors consistently, regardless of whether the interface type has additional directives (e.g., custom schema directives).

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.