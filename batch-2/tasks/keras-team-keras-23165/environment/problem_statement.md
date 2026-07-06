## Description

The Keras model saving and loading pipeline has several security vulnerabilities and correctness bugs that need to be addressed. Maliciously crafted weight files can exploit the system in multiple ways: HDF5 files may embed link structures that redirect reads to arbitrary paths on disk, declare datasets with impossibly large shapes that would exhaust system memory, or contain virtual datasets that map to external sources. Similarly, compressed model archives can be constructed as decompression bombs — tiny files on disk that expand to gigabytes in memory — crashing or hanging the process that loads them.

Beyond file-level attacks, the deserializer will silently execute arbitrary serialized bytecode embedded in saved model configs (lambda functions, serialized native modules) without any user opt-in, which is a significant security risk when loading models from untrusted sources.

There are also two correctness bugs: the asset directory store does not validate paths for traversal sequences, allowing parent-directory references to escape the intended working directory; and reading from sharded weight files fails when layers are requested in a different order than they were written.

## Expected Behavior

- Loading an HDF5 weight file that contains external links, soft links, or virtual datasets must be refused with a clear error message identifying the link type.
- Loading an HDF5 weight file that contains a dataset claiming an impossibly large shape must be refused before any memory allocation is attempted.
- Loading a compressed model archive containing a decompression bomb must be refused before the member is expanded, both for the config entry and the weights entry.
- Deserializing a saved model config that contains embedded bytecode (e.g., a serialized lambda or a native module) must fail by default; users must explicitly opt in to allow such deserialization.
- The asset directory store must refuse to create or access paths that contain traversal sequences (including backslash variants), while still working correctly for normal nested relative paths and remote storage paths.
- Reading from a sharded weight store must work correctly regardless of the order in which layers are requested.
- Tar archive extraction must reject members that are routed to locations outside the base directory through chains involving in-bounds symlinks and parent-directory components. Hardlink entries must also be rejected if the entry's own name traverses outside the base directory.

## Why This Matters

Without these fixes, loading a model from an untrusted source (e.g., a file downloaded from the internet) can lead to arbitrary file reads, memory exhaustion, or execution of arbitrary code. Addressing these vulnerabilities is essential for Keras to be safely used in any environment where model files may not be fully trusted.
