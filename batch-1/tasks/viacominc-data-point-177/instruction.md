Refactor the entity factory to separate the entity identifier from the specification object. Implement a validation utility to ensure entity configurations use only recognized property names, providing descriptive errors for unrecognized keys.

*   Update the `create` function in `packages/data-point/lib/entity-types/entity-hash/factory.js`:
    *   Accept the entity id as a second string argument instead of reading it from the spec object.
    *   Forward the id to internal compose validation to ensure error messages reference the actual entity id.

*   Create a new module at `packages/data-point/lib/entity-types/validate-modifiers/validate-modifiers.js`:
    *   Implement `validateProperties(id, spec, validKeys)`:
        *   Throw an error if the spec object contains any property key not in the validKeys array.
        *   Use the error message format: `Entity "{id}" did not recognize the following properties:\n {unrecognizedKeys}\nValid properties for this entity are:\n {validKeys}\nPlease review your entity and make any necessary corrections so it can be parsed:\n'{id}': {util.inspect(spec)}`.
        *   Return true if all spec keys are in validKeys or if spec is empty.
    *   Implement `validateModifiers(id, spec, validKeys)`:
        *   Behave like `validateProperties` but allow these base modifier keys: `inputType`, `before`, `value`, `after`, `outputType`, `error`, `params`.
        *   Throw an error only if spec contains a key not in the base set or validKeys array.
        *   Return true otherwise.

*   Create an index file at `packages/data-point/lib/entity-types/validate-modifiers/index.js`:
    *   Re-export everything from `validate-modifiers.js` to allow other modules to access `validateModifiers` and `validateProperties`.

*   Update the `helpers` module at `packages/data-point/lib/helpers/helpers.js`:
    *   Export `validateModifiers` from the `validate-modifiers` module under the key `validateEntityModifiers`.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.