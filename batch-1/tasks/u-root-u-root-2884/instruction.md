Refactor the ARM64 boot image loading logic to make it testable without hardware dependencies. Implement a new function that accepts a memory map as input and handles kernel image loading, including error handling and segment allocation. Additionally, create utility functions for comparing memory segment collections.

Requirements:

* Implement `kexecLoadImageMM` function in `pkg/boot/linux/load_linux_image.go`:
    * Accept parameters: `kexec.MemoryMap`, `*os.File` for kernel, optional `*os.File` for ramfs, `*dt.FDT`, `cmdline` string, and `KexecOptions`.
    * Return `*kimage` on success or a non-nil error on failure.
    * Ensure `kimage.entry` equals the physical start address of the trampoline segment.
    * Ensure `kimage.segments` contains all allocated segments in physical-address ascending order.
    * Handle both scenarios: with and without ramfs, adjusting segment count and order accordingly.
    * Ensure the trampoline segment is exactly 40 bytes with specified ARM64 instruction sequence and addresses.
    * Align the kernel segment to a 2 MB boundary, offset by `text_offset`.
    * Sanitize and update the FDT's 'chosen' node properties as specified.
    * Return specific errors for missing 'chosen' node, insufficient space for segments, and unreadable files.
    * Handle non-seekable kernel files by reading content sequentially.

* Implement utility functions in `pkg/boot/kexec/memory_linux.go`:
    * `SegmentsEqual(s, t Segments) bool`: Return true if both segment collections are identical in length and content.
    * `SegmentEqual(s, t Segment) bool`: Return true if both segments have the same physical range and identical buffer contents.

* Ensure error handling distinguishes between stages of failure, returning errors like `errNoChosenNode`, `errKernelSegmentFailed`, `errInitramfsSegmentFailed`, `errDTBSegmentFailed`, and `os.ErrClosed` as appropriate.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.