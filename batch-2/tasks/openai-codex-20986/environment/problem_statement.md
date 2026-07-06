## Description

The memories backend currently only supports reading a file from the very beginning. There is no way to specify a starting line when reading a memory file. This becomes a problem when working with large memory files where only the latter portion is relevant — for instance, after an agent has already processed earlier sections and wants to continue from a known line.

## Expected Behavior

- Reading a memory file should accept an optional 1-indexed starting line number. When specified, only the content from that line onward is returned.
- The response should include the line number from which the content begins, so callers can confirm where the returned content starts.
- If the starting line number is zero (which is not a valid 1-indexed position), the request should be rejected with an appropriate error.
- If the starting line number exceeds the total number of lines in the file, the request should be rejected with a separate, descriptive error rather than returning empty content silently.
- When no starting line is specified (or the starting line is 1), the behavior is unchanged — the full file content is returned starting from the beginning.

## Why This Matters

Without line offset support, agents must re-read memory files from the top every time, even when only the tail of a file is relevant. Supporting a starting line number makes memory access more efficient and allows incremental reading patterns.
