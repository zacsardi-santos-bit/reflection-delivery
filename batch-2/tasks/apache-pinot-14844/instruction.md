Implement a configurable static analysis layer for Groovy scripts in Apache Pinot to enhance security. Ensure that Groovy scripts embedded in SQL queries are inspected and validated against a defined policy before execution. Update the query validation mechanism to incorporate these checks.

*   Create the `GroovyStaticAnalyzerConfig` class in `pinot-segment-local/src/main/java/org/apache/pinot/segment/local/function/GroovyStaticAnalyzerConfig.java`.
    *   Constructor must accept lists for allowed class receivers, allowed imports, allowed static imports, disallowed method names, and a boolean for method definitions.
    *   Ensure JSON serialization/deserialization compatibility using Jackson.
    *   Provide static methods `getDefaultAllowedReceivers()`, `getDefaultAllowedImports()`, and `createDefault()` for default configurations.
    *   Implement getters for all parameters, ensuring null values are preserved.

*   Update `GroovyFunctionEvaluator` in `pinot-segment-local/src/main/java/org/apache/pinot/segment/local/function/GroovyFunctionEvaluator.java`.
    *   Add `setGroovyStaticAnalyzerConfig(GroovyStaticAnalyzerConfig config)` to set the global analyzer configuration. Ensure thread safety and immediate effect.
    *   Implement `parseGroovyScript(String script)` to validate scripts against the active configuration, throwing exceptions for violations.

*   Modify `BaseSingleStageBrokerRequestHandler` in `pinot-broker/src/main/java/org/apache/pinot/broker/requesthandler/BaseSingleStageBrokerRequestHandler.java`.
    *   Replace `rejectGroovyQuery` with `validateGroovyScript(PinotQuery pinotQuery, boolean disableGroovy)`.
    *   Ensure it throws an exception with "Groovy transform functions are disabled for queries" if Groovy is globally disabled and used in the query.
    *   Perform static analysis on Groovy scripts when not globally disabled, throwing descriptive exceptions for violations.
    *   Validate that Groovy function calls have at least 2 arguments, throwing an exception if not.

*   Ensure that:
    *   Scripts attempting shell command execution or disallowed operations are rejected with specific error messages.
    *   Configuration changes are applied at runtime without service restart.
    *   The JSON configuration survives serialization/deserialization without data loss.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.