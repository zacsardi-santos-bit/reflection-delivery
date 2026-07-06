## Description

Airflow currently allows DAG run IDs and task/XCom keys to contain consecutive dots. Because these identifiers are used in file paths and URLs, an attacker or misconfigured workflow could craft an identifier that traverses directory boundaries — for example, encoding a directory traversal sequence in a run ID. This is a security vulnerability that should be closed by default.

## Expected Behavior

- Attempting to create a DAG run whose run ID contains consecutive dots should be rejected with a clear error indicating that consecutive dots are not allowed.
- Attempting to use a key (e.g., for task state or XCom) that contains consecutive dots should be rejected with a clear error indicating that consecutive dots are not allowed to prevent path traversal.
- The rejection should cover all forms: plain consecutive dots, run IDs that are only consecutive dots, and run IDs where the consecutive dots appear alongside other characters (including URL-encoded separators).

## Why This Matters

Without this protection, identifiers accepted by Airflow could be used to construct directory traversal sequences, potentially exposing sensitive files or system paths when those identifiers are used in file operations. Blocking consecutive dots in identifiers by default prevents this class of attack without requiring operators to configure anything.
