We're splitting our existing monolithic NgRx generator for Angular into two separate, purpose-built generators — one for setting up root store state management and one for generating feature state.

*   The migrateNgrxGeneratorDefaults function must be the default export of its module and accept a Tree parameter, returning a Promise.

*   When nx.json has no generators field, or when no '@nx/angular:ngrx' defaults are set (in flat or nested form), the function must make no changes to the configuration.

*   When flat '@nx/angular:ngrx' defaults are present, the function must delete the old key and redistribute its options: facade, minimal, and directory go to '@nx/angular:ngrx-root-store'; facade, barrels, and directory go to '@nx/angular:ngrx-feature-store'. The 'minimal' option must NOT be written to '@nx/angular:ngrx-feature-store'.

*   When nested '@nx/angular.ngrx' defaults are present, the function must remove the old 'ngrx' key from the '@nx/angular' collection and write to '@nx/angular['ngrx-root-store']' and '@nx/angular['ngrx-feature-store']'. If both nested and flat ngrx defaults coexist, the flat format takes precedence (flat overrides nested), and output is written in flat format.

*   When a 'module' option is present in the old defaults, the function must rename it to 'parent' on the '@nx/angular:ngrx-feature-store' key. '@nx/angular:ngrx-root-store' must not receive 'parent' or 'module' options. When both 'module' and 'parent' are set, 'module' takes precedence — its value becomes the migrated 'parent' value.

*   The obsolete 'root' toggle must be dropped entirely. If dropping it leaves no remaining options to write, no new generator keys must be created (do not write empty defaults).

*   When existing defaults are already present under the new generator keys ('@nx/angular:ngrx-root-store' or '@nx/angular:ngrx-feature-store'), the migration must merge the migrated options into them rather than overwriting the existing values. User-set values on the new keys take priority.

*   When the user already has the new generator keys expressed in nested format (e.g. '@nx/angular['ngrx-root-store']'), migrated output for that key must also be written in nested format under '@nx/angular', rather than creating a competing flat key.

*   After removing the old 'ngrx' entry from a nested '@nx/angular' collection, if the collection becomes empty (no other sibling keys remain and no new defaults were written), the function must remove the '@nx/angular' key entirely. If new keys were written into it, the collection must be preserved.

*   The function must process both nx.json and all project.json files in the same migration run, applying the same redistribution logic independently to each file. Projects without '@nx/angular:ngrx' defaults (flat or nested) must be left completely untouched.


*   Interface details: Type: Function
Name: migrateNgrxGeneratorDefaults
Location: packages/angular/src/migrations/update-23-0-0/migrate-ngrx-generator-defaults.ts
Signature: migrateNgrxGeneratorDefaults(tree: Tree): Promise<void>
Description: Default export. Migrates old @nx/angular:ngrx generator defaults from both nx.json and project.json files to the new @nx/angular:ngrx-root-store and @nx/angular:ngrx-feature-store generator keys. Handles both flat format (e.g. '@nx/angular:ngrx': {...}) and nested format (e.g. '@nx/angular': { ngrx: {...} }). Deletes the old ngrx key after migration and cleans up any empty collection buckets.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.