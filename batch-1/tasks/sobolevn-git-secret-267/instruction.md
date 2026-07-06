Implement validation for the "add user" and "remove user" commands in git-secret to require full, exact email addresses. Ensure that partial or incomplete identifiers are rejected, preventing any unintended modifications to the keyring.

*   Update 'git secret killperson' to:
    *   Exit with a non-zero status code (1) if called with only the local part of an email address (e.g., a short name without the @domain).
    *   Leave the keyring state unchanged if the command fails due to an incomplete email address.
    *   Ensure 'git secret whoknows' succeeds (exit code 0) after a failed call, confirming the user was not removed.
    *   Maintain the user's full email address in the output of 'git secret whoknows' after a failed call.

*   Update 'git secret tell' to:
    *   Exit with a non-zero status code (1) if called with a string that is only a substring of email addresses in the GPG keyring.
    *   Ensure 'git secret whoknows' exits with a non-zero status code (1) after a failed call, confirming no user was added.

*   Ensure 'git secret killperson' continues to:
    *   Succeed (exit code 0) when called with one or more exact, full email addresses that exist in the keyring.
    *   Remove the specified users from the keyring when valid email addresses are provided.

*   Implement validation to reject partial names and substrings before any keyring modification occurs, ensuring the keyring is never partially modified when an invalid email is provided.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.