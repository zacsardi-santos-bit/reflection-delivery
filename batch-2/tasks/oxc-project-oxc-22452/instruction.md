I'm working on improving the error experience in our shared utilities for the oxc toolchain.

*   The module at apps/shared/src-js/node_version.ts must export a string constant named NODE_TYPESCRIPT_SUPPORT_RANGE representing the Node.js version range that supports native TypeScript module loading.

*   isTypeScriptModuleSpecifier must return true for file paths ending in .ts, .mts, or .cts (e.g. /tmp/oxfmt.config.ts, /tmp/oxfmt.config.mts, /tmp/oxfmt.config.cts) and for file:// URLs ending in .ts (e.g. file:///tmp/oxfmt.config.ts).

*   isTypeScriptModuleSpecifier must return false for paths that do not end in a TypeScript extension, such as paths ending in .js.

*   getUnsupportedTypeScriptModuleLoadHintForError must return null when the error does not have its code property set to 'ERR_UNKNOWN_FILE_EXTENSION', regardless of whether the specifier is a TypeScript file.

*   getUnsupportedTypeScriptModuleLoadHintForError must return null when the specifier is not a TypeScript module specifier (i.e. isTypeScriptModuleSpecifier returns false), regardless of the error type.

*   getUnsupportedTypeScriptModuleLoadHintForError must return a hint string when the error has code 'ERR_UNKNOWN_FILE_EXTENSION' and the specifier is a TypeScript module specifier. The returned string must have the exact format: the original error message, followed by two newlines, followed by 'TypeScript config files require Node.js ' + NODE_TYPESCRIPT_SUPPORT_RANGE + '.', followed by a newline, followed by 'Detected Node.js ' + nodeVersion + '.', followed by a newline, followed by 'Please upgrade Node.js or use a JSON config file instead.'

*   The nodeVersion parameter of getUnsupportedTypeScriptModuleLoadHintForError is optional; when omitted and the error and specifier conditions are not met, the function still returns null.


*   Interface details: Type: Constant
Name: NODE_TYPESCRIPT_SUPPORT_RANGE
Location: apps/shared/src-js/node_version.ts
Description: A string constant representing the Node.js version range required for native TypeScript module loading support. Exported and used within the hint message produced by getUnsupportedTypeScriptModuleLoadHintForError.

Type: Function
Name: isTypeScriptModuleSpecifier
Location: apps/shared/src-js/node_version.ts
Signature: isTypeScriptModuleSpecifier(specifier: string): boolean
Description: Returns true if the given specifier (file path or file:// URL) refers to a TypeScript module — i.e., it ends with .ts, .mts, or .cts. Returns false for non-TypeScript extensions such as .js.

Type: Function
Name: getUnsupportedTypeScriptModuleLoadHintForError
Location: apps/shared/src-js/node_version.ts
Signature: getUnsupportedTypeScriptModuleLoadHintForError(err: Error, specifier: string, nodeVersion?: string): string | null
Description: Given an error, a module specifier, and an optional detected Node.js version string, returns a human-readable hint string if the error was caused by trying to load a TypeScript config file on an unsupported Node.js version; otherwise returns null. The hint is returned only when the error has code "ERR_UNKNOWN_FILE_EXTENSION" and the specifier passes isTypeScriptModuleSpecifier. The returned string has the format: `${err.message}\n\nTypeScript config files require Node.js ${NODE_TYPESCRIPT_SUPPORT_RANGE}.\nDetected Node.js ${nodeVersion}.\nPlease upgrade Node.js or use a JSON config file instead.`


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.