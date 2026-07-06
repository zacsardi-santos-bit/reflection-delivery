Create and register a new JSON schema for the ABCSupplyPlan version 4.0.0 to ensure proper validation support in SchemaStore. Update the schema catalog and validation configuration to include the new version and its custom validation rules.

*   Create a JSON schema file at `src/schemas/json/abc-supply-plan-4.0.0.json`:
    *   Set `$id` to `https://json.schemastore.org/abc-supply-plan-4.0.0.json`.
    *   Use JSON Schema draft-07.
    *   Define required top-level properties: `$schema`, `planDate`, `planNotes`, `abcMaterialsMap`, `recipeMap`.
    *   Disallow undeclared top-level additional properties.
    *   Ensure `$schema` is a string exactly matching `https://json.schemastore.org/abc-supply-plan-4.0.0.json`.
    *   Type `planDate` as a string with format `date`; reject non-string values.
    *   Disallow additional properties at each object level.
    *   Type `lotSize` as an integer; reject fractional values.
    *   Type `abcMaterialName`, `firmOrderName`, and `lotNumber` as strings; reject numeric values.

*   Update `src/api/json/catalog.json`:
    *   Add `"4.0.0": "https://json.schemastore.org/abc-supply-plan-4.0.0.json"` to the `versions` object of the ABCSupplyPlan entry.
    *   Update the default `url` for the ABCSupplyPlan entry to `https://json.schemastore.org/abc-supply-plan-4.0.0.json`.

*   Update `src/schema-validation.jsonc`:
    *   Add a new entry keyed `"abc-supply-plan-4.0.0.json"`.
    *   Declare `unknownFormat` with `["abc-draft-js_RawDraftContentState"]`.
    *   List custom keywords in `unknownKeywords`: `abcIsFirstDayOfMonth`, `abcIsLastDayOfMonth`, `abcIsAfter0001-01-01`, `abcIsBefore9999-12-31`, `abcDoMaterialIDsExist`, `abcIsAcyclic`, `abcAreAllocationMethodsHomogeneous`, `abcIsValidColor`, `abcNoDuplicateValuesForOrderingProperty`, `abcHasNonOverlappingTimeDependentValues`, `abcHasUninterruptedTimeDependentValues`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.