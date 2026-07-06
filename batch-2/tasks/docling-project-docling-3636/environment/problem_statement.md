## Description

When converting scientific journal articles from JATS/NXML format, grouped footnotes that appear within document sections are silently dropped from the output. This affects footnote groups that carry important article metadata such as competing interests declarations, author contribution descriptions, and ethics statements — all of which are standard elements in life sciences publications like eLife articles.

The parser currently encounters footnote-group elements in document sections (including back matter) but skips them entirely without extracting or preserving their content. As a result, the converted document is missing these sections entirely.

## Expected Behavior

- Footnote groups in JATS document sections should be parsed and their text content preserved in the output
- Each footnote's text should appear as a list item under an appropriate heading in the converted document
- The heading for the footnote section should come from the group's own title element when present, or fall back to a default heading when absent
- Empty footnote groups (containing no individual footnotes) should be skipped gracefully without errors
- When exported to Markdown, footnote group content must be visible as structured content
- For articles with author-notes sections (e.g. competing interests, author contributions, ethics statements), these sections must be present and populated in the converted output

## Why This Matters

Academic publishers encode important article metadata as footnote groups in the document body. The current behavior silently drops all of this content, making converted documents incomplete. Users who rely on document conversion to process scientific literature lose crucial metadata about the article — including disclosures that may be legally or editorially required.
