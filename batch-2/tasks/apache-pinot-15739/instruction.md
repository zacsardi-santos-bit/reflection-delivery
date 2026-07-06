Implement a Maven Enforcer custom rule to enforce dependency version management policies in an Apache Pinot project. Ensure that dependency versions are centralized in the root build configuration and not specified in submodule build files.

*   Implement the `PinotCustomDependencyVersionRule` class in `pinot-dependency-verifier/src/main/java/org/apache/pinot/verifier/PinotCustomDependencyVersionRule.java`.
    *   Implement the `org.apache.maven.enforcer.rule.api.EnforcerRule` interface.
    *   Provide a no-argument constructor and a constructor accepting a comma-separated list of module names to skip.
*   Implement the `execute(EnforcerRuleHelper helper)` method to enforce version management rules:
    *   For the root POM:
        *   Inspect the `dependencyManagement` section.
        *   Throw an `EnforcerRuleException` with the message containing 'Please refer to' if any dependency has a hardcoded version not starting with '${'.
        *   Complete without exception if all versions use property placeholders.
    *   For non-root POMs not in the skip list:
        *   Inspect the direct dependencies section.
        *   Throw an `EnforcerRuleException` with the message containing 'Please refer to' if any dependency declares a version, whether literal or property-based.
        *   Complete without exception if no versions are declared.
    *   For non-root POMs in the skip list:
        *   Complete without exception regardless of declared versions.
*   Implement the skip list logic:
    *   Use the Maven session's top-level project base directory and the current project's base directory to compute a relative path.
    *   Compare the first path component of the relative path against each entry in the skip list.
*   Implement the following methods:
    *   `String getCacheId()`
    *   `boolean isCacheable()`
    *   `boolean isResultValid(EnforcerRule cachedRule)`

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.