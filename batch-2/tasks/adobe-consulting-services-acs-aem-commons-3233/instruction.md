Update the `DialogProviderAnnotationProcessor` class to address build warnings and ensure consistent file generation across platforms. Implement the following requirements to improve the annotation processor's functionality:

*   Modify `DialogProviderAnnotationProcessor` class:
    *   Implement `getSupportedAnnotationTypes()` to return a set containing the canonical name of the `DialogProvider` annotation class: `"com.adobe.acs.commons.mcp.form.DialogProvider"`.
    *   Implement `getSupportedSourceVersion()` to return `SourceVersion.latestSupported()` to support the latest Java version available during compilation.
    *   Implement `process(Set<? extends TypeElement> annotations, RoundEnvironment roundEnv)` to handle classes annotated with `@DialogProvider`.

*   Ensure correct file generation:
    *   If a class annotated with `@DialogProvider` does not expose a resource type through a `getResourceType()` method, a `resourceType` field, or a `@Model(resourceType=...)` annotation, generate no additional source files.
    *   If a class exposes a resource type through any of these mechanisms, generate a registration class in the sub-package `{originalPackage}.impl` with the class name `{OriginalClassName}_dialogResourceProvider`.

*   Ensure generated source files:
    *   Use `System.lineSeparator()` for line breaks instead of hardcoded values like `'\n'` or `'\r\n'`.
    *   Follow the exact content structure for the generated file, including package declaration, imports, annotations, class definition, and method implementations as specified.

*   Detect resource type:
    *   Check for a `resourceType` attribute in a `@org.apache.sling.models.annotations.Model` annotation on the class, in addition to the `getResourceType()` method or `resourceType` field.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.