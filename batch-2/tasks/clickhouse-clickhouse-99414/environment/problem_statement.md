## Description

The built-in function for reversing UTF-8 encoded strings crashes when the input contains truncated or incomplete multi-byte character sequences. This was discovered by a fuzzer — passing certain byte sequences that look like the start of a multi-byte UTF-8 character but are cut short (e.g., only the leading byte is present without the expected continuation bytes) causes the server to crash instead of handling the malformed data gracefully.

## Expected Behavior

- When given a string with incomplete multi-byte UTF-8 sequences, the function should complete without crashing. It should treat the orphaned leading byte(s) as individual single bytes.
- Valid UTF-8 strings should continue to be reversed correctly, with each Unicode character (including multi-byte ones) treated as a unit.

## Reproduction

The fuzzer produced a query involving the UTF-8 reversal function applied to data constructed from raw hex bytes representing truncated multi-byte sequences. Various truncation patterns trigger the crash:
- A 2-byte sequence with only the leading byte present
- A 3-byte sequence with only 1 or 2 bytes present
- A 4-byte sequence with only 1, 2, or 3 bytes present

## Why This Matters

Database servers should never crash on unexpected or malformed input data. Graceful handling of invalid UTF-8 is important for reliability, especially since byte-level operations and external data sources can produce malformed strings.
