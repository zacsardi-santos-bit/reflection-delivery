## Description

The annotation processor that automatically generates dialog resource provider registration classes for AEM components is causing build warnings on Java 11 and higher. These warnings appear because the processor currently declares support only for Java 8, even though developers are now building with newer Java versions. Additionally, the generated registration classes contain hardcoded Unix-style line endings, which can produce inconsistent results across different operating systems.

## Expected Behavior

- The annotation processor should declare support for the latest Java version available during compilation, rather than being limited to Java 8. This removes the warnings seen on Java 11 and higher.
- The code generator should use the platform-native line separator when writing generated source files, ensuring consistent results on Windows, macOS, and Linux.
- When a class annotated to be a dialog provider does not expose a resource type (either through a method, a field, or another annotation's attribute), the processor should generate no additional source file for that class.
- When a class does expose a resource type via any of these mechanisms (a dedicated method, a field, or from another annotation that carries the resource type), the processor should generate a registration class in the appropriate sub-package.

## Why This Matters

Developers building with Java 11+ currently see unnecessary build warnings because of this outdated Java version declaration. Fixing both the version declaration and the line-separator issue brings the annotation processor in line with current best practices and makes generated code portable across different development environments.
