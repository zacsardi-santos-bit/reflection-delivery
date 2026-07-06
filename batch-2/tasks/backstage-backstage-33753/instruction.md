I'm working on the catalog backend plugin and want to add a new column to the locations database table that stores the pre-computed entity reference for each location row.

*   The `computeLocationEntityRef` function must be exported from `plugins/catalog-backend/src/util/conversion.ts` and accept two string parameters: `type` and `target`. It must return a string in the format `location:default/generated-{sha1hex}` where `sha1hex` is the lowercase hexadecimal SHA-1 digest of the concatenated string `{type}:{target}`. The result must be fully lowercased using `toLocaleLowerCase('en-US')`.

*   For input `('url', 'https://github.com/backstage/demo/blob/master/catalog-info.yml')`, `computeLocationEntityRef` must return exactly `'location:default/generated-fa35d9c166e43ab7f4a7c59a00e88e4e8b5aba34'`.

*   The `DbLocationsRow` type in `plugins/catalog-backend/src/database/tables.ts` must include a required `location_entity_ref: string` field.

*   When creating a new location, the `location_entity_ref` value computed from the location's `type` and `target` must be persisted in the `locations` database row at insert time. Reading the row back from the database must yield the correct `location_entity_ref` value.

*   All rows read from the `locations` database table must include the `location_entity_ref` field. Queries over the locations table (including those used in event handling and pagination) must return rows that include this field.

*   A new database migration file named `20260403000000_add_location_entity_ref.js` must be created in `plugins/catalog-backend/migrations/`. Before the migration runs, the `locations` table must have no `location_entity_ref` column. After the migration runs, the column must exist.

*   The migration must backfill the `location_entity_ref` column: non-bootstrap rows (where `type != 'bootstrap'`) must receive a value of `location:default/generated-{sha1hex}` (SHA-1 of `{type}:{target}`, lowercased). Rows with `type = 'bootstrap'` must receive an empty string `''` as a placeholder. Different targets must produce distinct entity refs.

*   The migration's rollback (`exports.down`) must drop the `location_entity_ref` column from the `locations` table, so that after rollback the column no longer exists.


*   Interface details: Type: Function
Name: computeLocationEntityRef
Location: plugins/catalog-backend/src/util/conversion.ts
Signature: computeLocationEntityRef(type: string, target: string): string
Description: Computes and returns the full entity ref string for a Location kind entity corresponding to a stored location row. The entity ref is formed as `location:default/generated-{sha1hex}` where `sha1hex` is the lowercase SHA-1 hex digest of the string `{type}:{target}`. The result is lowercased via `toLocaleLowerCase('en-US')`. For example, `computeLocationEntityRef('url', 'https://github.com/backstage/demo/blob/master/catalog-info.yml')` returns `'location:default/generated-fa35d9c166e43ab7f4a7c59a00e88e4e8b5aba34'`. This function must be exported from the module.

Type: Interface Field
Name: location_entity_ref
Location: plugins/catalog-backend/src/database/tables.ts
Description: The `DbLocationsRow` type must include a required field `location_entity_ref: string`. This field stores the pre-computed entity ref for the Location kind entity corresponding to the row (e.g., `location:default/generated-<sha1hex>`). The internal bootstrap location row uses an empty string as a placeholder.

Type: Migration File
Name: 20260403000000_add_location_entity_ref.js
Location: plugins/catalog-backend/migrations/20260403000000_add_location_entity_ref.js
Description: A new Knex migration file that adds a `location_entity_ref` column to the `locations` table. The migration must: (1) add the column to the `locations` table, (2) backfill all non-bootstrap rows with their entity ref computed as `location:default/generated-{sha1('type:target').hex}` (lowercased), (3) set the bootstrap row (where `type = 'bootstrap'`) to an empty string `''`, (4) tighten the column to NOT NULL after backfill. The `exports.down` function must drop the `location_entity_ref` column, restoring the table to its prior state.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.