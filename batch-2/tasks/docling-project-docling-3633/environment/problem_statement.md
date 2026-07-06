## Description

When converting structured documents (PDFs, scanned reports, legal texts) that use numbered section headings, the parser currently treats all headings as the same level regardless of their numbering scheme or visual size. For example, a document that uses Roman numerals for top-level parts and Arabic numbers for subsections will lose this hierarchy in the output — both are treated as depth-1 headings. Similarly, deeply nested legal-style numbering (parts → sections → subsections → clauses) collapses entirely.

This makes the Markdown (and other structured format) output incorrect: all headings get the same depth marker, so the exported document has no meaningful outline structure.

## Expected Behavior

- Headings that use different numbering schemes (Roman vs. Arabic, keyword-prefixed parts, parenthetical letters, dotted decimals) should be assigned relative hierarchy levels that reflect their structural role in the document.
- When a document uses only one numbering scheme (e.g., dotted decimal), that scheme's outermost level should map to heading level 1 — no artificial depth offset should be added.
- Unnumbered headings (plain words like "Summary" or "Abstract") should not receive an inferred level from the numbering logic.
- When no numbering is present, the system should optionally fall back to visual cues such as heading size to determine relative level.
- A configurable maximum level should clamp any deeper headings to the specified maximum depth.
- The precedence of numbering schemes should be user-configurable so that, for example, Arabic numbering can be declared as outranking Roman numerals.
- After levels are assigned, structured export formats such as Markdown should correctly reflect the assigned depth (e.g., top-level headings render as first-level headers, second-level as second-level headers).

## Why This Matters

Documents that follow structured conventions — academic papers, legal contracts, technical reports — rely on heading hierarchy to communicate their outline. Without inferring this hierarchy automatically, users must manually relabel every heading after conversion, which defeats the purpose of automated document conversion.
