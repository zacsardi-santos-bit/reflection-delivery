Implement a reusable hook and a story component to support form field pre-population from the current navigation context in a React Admin application. Ensure forms can automatically apply location-based data, overriding existing records and default values as necessary, and demonstrate this functionality in a multi-route tabbed form scenario.

*   Create a new module at `packages/ra-core/src/form/useRecordFromLocation.ts` and re-export its contents from `packages/ra-core/src/form/index.ts`.
    *   Implement `getRecordFromLocation(location: Location, options?: { searchSource?: string; stateSource?: string }): Partial<RaRecord> | null`.
        *   Extract a record from `location.state['record']` by default, or use `options.stateSource` if provided.
        *   Parse a JSON object from the URL search string under the key 'source', or use `options.searchSource` if provided.
        *   Return the state record if both state and search contain records; otherwise, return the search record or null if neither contains a record.
    *   Implement `useRecordFromLocation(props?: UseRecordFromLocationOptions): Partial<RaRecord> | null`.
        *   Use the current router location and delegate to `getRecordFromLocation`.
        *   Return null if no record is found in the location state or search.
    *   Define and export `UseRecordFromLocationOptions` with optional fields `searchSource` and `stateSource`.
        *   Re-export `UseRecordFromLocationOptions` from the ra-core package root via `packages/ra-core/src/form/index.ts`.

*   Develop a `MultiRoutesForm` story component in `packages/ra-core/src/form/Form.stories.tsx`.
    *   Accept `url`, `initialRecord`, and `defaultValues` as props.
    *   Render a tabbed form with a 'General' tab (containing 'title' and 'category' inputs) and a 'Settings' tab (containing a 'body' input).
    *   Include a Submit button that reflects the form's dirty state based on field modifications.
    *   Use `TestMemoryRouter` with the provided `url` to ensure correct application of location-based pre-population.

*   Ensure the form component automatically pre-fills fields with location-based data when present, persisting these values across sub-route navigation.
*   Override initialRecord values with location-based values for matching fields, retaining initialRecord or defaultValues for other fields.
*   Ensure the Submit button is not disabled after pre-filling from location data, indicating modified fields.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.