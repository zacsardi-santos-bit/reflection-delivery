## Description

The copy-paste feature in Sanity Studio has several gaps and bugs that need to be addressed. Users copying field values between documents encounter missing or incorrect behavior in a number of scenarios, and the recent searches storage is tightly coupled in a way that makes it brittle and hard to test reliably.

## Problems

**Copy-Paste issues:**
- When a user copies a reference field and pastes it somewhere, the paste operation does not validate whether the referenced document actually exists. This can silently produce broken data.
- Pasting into array fields is unreliable: objects and references cannot be reliably copied into arrays, and there is no support for appending a single item to an existing array.
- When copying a reference between fields with differing strength requirements (one weak, one strong), the strength property is not adjusted to match the target field.
- Error messages shown to the user when paste operations fail use the wrong category (e.g., array value incompatibility uses a generic schema mismatch error key).

**Recent searches issues:**
- The recent searches functionality couples its storage logic directly with the hook that exposes searches to the UI, making it very difficult to write deterministic tests and causing flaky behavior in the test suite.

## Expected Behavior

- Reference validation happens before any paste is applied, and users receive a clear error message if the referenced document does not exist.
- Objects and references can be pasted into compatible array fields, with proper handling for both full replacement and item appending.
- Reference strength is automatically adjusted to match the target field's schema when pasting.
- Informative, correctly categorized error messages are shown when paste operations fail due to type incompatibility.
- The recent searches storage is abstracted behind a standalone, independently testable module that can be replaced or mocked in tests without affecting the broader hook.

## Why This Matters

Without these fixes, the copy-paste experience is unreliable: silently broken references can be introduced, array fields cannot be populated by pasting, and the test suite for recent searches is flaky. This work makes the feature trustworthy for end users and maintainable for developers.
