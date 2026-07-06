## Description

The publishing system currently handles content transformations (like stripping member-only content) in an ad-hoc, scattered way. There's no reusable, configurable utility to apply common transformations such as removing member-only sections, escaping HTML, converting line breaks, or wrapping content in a paragraph element. Additionally, the publishing service only supports publishing full blog posts and has no way to publish standalone short-form notes.

## Expected Behavior

- A new content preparation utility should be introduced that supports the following configurable transformations, each independently opt-in:
  - Removing member-only content (content that follows a designated marker separating public and member-only sections)
  - Escaping HTML characters
  - Converting newline characters to HTML line break elements
  - Wrapping content in a paragraph element
- The publishing service should accept this content preparation utility as a dependency so it can be used during post publishing.
- The publishing service should gain a new method for publishing standalone short-form notes (not just full articles). This method should distribute the note to the author's followers via the ActivityPub protocol and return a structured result.
- All publishing operations should return a structured result indicating whether the content was published or skipped (e.g., because there was no publicly visible content), along with the resulting activity payload.

## Why This Matters

This makes content transformation reusable and testable in isolation, and extends the platform's publishing capabilities to include short-form notes — enabling more flexible content distribution for ActivityPub-connected Ghost sites.
