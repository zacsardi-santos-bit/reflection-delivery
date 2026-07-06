Implement changes to the Pulsar Functions statistics data model and admin tools to correctly handle and report function statistics, especially for functions that have not yet processed any messages. Ensure the admin API and CLI provide consistent and accurate statistics for both aggregate and per-instance queries.

*   Update the `FunctionStats` class in `pulsar-common/src/main/java/org/apache/pulsar/common/policies/data/FunctionStats.java`:
    *   Change `avgProcessLatency` from `double` to `Double` to allow null values.
    *   Change `lastInvocation` from `long` to `Long` to allow null values.
    *   Ensure `calculateOverall()` method handles null values for `avgProcessLatency` and `lastInvocation`.
    *   Implement a static method `decode(String json)` that parses a JSON string into a `FunctionStats` object, throwing `IOException` on failure.

*   Update the `FunctionStats.FunctionInstanceStats.FunctionInstanceStatsDataBase` class:
    *   Change `avgProcessLatency` from `double` to `Double`.

*   Update the `FunctionStats.FunctionInstanceStats.FunctionInstanceStatsData` class:
    *   Change `lastInvocation` from `long` to `Long`.

*   Modify `FunctionRuntimeManager` in `pulsar-functions/worker/src/main/java/org/apache/pulsar/functions/worker/FunctionRuntimeManager.java`:
    *   Implement `getFunctionStats(String tenant, String namespace, String functionName, URI workerServiceUrl)` to return `FunctionStats`, ensuring null values for unprocessed functions.
    *   Implement `getFunctionInstanceStats(String tenant, String namespace, String functionName, int instanceId, URI workerServiceUrl)` to return `FunctionStats.FunctionInstanceStats.FunctionInstanceStatsData`.

*   Update the admin Java client `Functions` interface:
    *   Implement `getFunctionStats(String tenant, String namespace, String functionName)` to return `FunctionStats`.
    *   Implement `getFunctionStats(String tenant, String namespace, String functionName, int instanceId)` to return `FunctionStats.FunctionInstanceStats.FunctionInstanceStatsData`.

*   Enhance the Pulsar admin CLI:
    *   Add a `functions stats` subcommand that accepts `--tenant`, `--namespace`, and `--name` arguments, outputting JSON parseable by `FunctionStats.decode()`.

*   Annotate the REST endpoints in `FunctionsBase` to produce JSON:
    *   Ensure `getFunctionStats` and `getFunctionInstanceStats` endpoints are annotated with `@Produces(MediaType.APPLICATION_JSON)`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.