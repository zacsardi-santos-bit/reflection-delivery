I'm building a TypeScript language service plugin for Payload CMS that provides IDE assistance for the custom component path strings used throughout Payload configurations.

*   The plugin must be a TypeScript Language Service Plugin: its default export is a factory function that accepts `{ typescript }` and returns an object with a `create(info)` method that wraps the base language service and returns an augmented language service.

*   The augmented `getSemanticDiagnostics` must emit a diagnostic with code 71001 when a component path string (in either string or object form) references a file path that cannot be resolved on disk. The error message must contain the unresolvable path fragment.

*   The augmented `getSemanticDiagnostics` must emit a diagnostic with code 71002 when a component path string references an export name that does not exist in the resolved module. The error message must contain the invalid export name wrapped in single quotes (e.g., 'ExportName').

*   When emitting code 71002 for a missing export and no close spelling suggestion exists but the module does have other exports, the error message must contain the text 'Available exports' followed by the list of available export names.

*   When a component path string has no '#' separator and the plugin defaults to checking for a default export, but the module has no default export, a code 71002 diagnostic must be emitted. The error message must contain the text 'default' wrapped in single quotes (i.e., the literal text "'default'").

*   Valid component paths (file exists, named export exists after '#') must produce no plugin diagnostics with codes 71001 or 71002.

*   A path string without '#' that resolves to a module with a default export is valid and must produce no plugin diagnostics.

*   Object form component references ({ path, exportName }) must be fully supported: when `exportName` is present and names a valid export, neither the path property nor the exportName property should produce a plugin diagnostic; when `exportName` is absent and the module has no default export, a code 71002 diagnostic is emitted; when `exportName` is invalid, a code 71002 diagnostic is emitted.

*   When the `path` property in object form contains a '#' (e.g. '/some/file.tsx#ExportName'), the plugin must parse it correctly and must not emit spurious diagnostics for valid such paths.

*   When a sibling `exportName` overrides the default-export requirement on a `path` property, the path property itself must NOT additionally produce a 'missing default export' diagnostic. In the test fixtures, there are exactly 2 (not 3) diagnostics pointing to the icon module path that lacks a default export (one from the bare string form, one from the object form without exportName).

*   The augmented `getCompletionsAtPosition` must return entries containing the named exports of the referenced module when the cursor position is after the '#' character in a component path string.

*   The augmented `getCompletionsAtPosition` must return file and directory name entries when the cursor is in the path segment portion of a component string (before '#' or with no '#'). A bare '/' prefix must show the top-level components directory.

*   The `exportName` property in object form must provide export completions drawn from the module specified by the sibling `path` property.

*   The augmented `getDefinitionAndBoundSpan` must return a `definitions` array with at least one entry when positioned on a component path string; each entry must have a `fileName` containing the resolved file path and a `name` equal to the referenced export name.

*   All diagnostics, completions, and go-to-definition behaviors must work correctly when component path strings use tsconfig `paths` aliases (e.g., '@/' prefix), resolving the alias to the mapped directory before validating the file and export.


*   Interface details: Type: Function
Name: pluginInit (default export)
Location: packages/typescript-plugin/src/index.ts (compiled to packages/typescript-plugin/dist/index.js)
Signature: pluginInit({ typescript: ts }: { typescript: typeof import('typescript') }) -> { create(info: ts.server.PluginCreateInfo): ts.LanguageService }
Description: The TypeScript Language Service Plugin factory function. Follows the standard TypeScript Language Service Plugin API: called with the TypeScript module, returns a plugin object with a `create` method. The `create` method receives a `PluginCreateInfo` (including the base language service, language service host, and project) and returns an augmented `LanguageService` that enhances `getSemanticDiagnostics`, `getCompletionsAtPosition`, and `getDefinitionAndBoundSpan` for Payload component path strings.

---

Diagnostic Codes (used by the augmented getSemanticDiagnostics):

- Code 71001: Emitted when a component path string references a file that cannot be resolved. The diagnostic message must contain the unresolvable path fragment.
- Code 71002: Emitted when a component path string references an export name that does not exist in the resolved module. The diagnostic message must contain the invalid export name. When the message is about a missing default export, it must contain the text 'default'. When similar exports exist, the message must contain the text 'Available exports'.

---

Component Path String Format (what the plugin must recognize and process):

String form:
  - '/path/to/Component.tsx#ExportName'  — explicit named export
  - '/path/to/Component.tsx'             — uses default export
  - '@/path/to/Component#ExportName'     — with tsconfig path alias

Object form:
  - { path: '/path/to/Component.tsx', exportName: 'ExportName' }
  - { path: '/path/to/Component.tsx#ExportName' }  — hash in path property
  - { path: '/path/to/Dir#ExportName' }             — directory resolved to index file
  - All of the above with '@/' alias paths

These path values appear in fields typed as PayloadComponent (or CustomComponent) in the Payload config type declarations, which include field admin component slots and admin-level component slots.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.