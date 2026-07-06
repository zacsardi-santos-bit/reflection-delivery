Implement a decoupling of the Trino client library from Java's database driver infrastructure by modifying the connection URI parsing and validation process. Replace checked exceptions with unchecked runtime exceptions, and ensure all validation errors are reported together. Introduce a builder API for constructing connection URIs with type-safe property setters.

Requirements:

*   Modify `ConnectionProperties` class:
    *   Implement a static method `allProperties()` that returns a set of all public static field values declared in the class.

*   Update `ConnectionProperty` interface:
    *   Add `encodeValue(T value)` and `decodeValue(V value)` methods for bidirectional serialization.
    *   Ensure round-trip serialization returns the original typed value.

*   Enhance `TrinoUri` class:
    *   Implement a static `builder()` method returning a `Builder` instance.
    *   Ensure `create(String url, Properties properties)` throws `RuntimeException` for invalid properties, preserving existing error messages.
    *   Aggregate multiple validation errors into a single `RuntimeException` with a message starting with 'Provided connection properties are invalid:' followed by each error on a new line.
    *   Provide an error message when a property appears in both the URL and the properties argument: 'Connection property <name> is passed both by URL and properties'.
    *   Ensure SSL is implicitly enabled on port 443, allowing SSL-related properties unless explicitly disabled.

*   Develop `TrinoUri.Builder` class:
    *   Include `setUri(URI uri)`, a generic `setProperty(ConnectionProperty<V, T>, T)` method, and a `build()` method.
    *   Provide a named setter method for each property in `ConnectionProperties.allProperties()`, excluding `setSslVerificationNone`, `setRestrictedProperties`, and `setUri`.

*   Implement `TrinoDriverUri` class:
    *   Provide a static `createDriverUri(String url, Properties properties)` method that throws `SQLException` for invalid input.
    *   Implement `getTimeout()` to return an `io.airlift.units.Duration` value when the 'timeout' property is specified.

*   Modify `QueryRunner` class:
    *   Constructor must accept `TrinoUri`, `ClientSession`, and a boolean debug flag, removing the `HttpLoggingInterceptor.Level` parameter.

*   Annotate `ClientOptions` class:
    *   Use `@PropertyMapping` annotation for all public fields except CLI-specific ones: url, server, file, debug, historyFile, progress, execute, outputFormat, outputFormatInteractive, pager, ignoreErrors, editingMode, disableAutoSuggestion.

*   Define `LoggingLevel` enum in `io.trino.client.uri` package:
    *   Include values: NONE, BASIC, HEADERS, BODY.

*   Ensure `DriverPropertyInfo` for SSL includes the current value and `SSLVerification` includes value='FULL' when applicable.

*   Use the error message 'TLS/SSL is required for authentication with username and password' for authentication requiring a secure connection.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.