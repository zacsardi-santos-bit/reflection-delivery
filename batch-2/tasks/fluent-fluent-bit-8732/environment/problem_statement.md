## Description

The CFL (Common Fluent Library) currently always makes deep copies of string and byte data when storing values in variants and key-value lists. This is wasteful in cases where the caller already owns the buffer and knows it will outlive the variant — there is no way to tell the library "use this existing buffer directly, don't copy it." Every string or byte insertion forces an allocation even when the data is already managed elsewhere.

Additionally, there is no function to append a length-delimited string to a CFL array, and the function for appending raw bytes to an array does not support the referenced/copy choice that other typed-value functions should support.

Finally, the array data structure lacks a comprehensive test suite covering all supported element types and the dynamic resizing behavior.

## Expected Behavior

- String and byte creation/insertion functions should accept an additional parameter that controls whether the data is copied or referenced from the caller's buffer.
- A new function should be available for appending a length-delimited string (with explicit size and reference flag) to an array.
- The raw-bytes array append function should accept the same reference flag.
- Array operations should be fully covered, including creation with zero or non-zero capacity, toggling dynamic resize on and off, appending all supported value types, fetching by index, and removing elements by index or by reference.

## Why This Matters

Avoiding unnecessary copies can significantly reduce memory pressure in high-throughput log and trace processing pipelines. Callers that hold long-lived buffers (such as parsed message pack data) should be able to share that memory with CFL variants without duplication. The expanded array test coverage also ensures correctness of the resizable-array mechanism and all element type paths.
