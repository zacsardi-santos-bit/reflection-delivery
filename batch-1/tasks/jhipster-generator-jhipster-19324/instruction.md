Implement a log sanitization component in JHipster-generated Spring Boot applications to prevent log injection attacks. Ensure this component is included in all generated server-side configuration files across different generation scenarios.

*   Create a file named `CRLFLogConverter.java` in the `src/main/java/{packageName}/config/` directory of each generated project.
    *   Ensure this file is actively written by the generator and appears in the output tracking.
*   Ensure the file generation occurs in all server generation scenarios:
    *   Full application generation
    *   Application with a custom client path
    *   Server-only generation
    *   Blueprint-based generation
    *   Scoped blueprint generation
*   Update the generator's file registry in `generators/server/files.js`:
    *   Include an entry mapping the template path `package/config/CRLFLogConverter.java` to the output path `{javaDir}config/CRLFLogConverter.java`.
    *   Use a `renameTo` function for the mapping.
*   Ensure a template file exists at `generators/server/templates/src/main/java/package/config/CRLFLogConverter.java.ejs`:
    *   This file should contain the Java source code for the CRLF log sanitization component.
    *   The template should generate the `CRLFLogConverter` Java class in the `packageName.config` package.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.