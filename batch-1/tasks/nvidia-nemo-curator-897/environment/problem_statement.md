# Add a clip writer stage and file-writing utilities for the video curation pipeline

## Description

The video curation pipeline is missing a stage that persists processed clip data to storage. After clips are decoded, filtered, captioned, and embedded, there is no standard component that writes the resulting MP4 files, thumbnail previews, embedding vectors, captions, and metadata JSON to disk. Without this, pipeline output cannot be stored or inspected reliably.

In addition, there are no shared utility functions for writing binary data, JSON, Parquet, or CSV files in a consistent way — with proper logging, file-existence policies, directory creation, and support for serializing non-standard data types (such as UUIDs).

## Expected Behavior

- A pipeline stage should exist that accepts processed video tasks and writes all associated outputs to configurable paths, using parallel I/O for throughput.
- The stage should support a dry-run mode that skips all file writes without changing any other behavior.
- When a clip has a video buffer, it should be written as an MP4 file; clips without a buffer should log a warning and still count toward statistics.
- Thumbnail previews, embedding vectors (for each supported embedding algorithm), and metadata should be written per clip.
- Filtered clips should be written to a separate output path and should not be counted in the "passed" statistics.
- When writing video-level metadata, the first chunk should produce both a video summary file and a clip chunk index file; subsequent chunks should produce only the clip chunk index file.
- After writing, all in-memory buffers (video bytes, embeddings, caption data, thumbnails) should be cleared from each clip to free memory.
- File writer utilities should skip writing if a file already exists, optionally overwrite it, and raise an error for unsupported modes. Verbose logging should emit a message when a write begins.

## Why This Matters

Without this stage, users have no way to save the output of video curation to disk. The file utilities also provide the foundation for all storage operations in the pipeline, ensuring consistent behavior around file conflicts and logging across all output types.
