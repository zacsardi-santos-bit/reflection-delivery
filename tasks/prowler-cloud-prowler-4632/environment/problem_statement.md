## Description

AWS CodeBuild projects allow defining environment variables with their values stored either as plaintext or via a secure parameter store. When developers accidentally place sensitive credentials — such as cloud access keys — directly as plaintext environment variables, those secrets are exposed in the project configuration and risk unauthorized access or leakage.

Prowler currently has no automated check to detect this dangerous misconfiguration. We need a new security check that scans CodeBuild project environment variables and flags any project that stores sensitive credentials as plaintext values.

## Expected Behavior

- For each CodeBuild project, the check should inspect all plaintext environment variables and detect sensitive credentials using automated secret scanning.
- Projects with no environment variables, or where no plaintext variables contain sensitive credentials, should pass.
- Projects where at least one plaintext environment variable contains a sensitive credential should fail.
- Variables whose types indicate they are stored securely (e.g., via a parameter store) should never trigger a failure.
- The check should be configurable: users should be able to specify a list of environment variable names to exclude from secret scanning, so known non-sensitive variables (or acceptable false positives) can be suppressed.
- The failure message should clearly identify the type of secret found and the name of the environment variable it was detected in, listing multiple findings if applicable.

## Why This Matters

Secrets stored in plaintext CodeBuild environment variables are visible to anyone with read access to the project configuration, increasing the attack surface. Automated detection allows security teams to quickly identify and remediate these risky configurations at scale.
