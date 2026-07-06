Implement support for sending Telegram alert notifications to a specific group topic by adding a topic ID field to the receiver configuration. Ensure backward compatibility with older Alertmanager versions by dropping unsupported fields with a warning. Correct the type mismatch in the labels map for static scrape configuration to resolve compile errors.

*   Update the telegramConfig struct in `pkg/alertmanager/types.go`:
    *   Add a field `MessageThreadID` of type `int` with yaml tag `message_thread_id,omitempty` and json tag `message_thread_id,omitempty`.

*   Modify the `sanitize` method in `pkg/alertmanager/amcfg.go`:
    *   Extend the method signature: `func (tc *telegramConfig) sanitize(amVersion semver.Version, logger *slog.Logger) error`.
    *   Check if `MessageThreadID` is non-zero and Alertmanager version is less than 0.26.0.
        *   Log a warning message if true.
        *   Set `MessageThreadID` to 0 to drop the field.
    *   Preserve `MessageThreadID` as-is if the Alertmanager version is >= 0.26.0.
    *   Ensure no error is returned solely due to the `MessageThreadID` version check.

*   Correct the type of the `Labels` field in `test/e2e/scrapeconfig_test.go`:
    *   Change the type to `map[string]string` from `map[monitoringv1.LabelName]string`.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.