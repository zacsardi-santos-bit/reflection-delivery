Convert the API documentation extraction tool from JavaScript to TypeScript. Ensure the tool is compatible with TypeScript module import syntax and retains all existing functionality.

*   Update the file:
    *   Rename the file to `misc/api-doc.ts`.
    *   Convert the existing JavaScript code to TypeScript.
*   Implement the `parseOutApiDocs` function:
    *   Export it as a named export from `misc/api-doc.ts`.
    *   Accept an array of TypeScript source file paths as a parameter.
    *   Return an object keyed by class name, containing extracted documentation.
*   Ensure functionality:
    *   Return an empty object `{}` if no documented elements are found.
    *   For directives or components, include: 
        *   `fileName`, `className`, `description`, `type` ('Directive' or 'Component'), `inputs`, `outputs`, and `methods`.
    *   For services (Injectable), include:
        *   `fileName`, `className`, `description`, `type` ('Injectable'), `methods`, and `properties`.
    *   For interfaces, include:
        *   `className`, `description`, `type` ('Interface'), `properties`, and `methods`.
    *   For other classes, include:
        *   `className`, `description`, `methods`, and `properties`.
*   Extract detailed information:
    *   Inputs: `name`, `type`, `description`, `defaultValue` (undefined if not set).
    *   Outputs: `name`, `description`.
    *   Methods: `name`, `description`, `args` (array of `{name, type}`), `returnType`.
        *   Exclude methods annotated with `@internal`.
    *   Properties: `name`, `type`, `description`, `defaultValue` (undefined if not set).
*   Handle JSDoc tags:
    *   Extract `@deprecated` as a `deprecated` field: `{version: string, description: string}`.
    *   Extract `@since` as a `since` field: `{version: string, description: string}`.
*   Manage generic classes/interfaces:
    *   Populate `typeParameter` with the type parameter string.
    *   Set `typeParameter` to undefined if not present.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.