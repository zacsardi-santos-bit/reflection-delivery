Implement a utility class `WebClientFilters` in the specified package to provide reusable filter factories for Spring reactive HTTP clients. These filters should modify URLs, count GET requests, and log outgoing requests.

*   Create the `WebClientFilters` class in the package `com.baeldung.webclient.filter` at the path `spring-5-reactive-2/src/main/java/com/baeldung/webclient/filter/WebClientFilters.java`.
*   Implement the following static factory methods in `WebClientFilters`:
    *   `urlModifyingFilter(String version)`: 
        *   Return an `ExchangeFilterFunction` that appends `"/" + version` to the outgoing request URL.
        *   Ensure the URL transformation is applied to all requests.
    *   `countingFilter(AtomicInteger counter)`:
        *   Return an `ExchangeFilterFunction` that increments the provided `AtomicInteger` by 1 for each GET request.
        *   Ensure the counter remains unchanged for non-GET requests (e.g., POST).
    *   `loggingFilter(PrintStream printStream)`:
        *   Return an `ExchangeFilterFunction` that logs the outgoing request details.
        *   Format the log message as `'Sending request {METHOD} {URL}'` without a trailing newline.
        *   Use the provided `PrintStream` for output.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.