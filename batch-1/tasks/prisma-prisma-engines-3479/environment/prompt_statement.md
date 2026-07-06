I'm working with CockroachDB and have multiple schemas in my database. When I enable the multi-schema preview feature in Prisma and try to introspect my CockroachDB database, the schema annotations on my models and enums are not being set correctly — or the introspection doesn't work at all for CockroachDB.

For other databases like PostgreSQL, multi-schema introspection works fine: each model gets an annotation indicating which schema it belongs to, duplicate table or enum names across schemas each get their own definition, and cross-schema foreign key relationships are preserved properly. I need the same behavior for CockroachDB.

Specifically, when I re-introspect an existing Prisma schema that has incorrect schema assignments (e.g., a model listed under the wrong schema), the engine should correct those assignments to match the actual database. When there are tables or enums with the same name in two different schemas, both should appear in the output with distinct schema annotations. And when a foreign key crosses schema boundaries, the relation should still be modeled correctly with each side tagged with its actual schema.

Could you add multi-schema introspection support for CockroachDB so it behaves consistently with the other supported databases?
