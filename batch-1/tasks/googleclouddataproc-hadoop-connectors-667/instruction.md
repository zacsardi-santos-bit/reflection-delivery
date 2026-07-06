Implement a dedicated wrapper type for sensitive configuration values in the Hadoop connector configuration system to prevent accidental exposure of secrets in log outputs. Ensure that sensitive values are logged as redacted and that the underlying value can be accessed explicitly when needed.

*   Update the `RedactedString` class in `com.google.cloud.hadoop.util`.
    *   Add a `value()` method that returns the underlying sensitive string value.
    *   Ensure `toString()` returns the literal string `"<redacted>"` for logging purposes.

*   Modify the `getPassword` method in `HadoopConfigurationProperty`.
    *   Change the return type from `String` to `RedactedString`.
    *   Wrap the resolved configuration value (or default value) in a `RedactedString`.
    *   Log a FINE-level message in the format `"<key> = <redacted>"` when called, ensuring the actual secret is never logged.

*   Ensure the `getStringCollection` method logs non-sensitive values correctly.
    *   Log a FINE-level message in the format `"<key> = [val1, val2]"` displaying the actual collection contents.

*   Update all existing call sites using `getPassword` to handle the `RedactedString` return type.
    *   Use the `.value()` method to obtain the underlying string when necessary.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.