Implement a system that allows Python provider configurations to reference external files, resolving these references before the Python script runs. Ensure support for various file types and dynamic configuration generation using JavaScript or Python functions.

*   Implement the `loadFileReference` function in `src/util/fileReference.ts`:
    *   Accept a `fileRef` string with a 'file://' prefix and an optional `basePath` string.
    *   Strip the 'file://' prefix and split on ':' to extract an optional function name.
    *   Resolve the file path using `path.resolve(basePath, filePath)`.
    *   Load files based on their extensions:
        *   JSON files: Use `fs.promises.readFile(path, 'utf8')` and `JSON.parse`.
        *   YAML files: Use `fs.promises.readFile(path, 'utf8')` and `yaml.load()`.
        *   JavaScript files: Use `importModule(resolvedPath, functionName)`.
        *   Python files: Use `runPython(resolvedPath, functionName || 'get_config', [])`.
        *   Text files: Use `fs.promises.readFile(path, 'utf8')` and return the content.
    *   Throw an error 'Unsupported file extension: <ext>' for unrecognized extensions.

*   Implement the `processConfigFileReferences` function in `src/util/fileReference.ts`:
    *   Recursively process configuration values, resolving 'file://' references using `loadFileReference`.
    *   Return primitive values unchanged.
    *   Log errors using `logger.error` and rethrow them if file loading fails.

*   Extend the `PythonProvider` class in `src/providers/pythonCompletion.ts`:
    *   Add a public `initialize()` method:
        *   Call `processConfigFileReferences(this.config, this.options?.config.basePath || '')`.
        *   Update `this.config` with the resolved result.
        *   Set `this.isInitialized = true` on success; remain falsy on failure.
        *   Ensure idempotency by calling `processConfigFileReferences` only once.
    *   Ensure `callApi`, `callEmbeddingApi`, and `callClassificationApi` methods:
        *   Call `initialize()` before execution.
        *   Pass arguments to `runPython` with a `config` key in the options object.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.