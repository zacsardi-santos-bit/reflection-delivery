Implement a garbage collection mechanism for the `ListOfABAFreeId` class in the brpc 1.3.0 library to manage memory usage by removing stale entries. Ensure the garbage collection is triggered based on a configurable threshold and update the necessary traits and methods to support this functionality.

*   Update the `ListOfABAFreeId` class:
    *   Add support for a new `IdTraits` constant `INIT_GC_SIZE` to specify the initial threshold for garbage collection.
    *   Maintain a private member `_next_gc_size` (uint32_t) initialized to `IdTraits::INIT_GC_SIZE`.
    *   Modify the `add()` method to trigger garbage collection when `_nblock * IdTraits::BLOCK_SIZE` exceeds `_next_gc_size`.
        *   Ensure the MAX_ENTRIES capacity check occurs before the GC check.
    *   Implement the `gc()` method to compact the list by:
        *   Iterating over entries and adding active IDs (as determined by `IdTraits::exists()`) to a new compacted block chain.
        *   Discarding inactive IDs.
    *   After a GC pass, double `_next_gc_size` if fewer than 75% of the threshold entries were freed, with a cap at `IdTraits::MAX_ENTRIES - IdTraits::BLOCK_SIZE * 2`.
    *   Ensure `add()` returns the result of `gc()` after a GC pass.

*   Update IdTraits structs:
    *   In `src/bthread/bthread.cpp`, add `static const size_t INIT_GC_SIZE = 65536;` to `TidTraits`.
    *   In `src/bthread/id.cpp`, add `static const size_t INIT_GC_SIZE = 4096;` to `IdTraits`.

*   Deliver the implementation as a patch file:
    *   Create a patch file named `brpc-1.3.0-2479.patch` in `thirdparty/patches/` directory.
    *   Ensure the patch modifies:
        *   `src/bthread/list_of_abafree_id.h` for GC logic and support.
        *   `src/bthread/bthread.cpp` and `src/bthread/id.cpp` for `INIT_GC_SIZE` updates.

*   Ensure the `add()` method logic:
    *   Checks if `_nblock * BLOCK_SIZE > MAX_ENTRIES` first and returns `EAGAIN` if true.
    *   Triggers `gc()` if `_nblock * BLOCK_SIZE > _next_gc_size`.
    *   Evaluates GC efficiency and adjusts `_next_gc_size` accordingly.
    *   Returns the result of `gc()` directly.

*   Test the implementation with `IdTraits` parameters:
    *   `BLOCK_SIZE=16`, `MAX_ENTRIES=100000`, `INIT_GC_SIZE=100`.
    *   Ensure `get_sizes` returns 96 after 1,000,000 add operations with a predicate tracking at most 4 active IDs.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.