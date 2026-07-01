Implement a fix to decouple the addition of descriptors to the virtio used ring from the publication of the ring index to the guest. Modify the existing method and introduce a new method to enable batched notifications, preventing slow block devices from monopolizing processing time.

*   Update the `Queue::add_used` method in `src/vmm/src/devices/virtio/queue.rs`:
    *   Ensure it writes a descriptor entry to the used ring internals.
    *   Do not advance the used ring's visible index (`used.idx`) immediately after adding a descriptor.
    *   Maintain the guest-visible ring index unchanged after `add_used` returns.
    *   Ensure that after calling `Queue::add_used` on a new queue, the externally observable used ring index equals 0.

*   Introduce a new method `Queue::advance_used_ring_idx` in `src/vmm/src/devices/virtio/queue.rs`:
    *   Implement it to publish all pending used-ring entries by setting the ring's visible index (`used.idx`) to the current internal `next_used` counter value.
    *   Include a release memory fence before updating the visible index.
    *   Verify that after calling `Queue::add_used` followed by `Queue::advance_used_ring_idx`, the externally observable used ring index equals 1.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.