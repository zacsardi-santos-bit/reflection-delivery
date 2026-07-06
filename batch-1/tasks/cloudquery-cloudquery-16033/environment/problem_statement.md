## Description

When publishing plugins or addons, the documentation (README or other markdown files) often contains images stored locally alongside the source. Before the plugin/addon can be published to a registry, those local images need to be uploaded to a remote host and the local paths in the markdown replaced with the hosted URLs.

Currently, there is no utility to automatically find all locally-referenced images in a markdown document, regardless of the syntax style used (inline markdown, HTML image tags, reference-style links, file-protocol URLs), and replace them with remote URLs after upload.

## Expected Behavior

- Scan a markdown document for all local image references across all common syntax forms, including inline syntax with optional alt text and titles, HTML image tags (with either quote style and across multiple lines), reference-style links, and linked images.
- Group discovered image references by the image's content identity (so that the same image referenced multiple times can be uploaded once).
- Record precise byte-range positions for each reference so they can be substituted later.
- Ignore image references that appear inside code blocks or inline code spans.
- Ignore images that are already externally hosted (using http or https URLs); only process local file paths.
- Support file-protocol URLs and filenames containing special characters.
- Return an error if a referenced local image cannot be found on disk.
- After images are uploaded, replace all occurrences in the document with the corresponding remote URLs.
- Validate that discovered reference ranges do not overlap or contain invalid positions, returning an error if they do.

## Why This Matters

Plugin and addon publishing workflows need to produce self-contained documentation where all image assets are accessible remotely. Without this functionality, images embedded in documentation would become broken links after publishing, degrading the user experience for anyone reading the plugin's documentation in the registry.
