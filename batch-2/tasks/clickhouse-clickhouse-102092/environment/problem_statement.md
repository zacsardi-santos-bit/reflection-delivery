## Description

When using the string tokenizer that splits on custom separators, passing an empty string as one of those separators is currently silently accepted. This should be invalid — splitting by an empty string is meaningless and can lead to confusing or undefined behavior. The tokenizer should reject any separator list that contains an empty string, just as it already rejects an empty separator list.

## Expected Behavior

- Providing an array with a single empty string as the separator argument should fail with a bad-argument error.
- Providing an array that mixes valid (non-empty) separators with an empty string should also fail with a bad-argument error.

## Current Behavior

Currently, only an entirely empty separator list is rejected. A separator list that contains at least one element passes validation even if one or more of those elements is an empty string.

## Why This Matters

Users who accidentally pass an empty string in their separator list receive no feedback that their query is invalid. Adding validation for this case makes the function behavior consistent and predictable, and surfaces configuration mistakes early with a clear error.
