I'm running into a bug with in-place assignment on slices of realized tensors.

*   When assigning values to a slice/view of a realized tensor (e.g., a contiguous sub-range), the assignment must update only the slice's region in the parent tensor's buffer, leaving all elements outside the slice unchanged.

*   A simple non-overlapping slice assignment — where the source computation reads from the same slice region being written — must complete in exactly 1 kernel execution.

*   When the source data for an assignment overlaps with but is not identical to the destination slice (e.g., two adjacent but overlapping slices of the same parent tensor), the assignment must use exactly 2 kernels to avoid data corruption and must produce the mathematically correct result.

*   When assigning to a reversed/flipped view of a realized tensor using values that also read from the same parent buffer, the assignment must use exactly 2 kernels and must produce the correct result (each element of the parent tensor updated to reflect the sum of its original value with its mirrored counterpart).


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.