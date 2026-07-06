I'm working on Bazel's test infrastructure and running into a problem with how the OS of the test execution platform is determined when using execution groups.

*   When a test rule defines a 'test' execution group with OS platform constraints, the execution OS stored in the test's execution settings must reflect the OS of the platform assigned to that test execution group, not the target's top-level exec_compatible_with constraints or the host platform. For example, if the test execution group requires macOS, getExecutionOs() must return OS.DARWIN even when the target specifies Windows and the configured platforms are Windows.

*   When a test rule provides testing.ExecutionInfo with an exec_group pointing to an alternative execution group, the execution OS in the test's execution settings must reflect the OS of that alternative execution group's assigned platform. For example, if the alternative exec group requires Linux, getExecutionOs() must return OS.LINUX.

*   ConstraintConstants must provide a public static field named OS_TO_DEFAULT_CONSTRAINT_VALUE (replacing OS_TO_CONSTRAINTS) that maps OS enum values to their corresponding ConstraintValueInfo objects. This field is annotated @VisibleForTesting.

*   ConstraintConstants must provide a static method named getOsFromConstraintsOrHost that accepts a PlatformInfo parameter (replacing the old getOsFromConstraints method that took a ConstraintCollection). The method must return OS.getCurrent() when the platform has no OS constraint, and OS.UNKNOWN for constraint values not recognized by Bazel. It must also recognize @platforms//os:macos as mapping to OS.DARWIN (in addition to the existing @platforms//os:osx mapping).

*   BazelRuleClassProvider must expose a public static field named SHELL_EXECUTABLES (renamed from SHELL_EXECUTABLE) that maps OS enum values to shell executable path fragments. This field must include entries for OS.LINUX and OS.DARWIN (both mapping to /bin/bash) in addition to the existing Windows, FreeBSD, OpenBSD, and UNKNOWN entries.

*   TestTargetExecutionSettings constructor must accept a PlatformInfo executionPlatform parameter as its final argument (after the int runs parameter), replacing internal use of ruleContext.getExecutionPlatform() with the provided platform for OS determination.

*   RuleContext must provide a method named isDefaultExecGroupExecutingOnWindows() (replacing isExecutedOnWindows()) that returns true if the execution platform of the default exec group is Windows.

*   RuleContext must provide a method named createOutputArtifactScriptForAnalysisTest() (replacing createOutputArtifactScript()) that creates the output script artifact with a file extension based on the default exec group's execution platform.


*   Interface details: Type: Class
Name: ConstraintConstants
Location: src/main/java/com/google/devtools/build/lib/analysis/constraints/ConstraintConstants.java
Description: Constants and utility methods for the constraints system. Must expose OS_TO_DEFAULT_CONSTRAINT_VALUE and getOsFromConstraintsOrHost.
Signature: public static final ImmutableMap<OS, ConstraintValueInfo> OS_TO_DEFAULT_CONSTRAINT_VALUE (annotated @VisibleForTesting)
Signature: public static OS getOsFromConstraintsOrHost(PlatformInfo platformInfo)

Type: Function
Name: getOsFromConstraintsOrHost
Location: src/main/java/com/google/devtools/build/lib/analysis/constraints/ConstraintConstants.java
Signature: getOsFromConstraintsOrHost(PlatformInfo platformInfo) -> OS
Description: Returns the OS corresponding to the given platform's constraint collection. Falls back to OS.getCurrent() if the platform has no OS constraint. Returns OS.UNKNOWN for unrecognized constraint values. Maps both @platforms//os:osx and @platforms//os:macos to OS.DARWIN. Replaces the old getOsFromConstraints(ConstraintCollection) method.

Type: Class
Name: TestTargetExecutionSettings
Location: src/main/java/com/google/devtools/build/lib/analysis/test/TestTargetExecutionSettings.java
Description: Container for common test execution settings shared by all TestRunnerAction instances for a given test target. Constructor must now accept PlatformInfo as the final parameter.
Signature: TestTargetExecutionSettings(RuleContext ruleContext, RunfilesSupport runfilesSupport, Artifact executable, @Nullable Artifact instrumentedFileManifest, int shards, int runs, PlatformInfo executionPlatform)

Type: Function
Name: isDefaultExecGroupExecutingOnWindows
Location: src/main/java/com/google/devtools/build/lib/analysis/RuleContext.java
Signature: isDefaultExecGroupExecutingOnWindows() -> boolean
Description: Returns true if the execution platform of the default exec group is Windows. Replaces the old isExecutedOnWindows() method.

Type: Function
Name: createOutputArtifactScriptForAnalysisTest
Location: src/main/java/com/google/devtools/build/lib/analysis/RuleContext.java
Signature: createOutputArtifactScriptForAnalysisTest() -> Artifact
Description: Returns an artifact whose path is based on the target name with a script suffix appropriate for the execution platform assigned to the default exec group (.cmd for Windows, .sh otherwise). Replaces the old createOutputArtifactScript() method.

Type: Class
Name: BazelRuleClassProvider
Location: src/main/java/com/google/devtools/build/lib/bazel/rules/BazelRuleClassProvider.java
Description: Must expose SHELL_EXECUTABLES (renamed from SHELL_EXECUTABLE) as a public static field mapping OS enum values to shell executable PathFragments. Must include entries for OS.LINUX (/bin/bash), OS.DARWIN (/bin/bash), OS.WINDOWS (c:/msys64/usr/bin/bash.exe), OS.FREEBSD (/usr/local/bin/bash), OS.OPENBSD (/usr/local/bin/bash), and OS.UNKNOWN (/bin/bash).
Signature: public static final ImmutableMap<OS, PathFragment> SHELL_EXECUTABLES (annotated @VisibleForTesting)


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.