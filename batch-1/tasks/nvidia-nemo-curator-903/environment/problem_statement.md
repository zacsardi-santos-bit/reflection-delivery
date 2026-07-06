## Description

The video reading pipeline currently only supports files on the local filesystem. When a remote storage URL is provided (such as a cloud object storage bucket), the pipeline fails with an error instead of downloading and processing the video. There is no way to read video files stored remotely without pre-downloading them manually.

Additionally, there is no general mechanism to discover and partition files from remote storage into batches for distributed processing. A new partitioning component is needed that works transparently with both local and remote filesystems, supporting file extension filtering, count limits, and loading an explicit list of files from a JSON manifest.

## Expected Behavior

- A new filesystem-aware path utility should bundle a filesystem instance and a path together, providing open, string representation, and efficient parallel byte-range download capabilities.
- A utility function should be available to determine whether a given URL points to remote storage or the local filesystem.
- A new file partitioning stage should work with any storage backend, group files into tasks, filter by extension, apply a limit, and support loading paths from a JSON manifest.
- The video reader stage should be updated to handle the filesystem-aware path objects directly, preserving the original path representation without forcing conversion to a local-only path type.

## Why This Matters

Teams running large-scale video curation pipelines need to read input files directly from cloud storage without manual pre-download steps. This change enables end-to-end remote storage support in the video reading pipeline and provides a reusable abstraction for any future stage that needs to work with files across different storage backends.
