I'm working on a Docusaurus utility package that handles custom heading IDs in Markdown files.

*   The functions parseMarkdownHeadingId, escapeMarkdownHeadingIds, and writeMarkdownHeadingId must be moved out of markdownUtils.ts and into a new dedicated module at packages/docusaurus-utils/src/markdownHeadingIdUtils.ts, exported from that file.

*   parseMarkdownHeadingId must accept a second parameter `syntax` of type HeadingIdSyntax ('classic' | 'mdx-comment'), defaulting to 'classic'. It must return { text: string; id: string | undefined }, where text is the heading with the ID suffix removed and id is the parsed ID or undefined.

*   When syntax is 'classic', parseMarkdownHeadingId must parse the `{#id}` pattern only when it appears at the end of the heading. The pattern must not be recognized when it appears in the middle. Empty IDs (`{#}`) must return id: undefined. The classic syntax parser must NOT parse mdx-comment style patterns (`{/* ... */}`).

*   When syntax is 'mdx-comment', parseMarkdownHeadingId must parse `{/* #id */}` only at the end of the heading. Whitespace around the id inside the comment is trimmed. IDs with internal spaces are not parsed (id: undefined). Missing hash prefix results in id: undefined. Empty IDs (`{/* # */}`) return id: undefined. The mdx-comment parser must NOT parse classic `{#id}` patterns.

*   writeMarkdownHeadingId must accept a `syntax` option (HeadingIdSyntax, defaults to 'classic'). When syntax is 'classic', generated IDs use the format `{#slug}`. When syntax is 'mdx-comment', generated IDs use the format `{/* #slug */}`.

*   writeMarkdownHeadingId must accept a `migrate` boolean option. When migrate is true, existing heading IDs (in any syntax) are preserved but rewritten using the target syntax. IDs already in the target syntax are kept as-is.

*   writeMarkdownHeadingId must accept an `overwrite` boolean option. When overwrite is true, existing IDs (in any syntax) are discarded and new IDs are generated from the heading text using the target syntax.

*   When both `overwrite` and `migrate` options are true, writeMarkdownHeadingId must throw an Error with the exact message: "Heading ids can either be overwritten or migrated, not both at the same time".

*   writeMarkdownHeadingId must skip h1 headings (lines starting with exactly one `#`) — they must not receive generated IDs. Headings inside code blocks (between triple-backtick fences) must also be skipped.

*   writeMarkdownHeadingId must accept a `maintainCase` boolean option. When true, the generated slug preserves the original casing of the heading text instead of lowercasing it.

*   writeMarkdownHeadingId must deduplicate generated slugs across the document by appending numeric suffixes (-1, -2, etc.) when the same slug would otherwise appear more than once. Existing IDs (when not overwriting) are pre-registered in the slugger to prevent collisions.

*   escapeMarkdownHeadingIds must escape `{#id}` occurrences on h1–h6 heading lines by prepending a backslash (`\{#id}`). Already-escaped patterns (`\{#id}`) must not be double-escaped. Lines with 7 or more leading `#` characters and non-heading lines must not be modified.


*   Interface details: Type: Function
Name: parseMarkdownHeadingId
Location: packages/docusaurus-utils/src/markdownHeadingIdUtils.ts
Signature: parseMarkdownHeadingId(heading: string, syntax: HeadingIdSyntax = 'classic'): { text: string; id: string | undefined }
Description: Parses a custom heading ID from a markdown heading string. Returns an object with `text` (the heading without the ID suffix) and `id` (the parsed ID, or undefined if not found). The `syntax` parameter determines which format to recognize: `'classic'` recognizes `{#id}` at the end of the heading; `'mdx-comment'` recognizes `{/* #id */}` at the end of the heading. Each syntax only recognizes its own format — classic does not parse mdx-comment patterns, and vice versa.

Type: Function
Name: escapeMarkdownHeadingIds
Location: packages/docusaurus-utils/src/markdownHeadingIdUtils.ts
Signature: escapeMarkdownHeadingIds(content: string): string
Description: Takes a full markdown document string and returns a copy where `{#id}` patterns on heading lines (h1–h6 only) are escaped with a backslash prefix (`\{#id}`). Already-escaped patterns are not double-escaped. Level-7+ headings and non-heading lines are not modified.

Type: Function
Name: writeMarkdownHeadingId
Location: packages/docusaurus-utils/src/markdownHeadingIdUtils.ts
Signature: writeMarkdownHeadingId(content: string, options?: WriteHeadingIDOptions): string
Description: Takes markdown content and returns it with heading IDs written. h1 headings are ignored. Code blocks are skipped. Duplicate slugs are deduplicated with numeric suffixes (-1, -2, etc.). When both `migrate` and `overwrite` are true, throws an error with the exact message: "Heading ids can either be overwritten or migrated, not both at the same time".

Type: TypeAlias
Name: HeadingIdSyntax
Location: packages/docusaurus-utils/src/markdownHeadingIdUtils.ts
Signature: type HeadingIdSyntax = 'classic' | 'mdx-comment'
Description: Union type representing the two supported heading ID syntaxes. `'classic'` uses `{#id}` format; `'mdx-comment'` uses `{/* #id */}` format.

Type: TypeAlias
Name: WriteHeadingIDOptions
Location: packages/docusaurus-utils/src/markdownHeadingIdUtils.ts
Signature: type WriteHeadingIDOptions = { syntax?: HeadingIdSyntax; migrate?: boolean; overwrite?: boolean; maintainCase?: boolean }
Description: Options for writeMarkdownHeadingId. `syntax` controls the output format (defaults to 'classic'). `migrate` converts existing IDs from any syntax into the target syntax while preserving their IDs. `overwrite` discards existing IDs and regenerates them from the heading text. `maintainCase` preserves casing in generated slugs.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.