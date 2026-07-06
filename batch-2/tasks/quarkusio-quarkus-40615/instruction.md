Relocate the utility class for managing system properties in a resettable way from the core runtime module to ensure it is not included in production builds. This class should be moved to a module that is not part of the runtime artifact, preventing it from being on the classpath of production applications.

Requirements:

*   Ensure the utility class is not present in the core runtime module's classpath.
    *   Attempting to load the class from the core runtime module should result in a class-not-found error.
*   Relocate the utility class to a module that is not included in the production runtime artifact.
    *   Verify that the class is only accessible in testing or development environments.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.