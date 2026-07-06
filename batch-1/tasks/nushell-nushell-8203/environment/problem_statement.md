## Description

The command for checking whether binary data begins with a specific byte sequence currently only works when that binary data is already fully loaded into memory as a single value. When binary data comes from an external program or process — arriving as a stream of chunks rather than a complete value — the command fails to handle it, producing an error instead of the expected true/false result.

This is a significant limitation for users who need to check the leading bytes of large binary streams, such as file outputs, network data, or the output of other commands that produce raw binary data. Since collecting the entire stream into memory defeats the purpose of streaming, the command should be able to process the data incrementally.

## Expected Behavior

- When a binary stream from an external command is piped in, the command should check whether the start of that stream matches the given byte pattern, processing chunks one at a time.
- The command should return true if the stream's beginning matches the pattern, and false if it doesn't match or if the stream ends before the pattern is fully covered.
- Both short streams and very long streams (e.g., tens of thousands of repeated byte sequences) should be handled correctly.
- Streams containing mixed content types (binary chunks and string chunks) should also be supported.
- Plain string values (not converted to binary) should continue to be rejected with an error indicating that the input type is not supported.

## Why This Matters

Many real-world binary processing tasks involve data that arrives incrementally from external processes. Making binary prefix checking work on streams makes the command practical for use in pipelines that process large binary data without needing to buffer everything first.
