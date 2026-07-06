Implement a mechanism in the `QuarkusTestProfileAwareClassOrderer` to group test classes by their classloader when multiple classloaders are detected. Ensure secondary ordering within each classloader group is maintained. Create new test classes in a specified package to validate this behavior.

*   Update `QuarkusTestProfileAwareClassOrderer.orderClasses(ClassOrdererContext)`:
    *   Detect when test class descriptors originate from more than one distinct classloader by comparing classloaders from each descriptor's test class.
    *   When multiple distinct classloaders are detected:
        *   Apply the configured secondary orderer, defaulting to alphabetical-by-class-name ordering.
        *   Perform a stable sort of all descriptors alphabetically by their classloader's name to group tests from the same classloader together.
    *   When all test class descriptors share a single classloader, use the existing profile-based ordering logic without changes.

*   Create four new empty public classes in the `io.quarkus.test.junit.util.dummyclasses` package:
    *   `Test07` in `test-framework/junit5/src/test/java/io/quarkus/test/junit/util/dummyclasses/Test07.java`
    *   `Test08` in `test-framework/junit5/src/test/java/io/quarkus/test/junit/util/dummyclasses/Test08.java`
    *   `Test09` in `test-framework/junit5/src/test/java/io/quarkus/test/junit/util/dummyclasses/Test09.java`
    *   `Test10` in `test-framework/junit5/src/test/java/io/quarkus/test/junit/util/dummyclasses/Test10.java`

*   Remove the inner private static classes `Test07`, `Test08`, `Test09`, and `Test10` from `QuarkusTestProfileAwareClassOrdererTest` and import these classes from the `io.quarkus.test.junit.util.dummyclasses` package instead.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.