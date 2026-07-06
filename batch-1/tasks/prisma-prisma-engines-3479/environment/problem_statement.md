## Description

The Prisma introspection engine supports a multi-schema preview feature that allows developers to work with tables and types spread across multiple database schemas within a single Prisma schema. This feature works for some databases, but it does not work correctly for CockroachDB. When CockroachDB users try to introspect a database that uses multiple schemas, the engine fails to properly assign the schema annotation to each model and enum, making it impossible to use multi-schema support with CockroachDB.

## Expected Behavior

- When introspecting a CockroachDB database configured with multiple schemas, each model and enum in the generated Prisma schema should be annotated with the correct schema it belongs to.
- Tables that share the same name but exist in different schemas should each produce a separate model definition with the appropriate schema annotation.
- Enums that share the same name but exist in different schemas should each produce a separate enum definition with the appropriate schema annotation.
- Cross-schema foreign key relationships should be correctly represented, with both sides of the relation annotated with their actual schema location.
- Re-introspecting an existing Prisma schema should correctly update schema annotations that may be incorrect, assigning each model to the schema where it actually lives in the database.

## Why This Matters

CockroachDB users who organize their data across multiple schemas are completely blocked from using the multi-schema preview feature. Without this support, they cannot use Prisma's introspection to generate or update their Prisma schema, which is a core part of the Prisma workflow. Adding this support brings CockroachDB to parity with other supported databases for multi-schema introspection.
