## Description

The Boa JavaScript engine needs to support resizable array buffers as specified by the ECMAScript standard. Currently, the internal function that allocates memory for array buffers only accepts a fixed byte length — there is no way to indicate that a buffer should support dynamic resizing up to a maximum capacity.

To properly implement resizable array buffers, the allocation function needs an additional optional parameter for the maximum byte length. When this parameter is absent (no maximum specified), the behavior should remain identical to the current implementation: valid sizes succeed and sizes that are too large to allocate fail with an error.

## Expected Behavior

- The array buffer byte data block creation function should accept an optional maximum byte length as a parameter
- When no maximum byte length is provided, existing behavior is preserved: a reasonably-sized allocation succeeds, and an impossibly large allocation (e.g., exceeding memory limits) fails
- This change lays the groundwork for the engine to create and track resizable buffers with a known upper bound on their capacity

## Why This Matters

The ECMAScript specification defines resizable array buffer objects that can grow dynamically up to a maximum size. Without this parameter, the engine cannot properly allocate or track the constraints for such buffers, blocking full implementation of the resizable array buffer proposal.
