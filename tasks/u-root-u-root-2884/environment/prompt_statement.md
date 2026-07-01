I'm working on the ARM64 boot loading package and want to make the kernel image loading logic testable without real hardware. Right now, the main loading function reads the physical memory map directly from the system, which means we can't run unit tests for it at all.

I'd like to refactor so the core segment-building and device tree configuration logic lives in a separate function that accepts the memory map as an explicit input. That way, I can pass in a synthetic memory map and verify that the kernel, initramfs, device tree, and boot stub segments end up at the right physical addresses with the right contents.

The refactored function also needs proper error handling: it should return distinct, checkable errors depending on which stage fails — whether the device tree is missing its boot configuration node, whether the physical memory is too small for the kernel, the initramfs, or the device tree itself, or whether an input file simply can't be read. It also needs to gracefully handle input files that aren't seekable (like pipes), falling back to reading them entirely into memory.

On top of that, I need utilities in the memory package that can compare two segment collections for equality — both individual segments (same physical address range and same data) and entire collections — so I can assert that the loading operation produced the exact memory layout I expect.
