## Description

When build artifacts are uploaded to cloud storage during CI runs, the files are stored without any HTTP content metadata. This means browsers and API clients cannot correctly interpret gzip-compressed log files — they are served as raw binary blobs rather than being transparently decompressed and displayed as readable text. Similarly, JSON result files and plain text logs lack `Content-Type` information, forcing clients to guess the format.

We need a way to automatically determine the appropriate HTTP metadata (content type and content encoding) for a file based purely on its name, so that uploaded artifacts are served with the correct headers.

## Expected Behavior

- A file with a double extension (e.g. a compressed text file) should be recognized as a compressed version of the inner type: the compression extension is stripped from the stored filename, and both the inner content type and a gzip encoding header are attached as metadata.
- A file ending with just a compression extension but with no recognized inner type should be stored with the compression suffix stripped from the filename, and only a generic compressed-archive content type — no content encoding header.
- A file whose entire name (with no dot) happens to be the compression suffix word should be left unchanged and tagged only with the generic compressed-archive content type.
- Files with standard extensions such as plain text or JSON should be stored with the correct content type and filename unchanged.
- The lookup must respect MIME type registrations made at runtime, so custom extension mappings are also honored.
- An empty filename or a filename consisting of only a dot should produce no metadata.

## Why This Matters

Without this metadata, developers inspecting CI logs in a browser must manually download and decompress files, significantly increasing debugging friction. Attaching the correct headers at upload time allows cloud storage to serve files in a browser-friendly way automatically.
