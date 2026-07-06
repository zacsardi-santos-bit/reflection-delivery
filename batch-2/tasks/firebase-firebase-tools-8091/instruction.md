Update the firebase config validator to align with the new JSON schema validation library's API changes. Ensure that error reporting uses the new property name and path format while maintaining existing validation logic.

*   Modify the firebase config validator to produce error objects with:
    *   An `instancePath` property that identifies the location of validation errors.
    *   An empty string `""` for root-level validation errors in `instancePath`.
    *   Forward-slash notation for nested validation errors in `instancePath` (e.g., `"/storage/rules"`).
    *   Retained `keyword` and `params` properties with the same structure as before.

*   Ensure the validator continues to:
    *   Accept valid `firebase.json` configs without errors.
    *   Identify unknown top-level fields, missing required properties, and fields with incorrect types.

*   Update the following functions in `src/firebaseConfigValidate.ts`:
    *   `getValidator() -> ValidateFunction`: Ensure it returns a JSON schema `ValidateFunction` that uses `instancePath`.
    *   `getErrorMessage(e: ErrorObject) -> string`: Adjust to reference `e.instancePath` when constructing error messages.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.