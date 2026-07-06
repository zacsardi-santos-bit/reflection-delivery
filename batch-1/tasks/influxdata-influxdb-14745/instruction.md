Implement a new HTTP handler method in the CheckHandler struct to return the effective query for a given check in InfluxDB. This handler should generate a Flux query that reflects the check's scheduling configuration and return it as a JSON response.

*   Implement the `handleGetCheckQuery` method in `http/check_service.go` with the signature `handleGetCheckQuery(w http.ResponseWriter, r *http.Request)`.
    *   Extract the "id" path parameter from the request context.
    *   Use `CheckService.FindCheckByID` to retrieve the check by ID.
    *   Generate a ready-to-execute Flux script from the check's configuration.
    *   Write a JSON response containing the script in the "flux" field.
*   Ensure the HTTP response:
    *   Has status 200 OK.
    *   Has Content-Type "application/json; charset=utf-8".
*   Format the generated Flux script as follows:
    *   Begin with "package main\n".
    *   Include the check's source query with:
        *   The range start replaced with the negative of the check's Every duration (e.g., -1h for Every=1h).
        *   The stop parameter removed.
        *   The aggregateWindow every parameter replaced with the check's Every duration.
    *   Append a task option after a blank line: `\noption task = {name: "<check.Name>", every: <check.Every>}`.
*   For example, for a check named 'hello' with Every=1h and source query 'from(bucket: "foo") |> range(start: -1d, stop: now()) |> aggregateWindow(every: 1m, fn: mean) |> yield()', ensure the response flux value is exactly:
    *   'package main\nfrom(bucket: "foo")\n\t|> range(start: -1h)\n\t|> aggregateWindow(every: 1h, fn: mean)\n\t|> yield()\n\noption task = {name: "hello", every: 1h}'.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.