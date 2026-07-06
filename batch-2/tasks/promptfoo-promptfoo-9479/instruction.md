I'm trying to configure external function callbacks in my provider setup on Windows, but the file paths aren't being parsed correctly.

*   parseFileUrl must treat a colon as a function name separator only when the portion of the path before the last colon ends with a JavaScript file extension or a .py extension. If the path segment before the last colon is not a JavaScript or Python file, the entire string (including the colon) must be returned as the filePath with no functionName.

*   parseFileUrl must support Python (.py) files as valid containers for named function exports, so 'file://./path/to/file.py:function_name' returns { filePath: './path/to/file.py', functionName: 'function_name' }.

*   parseFileUrl must handle Windows drive-letter paths of the form 'file://C:/path/to/file.js:handler' by correctly identifying the last colon (before 'handler') as the function separator, not the colon after the drive letter, returning { filePath: 'C:/path/to/file.js', functionName: 'handler' }.

*   parseFileUrl must handle canonical Windows file URLs of the form 'file:///C:/path/to/file.js' on Windows (win32 platform) by stripping the leading slash before the drive letter, returning { filePath: 'C:/path/to/file.js' }. The same URL on POSIX/Linux must preserve the full path as '/C:/path/to/file.js' without stripping the leading slash.

*   parseFileUrl must correctly parse named exports from file paths containing colons in the file name itself (e.g., 'file://./path/to/file:default.js:functionName' returns { filePath: './path/to/file:default.js', functionName: 'functionName' }), and from paths with colons in directory names (e.g., 'file://path:with:colons/hooks.js:functionName' returns { filePath: 'path:with:colons/hooks.js', functionName: 'functionName' }).

*   When a file callback reference uses a Windows-style drive-letter path (e.g., 'file://C:/path/to/functions.js:handlerName'), the function loading logic must resolve the module path using only the file path portion (without the drive-letter colon being treated as a separator) and invoke the named export from that module.

*   When a file callback reference uses the canonical Windows triple-slash URL form (e.g., 'file:///C:/path/to/functions.js') on Windows, the function loading logic must correctly resolve the file by stripping the leading slash from the drive-letter path and load the default export.

*   When a file reference path contains a colon that is not a function name separator (e.g., 'file://path/to/functions:default.js'), the entire path including the colon must be used to locate the module, and the default export must be invoked.


*   Interface details: Type: Function
Name: parseFileUrl
Location: src/util/functions/loadFunction.ts
Signature: parseFileUrl(fileUrl: string): { filePath: string; functionName?: string }
Description: Parses a file:// URL into its file path and optional function name components. The function must correctly handle Windows drive-letter paths (e.g., file://C:/path/to/file.js:handler and file:///C:/path/to/file.js), colons embedded in directory or file names, and Python (.py) files as valid sources for named function exports. A colon is treated as a function name separator only when the path segment before the last colon resolves to a JavaScript or Python file; otherwise the colon is preserved as part of the file path. On Windows (win32 platform), if the path starts with a slash followed by a drive letter (e.g., /C:/...), that leading slash is stripped. On POSIX platforms the path is returned as-is, preserving any leading slash.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.