Implement server-side condition evaluation support for the Firebase Remote Config Go Admin SDK. Update the SDK to evaluate conditions against a provided context and return appropriate parameter values based on active conditions.

*   Implement `conditionEvaluator.evaluateConditions()`:
    *   Return a `map[string]bool` indicating the evaluation result for each condition.
    *   Ensure OR conditions evaluate to true if any sub-condition is true; AND conditions evaluate to false if any sub-condition is false.
    *   Evaluate percentage conditions using `computeInstanceMicroPercentile()` and support operators: `LESS_OR_EQUAL`, `GREATER_THAN`, `BETWEEN`.

*   Implement `computeInstanceMicroPercentile(seed, randomizationID string) uint32`:
    *   Hash the string `"<seed>.<randomizationID>"` or `"<randomizationID>"` using SHA-256.
    *   Convert the hash to a big-endian unsigned integer, modulo 100,000,000, and return as `uint32`.

*   Update `Evaluate(context map[string]any) (*ServerConfig, error)` in `ServerTemplate`:
    *   Return an error if no template is cached.
    *   Seed config with in-app defaults from `stringifiedDefaultConfig`.
    *   Evaluate conditions and determine parameter values based on condition matches.
    *   Handle `UseInAppDefault` and `Value` logic for parameter values.

*   Modify `newServerTemplate`:
    *   Store string values in `defaultConfig` verbatim, not JSON-encoded.

*   Update `serverTemplateData` struct:
    *   Add `Conditions []namedCondition` field.
    *   Change `ETag` JSON tag to `"etag"`.

*   Define `namedCondition`, `oneOfCondition`, `orCondition`, `andCondition`, `percentCondition`, `microPercentRange`, `parameter`, and `parameterValue` structs in `server_template_types.go`:
    *   Ensure correct JSON field names and structure.

*   Ensure correct evaluation of percent operators:
    *   `LESS_OR_EQUAL`: true if `instanceMicroPercentile <= MicroPercent`.
    *   `GREATER_THAN`: true if `instanceMicroPercentile > MicroPercent`.
    *   `BETWEEN`: true if `MicroPercentLowerBound < instanceMicroPercentile <= MicroPercentUpperBound`.

*   Ensure `ServerConfig` methods return correct values and sources:
    *   Use `Remote`, `Default`, and `Static` as value sources.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.