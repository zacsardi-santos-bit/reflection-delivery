Update the Java code generation template to ensure compatibility with older Android environments by replacing the modern resource cleanup API with a traditional finalization approach. Implement the following changes in the Jinja2 template and ensure the generated Java code aligns with the updated pattern.

*   Modify the Jinja2 template `ChipClusters_java.jinja` located at `scripts/py_matter_idl/matter_idl/generators/java/ChipClusters_java.jinja`:
    *   Remove any import statements for `java.lang.ref.Cleaner`.
    *   Ensure the generated `BaseChipCluster` class does not declare a `Cleaner.Cleanable` field.
    *   Eliminate any `Cleaner`-based registration or cleanup logic from the `BaseChipCluster` constructor.
    *   Add a `protected finalize()` method in the generated `BaseChipCluster` class:
        *   Declare it as `protected void finalize() throws Throwable`.
        *   Annotate it with `@SuppressWarnings("deprecation")`.
        *   Within `finalize()`, first call `super.finalize()`.
        *   Check if `chipClusterPtr` is non-zero; if true, invoke `deleteCluster(chipClusterPtr)` and set `chipClusterPtr` to 0.

*   Ensure the golden output file `ChipClusters.java` at `scripts/py_matter_idl/matter_idl/tests/outputs/several_clusters/java/ChipClusters.java` matches the output from the updated template.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.