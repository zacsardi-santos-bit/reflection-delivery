## Description

When using the entity generator to reverse-engineer a MySQL database schema into TypeScript entity files, there is an option to also generate scalar properties that expose the raw foreign key column values alongside the relation properties. These scalar properties are marked as non-persisted (virtual/read-only) and are very useful for directly accessing FK values without loading related entities.

The problem is that these generated scalar properties are missing important metadata that reflects the actual database column definition. Specifically, columns that are unsigned, have a specific character length, have auto-increment behavior, or use a non-standard database column type do not have those attributes carried over into the generated scalar property. This results in generated code that is an incomplete and inaccurate representation of the underlying schema.

## Expected Behavior

- When a foreign key column is unsigned (e.g., an unsigned integer), the generated scalar property should include the unsigned flag
- When a foreign key column has a specific character length (e.g., a 2-character country code column), the generated scalar property should include that length
- When a foreign key column has auto-increment set, the generated scalar property should include that attribute
- When a foreign key column has a non-standard database-specific type (such as MySQL's YEAR type), the generated scalar property should include the native column type

## Why This Matters

Without these attributes, developers must manually patch the generated entity files after each round of generation, defeating the purpose of having an automated generator. The generated entities are also subtly wrong — for example, a column that only allows positive numbers appears to allow negative ones, or a fixed-length character column loses its length constraint. The generator should produce accurate, ready-to-use entity files that reflect every relevant property of the underlying database schema.
