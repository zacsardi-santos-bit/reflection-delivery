## Description

The ARM64 boot image loading logic is currently implemented as a single monolithic function that reads the physical memory map directly from hardware (via the device tree), making it impossible to write unit tests for the core image loading pipeline. The logic for placing kernel segments, generating the architecture-specific boot stub, updating the device tree, and handling initramfs needs to be testable in isolation.

Additionally, there are no utilities in the memory package for comparing segment collections by equality, which is needed to verify that a loading operation produces the expected physical memory layout.

## Expected Behavior

- The core image loading logic should be extracted into a function that accepts the memory map as an explicit parameter, allowing callers and tests to supply controlled memory layouts.
- The function must correctly handle loading a kernel image with or without an initramfs, setting up the device tree's boot configuration node, generating the small ARM64 boot stub, and returning an error that identifies which stage failed.
- Specific, distinguishable errors must be returned when: the device tree has no boot configuration node; there is not enough physical memory for the kernel segment; there is not enough memory for the initramfs segment; there is not enough memory for the device tree segment.
- The function must also handle input files that cannot be memory-mapped (such as pipes) by falling back to reading their content sequentially.
- New utility functions must allow comparing two memory segment collections for equality — both at the individual segment level and at the collection level — by checking that physical addresses and data contents match.

## Why This Matters

Without a testable, hardware-independent entry point into the ARM64 image loading pipeline, correctness of segment placement, device tree updates, boot stub generation, and error handling can only be verified on real hardware. Extracting this logic and adding comparison utilities makes the entire loading pipeline verifiable with automated tests on any machine.
