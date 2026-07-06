Consolidate all integration and end-to-end tests into a single Maven module named "e2e". Update the Maven build configuration to support this new module, ensuring it is independently buildable and testable.

*   Create a new Maven module:
    *   Place the module in the `e2e/` directory at the repository root.
    *   In `e2e/pom.xml`, declare `org.jdbi.internal:jdbi3-parent` as the parent.
    *   Set the artifact ID to `jdbi3-e2e`.

*   Update the root `pom.xml`:
    *   Register the `e2e` module to ensure it is included in the build reactor.
    *   Enable running tests with the command `mvn -pl e2e test`.

*   Configure dependencies in `e2e/pom.xml`:
    *   Add test-scoped dependencies for:
        *   `jdbi3-core`
        *   `jdbi3-sqlobject`
        *   `jdbi3-testing`
        *   H2 in-memory database driver
    *   Include `jdbi3-freemarker` for FreeMarker SQL template tests.
    *   Add `org.projectlombok:lombok` and `jakarta.annotation:jakarta.annotation-api` for annotation processor tests.

*   Set Java version and module name:
    *   Configure the module to compile with Java 17 as the minimum target.
    *   Set the Java module name to `org.jdbi.v3.e2e` using the `moduleName` property.

*   Ensure compatibility:
    *   Include a Maven build profile or activation condition to skip tests if the build JDK is older than Java 17, maintaining compatibility with Java 11 CI builds.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.