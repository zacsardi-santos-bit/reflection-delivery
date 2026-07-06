## Description

The Google Chronicle Backstory integration supports creating and updating reference lists, but currently lacks two important capabilities:

1. **Content type support for reference lists**: When creating or updating a reference list, users cannot specify what type of content the list will hold (such as network address ranges or pattern-based entries). The API supports different content types, but this option is not exposed as a command argument.

2. **No pre-validation command**: Users have no way to validate whether their list entries are valid for a given content type before actually creating or updating a list. If entries don't match the expected format, they only find out after attempting the operation.

Additionally, the existing create and update commands have weak validation for the lines argument — they do not catch cases where the input looks non-empty but actually contains no real entries (for example, a bracket-enclosed empty list or a string of separators).

## Expected Behavior

- The create and update reference list commands should accept an optional content type argument, with valid values being plain text, CIDR (network ranges), or regex patterns.
- Both commands should validate that after splitting and filtering the lines input, at least one real entry remains — inputs that only contain empty values, brackets, or separators should be rejected with a clear error.
- If an invalid content type value is provided, the commands should reject it with a clear error message listing the valid options.
- A new verify command should be added that accepts lines and a content type, sends them to the API for validation, and returns any per-line errors without actually creating or modifying a list.
- The verify command should display a success message when all lines are valid, or a table of line numbers and error descriptions when some lines are invalid.

## Why This Matters

Users working with structured reference lists (network ranges, regex patterns) need confidence that their entries are correctly formatted before committing changes. The new verify command provides a safe way to check content in advance, and the content type argument ensures the API correctly interprets and validates the list's entries.
