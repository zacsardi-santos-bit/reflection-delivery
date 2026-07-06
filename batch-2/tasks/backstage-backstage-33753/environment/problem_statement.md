## Description

The catalog backend maintains a database table of registered locations (entries pointing to catalog descriptor files). Each location has a corresponding catalog entity reference that uniquely identifies it. Currently, that reference is not stored in the table — it is recomputed every time from the location's type and URL target. This means the same hash-based computation runs repeatedly on reads, and there is no single source of truth in the database for what entity ref a given location row corresponds to.

## Expected Behavior

- A new column should be added to the locations table to store the pre-computed entity reference for each registered location.
- When a new location is created, its entity reference should be computed once and stored alongside the location in the database.
- Existing location rows in the database should be backfilled with their correct entity reference values via a database migration.
- The internal bootstrap location row (a special placeholder that will be removed in a future migration) should receive an empty string as its entity reference value, since it does not correspond to a real catalog entity.
- Rolling back the migration should remove the new column cleanly.

## Why This Matters

Pre-storing the entity reference avoids redundant computation on every read and makes the relationship between a location row and its corresponding catalog entity explicit and queryable directly from the database. It is a step toward removing the need to recompute this value in application code altogether.
