## Description

The utilities for parsing, writing, and escaping custom heading IDs in Markdown documents are currently bundled together with general-purpose Markdown processing utilities. This makes the code harder to navigate and maintain. These heading ID utilities should be extracted into their own dedicated module.

Beyond the refactoring, the heading ID writer currently only supports one style of heading ID — a bracket-based format that is technically invalid in newer versions of MDX. Projects that prioritize valid MDX syntax should be able to use a comment-based syntax instead. The writer should support both styles, and users should be able to choose which syntax to use when generating heading IDs for their documents.

## Expected Behavior

- The heading ID utilities (parsing, writing, escaping) are available from a new dedicated module, separate from the general markdown utilities.
- When writing heading IDs, users can choose between a classic bracket style and a comment-based MDX-compatible style.
- When migrating an existing document, the tool can convert all heading IDs from one syntax to the other while preserving the actual ID values.
- When both "migrate" and "overwrite" modes are requested simultaneously, the tool should report an error because these two modes are mutually exclusive.
- The heading ID parser correctly handles each syntax independently — recognizing only its own format and treating the other as plain text.

## Why This Matters

Many Docusaurus users are migrating to newer versions of MDX, where the classic bracket-style heading ID syntax is no longer valid. Having a supported way to generate and migrate to the comment-based syntax reduces friction for teams that want fully valid MDX documents without manual search-and-replace across their entire documentation tree.
