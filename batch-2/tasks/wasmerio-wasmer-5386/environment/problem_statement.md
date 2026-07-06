## Description

The journal system records pipe creation events, but the data structure for these events uses generic, non-descriptive field names for the two file descriptors involved. Currently, the two fields do not indicate which one represents the read end and which represents the write end of the pipe. This makes the API less self-documenting and can lead to confusion or mistakes when working with pipe creation journal entries.

## Expected Behavior

- The pipe creation journal entry should have clearly named fields that distinguish between the read end and write end file descriptors.
- Any code that creates, serializes, or deserializes pipe creation entries should use these descriptive names consistently.
- The serialization and deserialization roundtrip must work correctly with the renamed fields so that persisted journal data can be accurately reconstructed.

## Why This Matters

When developers read or write journal entries for pipe creation events, the field names should make the intent immediately clear. Using opaque, positional names (like "first" and "second") rather than role-describing names forces readers to consult additional documentation or source code to understand which end of the pipe each field corresponds to. Clear naming reduces bugs and improves maintainability.
