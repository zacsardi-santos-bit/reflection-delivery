## Description

JHipster-generated Spring Boot applications currently lack a built-in defense against log injection attacks. Log injection occurs when user-supplied input containing carriage return or line feed characters is written directly to log files, allowing an attacker to forge log records, corrupt audit trails, or obscure malicious activity. Static code analysis tools flag this as a security concern in generated codebases.

## Expected Behavior

- Every newly generated JHipster server application should automatically include a log sanitization component in its configuration package
- This component should be registered as part of the standard set of generated configuration files, so it is written to disk whenever a server application is generated
- The component should be present regardless of which frontend framework (or no frontend) is used, and should also be generated when using custom blueprints

## Why This Matters

JHipster's value proposition is to give developers a production-ready application out of the box. Security best practices, including protection against log injection, should be included by default so that teams don't have to add this protection manually after the fact. Without this, generated applications may fail security audits or static analysis scans right after generation.
