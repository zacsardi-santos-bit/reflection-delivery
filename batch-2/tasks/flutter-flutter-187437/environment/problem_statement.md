## Description

The release asset server for Flutter web apps currently serves any file it can resolve within the project root directory or the Flutter SDK root directory — without any restriction on the type of file being requested. This means that sensitive files in a typical Flutter project (such as environment configuration files containing API keys, or credential and signing files used for native platform builds) can be retrieved over HTTP just by knowing their path.

These roots are only supposed to serve source files needed for source-map debugging resolution. There is no legitimate reason for the server to expose unrelated project or SDK files over HTTP.

## Expected Behavior

- Requests for Dart source files from the project root and Flutter SDK root should continue to be served normally, since they may be needed for source-map resolution.
- Requests for any other file type from the project root or Flutter SDK root should fall through to the index.html fallback response rather than serving the actual file.
- Files in the web build output directory should remain unrestricted, since that directory only contains generated, publishable assets.

## Why This Matters

A developer running a Flutter web release build locally should not inadvertently expose sensitive project files to anyone who can reach the local server. Restricting the project and SDK roots to only source-map-relevant file types prevents accidental disclosure of secrets without breaking legitimate debugging use cases.
