## Description

There is a bug in how the virtio used ring index is updated that allows a block device backed by a sufficiently slow drive to starve all other virtual devices. Currently, every time a descriptor is added to the used ring, the ring's visible index is immediately updated and flushed to the guest. This means that when processing a batch of descriptors, the guest sees each one as soon as it is added, and each update can trigger an interrupt or notification cycle.

The problem is that this per-descriptor ring index flush prevents batching: a slow block device that adds many descriptors one at a time ties up the entire processing loop, preventing other devices from getting CPU time.

## Expected Behavior

- Adding a descriptor to the used ring should NOT immediately advance the ring's visible index. The descriptor should be written to the ring, but the guest should not see it yet.
- There should be a separate, explicit operation to advance the ring's visible index and publish all pending descriptors to the guest at once.
- Callers that process multiple descriptors in a loop should add all of them first, then call the publish operation once at the end.

## Why This Matters

Without this change, a block device backed by a slow backing store can monopolize the VMM's processing loop, effectively starving all other virtio devices (network, entropy, vsock, etc.) of service. The fix enables batched notifications and restores fair scheduling among devices.
