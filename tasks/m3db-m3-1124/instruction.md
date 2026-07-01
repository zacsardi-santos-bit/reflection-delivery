Implement a configurable limit on the maximum number of datapoints a query can compute in your time-series query service. Ensure that queries exceeding this limit are rejected with a clear error message. Update the query handler to accept this configuration and provide a sample configuration file demonstrating its usage.

*   Define the `LimitsConfiguration` struct:
    *   Include a field `MaxComputedDatapoints` of type `int64` with YAML tag `maxComputedDatapoints`.
    *   Treat zero or negative values as 'limit disabled'.

*   Update the `Configuration` struct:
    *   Include a field `Limits` of type `LimitsConfiguration` with YAML tag `limits`.

*   Create a sample configuration file:
    *   Ensure `src/cmd/services/m3query/config/testdata/sample_config.yml` is loadable as a `Configuration`.
    *   Set `limits.maxComputedDatapoints` to 12000 in the sample file.

*   Validate all YAML files:
    *   Ensure all YAML files in `src/query/config/` are loadable and pass validation as a `Configuration`.

*   Modify the `NewPromReadHandler` function:
    *   Accept three arguments: an engine, tag options, and a `*LimitsConfiguration`.
    *   Return a `*PromReadHandler`.
    *   Store the `LimitsConfiguration` pointer in the `limitsCfg` field of the handler.

*   Update the `PromReadHandler` struct:
    *   Include an unexported field `limitsCfg` of type `*config.LimitsConfiguration`.

*   Implement the `validateRequest` method:
    *   Signature: `(h *PromReadHandler) validateRequest(params *models.RequestParams) error`.
    *   Compute `numSteps` as `int64((params.End - params.Start) / params.Step)`.
    *   If `h.limitsCfg.MaxComputedDatapoints > 0` and `numSteps > h.limitsCfg.MaxComputedDatapoints`, return an error.
    *   Use the error message format:
        *   "querying from %v to %v with step size %v would result in too many datapoints (end - start / step > %d). Either decrease the query resolution (?step=XX), decrease the time window, or increase the limit (`limits.maxComputedDatapoints`)".

*   Handle errors in `ServeHTTP`:
    *   When `validateRequest` returns an error, respond with HTTP status 400 Bad Request.
    *   Include a JSON body: `{"error": "<error message string>"}`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.