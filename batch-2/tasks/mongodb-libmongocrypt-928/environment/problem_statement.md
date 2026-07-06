## Description

We need to implement the string encoding step for encrypted full-text search indexing. Currently, there is no way to take a plaintext string and produce the structured set of encoded text fragments — substrings, prefixes, and suffixes — that are required to build encrypted text search indexes.

The encoding process must:
- Accept a string along with parameters specifying which index types are needed (substring, suffix, prefix) and the bounds on fragment lengths
- Produce a result containing all requested fragment sets, along with an exact copy of the original string
- Pad the fragment counts to align with AES block size (multiples of 16 codepoints) to prevent leaking the string length
- Deduplicate substrings so that repeated character sequences appear only once, with the remaining count represented as padding
- Correctly compute fragment boundaries for multi-byte Unicode characters (using codepoint positions, not byte positions)
- Reject input strings that are not valid UTF-8

## Expected Behavior

- A suffix set contains all suffixes of the string with lengths in the specified range, padded to the block-aligned total
- A prefix set contains all prefixes of the string with lengths in the specified range, padded to the block-aligned total
- A substring set contains all unique substrings with lengths in the range, deduplicated, padded to the expected total
- The encoded result carries the original string bytes as an exact match entry
- If the lower bound on fragment length exceeds the block-aligned padded length, the corresponding set is omitted (null)
- If the string is longer than the specified maximum indexable length (substring mode only), encoding fails with an appropriate error
- Encoding a string that is not valid UTF-8 fails with an error

## Why This Matters

This encoding step is the foundation for encrypted text search: without it, drivers cannot prepare the encrypted tokens needed to index string fields for substring, prefix, or suffix queries on encrypted collections.
