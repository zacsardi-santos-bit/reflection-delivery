Implement a validation layer for the gRPC service generation tool to ensure GraphQL schemas are checked for unsupported patterns before generating output files. Use a dedicated visitor class to encapsulate the validation logic, distinguishing between warnings and errors.

*   Implement the `SDLValidationVisitor` class in `protographic/src/sdl-validation-visitor.ts`.
    *   Accept a GraphQL SDL string in the constructor.
    *   Provide a `visit()` method that returns an object with two arrays: `errors` and `warnings`.

*   Ensure the `visit()` method behaves as follows:
    *   Return empty `errors` and `warnings` arrays for a valid schema with no unsupported features.
    *   For a schema with nullable items in a list type, produce one warning: 'Nullable items are not supported in list types', and no errors.
    *   For a schema with a nested nullable item inside a list-of-lists, produce one warning: 'Nullable items are not supported in list types', and no errors.
    *   For a schema where a type's @key directive references nested fields, produce one error: 'Nested key directives are not supported', and no warnings.
    *   For a schema where a field uses a @requires directive, produce one warning: 'Use of requires is not supported yet', and no errors.

*   Update the gRPC generate command to handle validation results:
    *   If only warnings are present, complete generation and create the output files: `mapping.json`, `service.proto`, and `service.proto.lock.json`.
    *   If any errors are present, stop generation and throw an error with the message 'Schema validation failed'. Do not create any output files.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.