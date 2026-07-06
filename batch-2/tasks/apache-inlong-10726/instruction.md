Set up a new end-to-end test infrastructure for the latest major version of the stream processing runtime. Implement a utility class for resolving named placeholders in template strings and ensure the necessary build dependencies and configurations are in place.

*   Add a new Maven profile:
    *   Modify `inlong-sort/sort-end-to-end-tests/pom.xml` to include a profile with id 'v1.18', set as active by default.
    *   Register the 'sort-end-to-end-tests-v1.18' submodule within this profile.

*   Create a new Maven module:
    *   Establish the module at `inlong-sort/sort-end-to-end-tests/sort-end-to-end-tests-v1.18/pom.xml`.
    *   Set `artifactId` to 'sort-end-to-end-tests-v1.18' and parent to 'sort-end-to-end-tests'.
    *   Include dependencies on `sort-dist`, `testcontainers`, `elasticsearch` (version 6.8.17), and `flink` libraries (version 1.18.1).

*   Implement the `PlaceholderResolver` class:
    *   Locate it in `org.apache.inlong.sort.tests.utils` at `inlong-sort/sort-end-to-end-tests/sort-end-to-end-tests-v1.18/src/test/java/org/apache/inlong/sort/tests/utils/PlaceholderResolver.java`.
    *   Provide a static method `getDefaultResolver()` returning a singleton instance.
    *   Implement `resolveByMap(String content, Map<String, Object> valueMap)` to replace `${key}` placeholders in `content` with corresponding map values.
    *   Ensure it returns 'today is 2024.07.15, today weather is song' when called with 'today is ${date}, today weather is ${weather}' and a map containing `date='2024.07.15'` and `weather='song'`.

*   Develop container-based test environment classes:
    *   Implement `FlinkContainerTestEnv`, `FlinkContainerTestEnvJRE8`, and `FlinkContainerTestEnvJRE11` in the same package as `PlaceholderResolver`.
    *   Ensure these classes manage Docker-based Flink clusters for testing.

*   Update format library dependencies:
    *   Modify `inlong-sort/sort-formats/pom.xml` to include `format-common`, `format-row`, and `format-rowdata` modules under the v1.18 profile.

*   Ensure the `TestUtils` class is available:
    *   Implement in `org.apache.inlong.sort.tests.utils` with a method `getResource(String resourceNameRegex)` to locate files by regex.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.