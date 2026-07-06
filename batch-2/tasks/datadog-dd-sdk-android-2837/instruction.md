Implement changes to the RUM SDK to attach context metadata as query tags on upload request URLs, rather than embedding them in each event payload. Ensure the request URL always includes a 'ddtags' parameter with specified context attributes and handles retry-related tags appropriately. Remove redundant tags from event payloads and eliminate the utility function responsible for building these tags.

*   Update the `RumRequestFactory` class:
    *   Modify the private `buildTags()` method to accept parameters for service name, version, SDK version, environment, and variant from `DatadogContext`.
    *   Ensure the `ddtags` string includes these parameters in this order: service, version, sdk_version, env.
    *   Append the variant tag if non-empty, immediately after the env tag.
    *   Add retry-related tags (retry_count, last_failure_status) after context tags, separated by commas, when applicable.
    *   Ensure the `buildUrl()` method always includes the `QUERY_PARAM_TAGS` entry.

*   Ensure `RumAttributes` class constants exist with exact values:
    *   `RumAttributes.SERVICE_NAME` with value "service"
    *   `RumAttributes.APPLICATION_VERSION` with value "version"
    *   `RumAttributes.SDK_VERSION` with value "sdk_version"
    *   `RumAttributes.ENV` with value "env"
    *   `RumAttributes.VARIANT` with value "variant"

*   Remove the `ddtags` field from RUM event models:
    *   Delete the `ddtags` field from the primary constructor of `ActionEvent`, `ErrorEvent`, `LongTaskEvent`, `ResourceEvent`, and `ViewEvent`.
    *   Ensure test forgery factories construct these models without requiring `ddtags`.

*   Delete the `RumTagsUtils.kt` file:
    *   Remove the `buildDDTagsString()` function.
    *   Update all callers in `RumActionScope`, `RumResourceScope`, `RumViewScope`, and `DatadogLateCrashReporter` to remove the `ddtags` argument from event construction calls.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.