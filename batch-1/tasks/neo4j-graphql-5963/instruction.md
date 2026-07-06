Implement enhanced validation error messages and paths for the Neo4j GraphQL library's schema validation. Ensure that directives requiring a type to be a graph node produce clear, actionable error messages when misused. Update the `validateDocument` function to enforce these validations consistently across all usage locations.

Requirements:
*   Implement the `validateDocument` function in `packages/graphql/src/schema/validation/validate-document.ts` with the following signature:
    *   `validateDocument({ document: DocumentNode, additionalDefinitions: { enums?: EnumTypeDefinitionNode[], interfaces?: InterfaceTypeDefinitionNode[], unions?: UnionTypeDefinitionNode[], objects?: ObjectTypeDefinitionNode[] }, features: object }): void`
*   Ensure the following validations and error messages:
    *   For `@relationship` on non-`@node` types, throw: 'Directive "relationship" requires in a type with "@node"'. Include the directive name in the error path.
    *   For `@cypher` on non-`@node` and non-root types, throw: 'Directive "cypher" requires in a type with "@node" or on root types: Query, and Mutation'. Include the directive name in the error path.
    *   For `@populatedBy` on non-`@node` and non-`@relationshipProperties` types, throw one error: 'Directive "populatedBy" requires in a type with "@node" or within the "@relationshipProperties" directive'. Include the directive name in the error path.
    *   For `@relayId` on non-`@node` types, throw: 'Directive "relayId" requires in a type with "@node"'. Include the directive name in the error path.
    *   For `@authorization` on non-`@node` types, throw: 'Directive "@authorization" requires in a type with "@node"'. Apply this to type extensions and include the directive name in the error path.
    *   For `@authorization` on root Query fields, throw: 'Directive @authorization is not supported on fields of the Query type. Did you mean to use @authentication?'
    *   For `@authorization` on relationship property fields, throw: 'Directive "@authorization" requires in a type with "@node"'. Include '@authorization' in the error path.
    *   For `@authentication` on relationship property fields, throw: 'Directive "authentication" requires in a type with "@node" or in root types: Query, and Mutation'. Include '@authentication' in the error path.
    *   For `@subscriptionsAuthorization` on relationship property fields, throw: 'Directive "@subscriptionsAuthorization" requires in a type with "@node"'. Include '@subscriptionsAuthorization' in the error path.
    *   For `@relationship` on relationship property fields, throw: 'Directive "relationship" requires in a type with "@node"'. Include '@relationship' in the error path.
    *   For `@cypher` on relationship property fields, throw: 'Directive "cypher" requires in a type with "@node" or on root types: Query, and Mutation'. Include '@cypher' in the error path.
    *   For `@default` with invalid DateTime values, throw: '@default.value on DateTime fields must be of type DateTime'. Use the error path ["TypeName", "fieldName", "@default", "value"].
    *   For `@coalesce` on Spatial or Temporal fields, ensure the error path ends at the directive level.
    *   For `@authorization` with no valid arguments, produce exactly one error for missing required arguments.
*   Recognize and validate `@relationshipProperties` types within the main document without needing additional definitions.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.