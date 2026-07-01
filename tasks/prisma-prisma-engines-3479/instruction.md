Implement multi-schema introspection support for CockroachDB in the Prisma introspection engine. Ensure that the generated Prisma schema correctly annotates models and enums with their respective schemas, and accurately represents cross-schema relationships.

*   Enable multi-schema introspection for CockroachDB:
    *   Ensure each introspected model includes a `@@schema()` attribute set to the schema name where the table resides.
    *   Produce separate model definitions for tables with the same name in different schemas, each with the correct `@@schema()` annotation.
    *   Correct `@@schema()` annotations during re-introspection if they are incorrect in the existing Prisma schema.

*   Handle cross-schema relationships:
    *   Accurately represent cross-schema foreign key relationships, ensuring each model is annotated with its actual schema.
    *   Preserve relation fields and references in the introspected output.

*   Support enums in multi-schema introspection:
    *   Emit each enum with a `@@schema()` attribute matching the schema where it is defined.
    *   Ensure distinct definitions for enums with the same name in different schemas, each with their respective `@@schema()` annotations.

*   Ensure the generated Prisma schema includes:
    *   A datasource block with `provider` set to 'cockroachdb' and a `schemas` array listing all configured namespaces.
    *   A generator block with the `multiSchema` entry in `previewFeatures`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.