## Description

The error messages raised by our data-handling components when users provide invalid configuration are inconsistently formatted, making them difficult to read and act on.

Two specific issues have been identified:

1. When too many fields are configured (more than the allowed maximum of 15), the error message contains unnecessary and potentially misleading extra text beyond the core message. The message should clearly state the limit was exceeded.

2. When a text key is specified that doesn't exist in the provided data object, the error message formats the key name without quotation marks and lists the available data keys without spaces after commas. This makes the message hard to parse at a glance, especially when keys have similar names.

## Expected Behavior

- The "fields exceeded" error message should contain a clear statement that the number of fields cannot exceed 15, and nothing misleading beyond that.
- The "invalid text key" error message should display the specified key name surrounded by single quotes and list the available data keys separated by a comma and a space (e.g., "key1, key2" rather than "key1,key2").

## Why This Matters

Clear, well-formatted error messages help developers quickly understand what went wrong when configuring components. These formatting improvements make the error output easier to read and act upon without requiring developers to parse dense or confusing strings.
