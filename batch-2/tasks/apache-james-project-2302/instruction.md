Update the project's dependency configuration to ensure compatibility with the latest library versions. Adjust the JSON comparison method calls to use the new API style and ensure that error handling for date-time parsing is precise.

*   Ensure that when deserializing JSON message metadata with an invalid date-time value, the system throws a `java.time.DateTimeException` instead of a `java.util.NoSuchElementException`.
*   Upgrade the JSON assertion library dependency:
    *   Change the version of the library identified by `groupId net.javacrumbs.json-unit` from `2.38.0` to `3.2.7`.
    *   Update the `javacrumbs.json-unit.version` property in the root `pom.xml` file to reflect this version change.
    *   Ensure that JSON comparison calls use the varargs-style option arguments.
*   Update the Jackson library versions:
    *   Change the `jackson.version` and `jackson.databind.version` properties in the root `pom.xml` from `2.15.2` to `2.17.1`.
    *   Ensure that datetime parsing failures are correctly propagated as `java.time.DateTimeException`.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.