## Description

The "add user" and "remove user" commands in git-secret currently accept partial or incomplete identifiers instead of requiring a full, exact email address. For example, passing just a short username (without the domain) or a substring that partially matches real email addresses in the keyring causes the tool to behave unexpectedly — sometimes appearing to succeed when it shouldn't, or silently failing to find the right person.

This is a correctness and safety issue: users should be required to supply the full, exact email address when adding or removing someone from the secret keyring. If the provided input doesn't exactly match any email in the GPG keyring, the command should fail immediately with a non-zero exit code and leave the keyring state completely unchanged.

## Expected Behavior

- Passing a short username (without the email domain) to either the "add user" or "remove user" command should fail immediately with a non-zero exit code.
- Passing a string that is only a substring of existing email addresses (e.g., a prefix that matches multiple emails) should also be rejected with a non-zero exit code.
- After such a rejection, querying who has access to the secrets should confirm that no changes were made to the keyring.
- Commands invoked with a fully qualified, exact email address that exists in the keyring should continue to work as before.

## Why This Matters

Without this validation, it is too easy to accidentally attempt to add or remove the wrong person — or to receive no clear feedback that the operation failed. Requiring an exact email match makes the tool safer and its behavior more predictable and auditable.

See also: https://github.com/sobolevn/git-secret/issues/176
